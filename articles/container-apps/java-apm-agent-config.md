---
title: "Tutorial: Configure APM integration for Java applications with Java agent and init-container"
description: Learn to configure APM integration for Java applications with Java agent and init-container
services: container-apps
author: croffz
ms.service: azure-container-apps
ms.custom: devx-track-azurecli
ms.topic: tutorial
ms.date: 09/26/2024
ms.author: kuzhong
---

# Tutorial: Configure APM integration for Java applications with Java agent and init-container

APM (Application Performance Management) is useful when it comes to observability for your applications running online. You can package the APM plugin in the same image or Dockerfile with your app, but it binds the management efforts together, like release and CVE (Common Vulnerabilities and Exposures) mitigation. Alternatively, you can apply Java agent and init containers in Azure Container Apps to inject APM solutions without modifying your app image.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Prepare an image to set up Java agent and push to Azure Container Registry
> * Create a Container Apps environment and a Container App as the target Java app
> * Configure init containers and volume mounts to set up Application Insights integration

## Prerequisites

- Have an instance of [Application Insights](/azure/azure-monitor/app/app-insights-overview)
- Have an instance of Azure Container Registry or other container image registries
- Install [Docker](https://www.docker.com/) to build image
- Install the latest version of the [Azure CLI](/cli/azure/install-azure-cli)

## Set up the environment

The following commands help you define variables and ensure your Container Apps extension is up to date.

1. Sign in to the Azure CLI.

    # [Bash](#tab/bash)

    ```azurecli
    az login
    ```

    # [PowerShell](#tab/powershell)

    ```azurecli
    az login
    ```

1. Ensure you have the latest version of Azure CLI extensions for Container Apps and Application Insights.

    # [Bash](#tab/bash)

    ```azurecli
    az extension add -n containerapp --upgrade
    az extension add -n application-insights --upgrade
    ```

    # [PowerShell](#tab/powershell)

    ```azurecli
    az extension add -n containerapp --upgrade
    az extension add -n application-insights --upgrade
    ```

1. Set up environment variables used in various commands to follow.

    # [Bash](#tab/bash)

    ```bash
    APP_INSIGHTS_RESOURCE_ID="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/microsoft.insights/components/my-app-insights"
    CONTAINER_REGISTRY_NAME="myacr"
    RESOURCE_GROUP="my-resource-group"
    ENVIRONMENT_NAME="my-environment"
    CONTAINER_APP_NAME="my-container-app"
    LOCATION="eastus"
    ```

    # [PowerShell](#tab/powershell)

    ```powershell
    $APP_INSIGHTS_RESOURCE_ID="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/microsoft.insights/components/my-app-insights"
    $CONTAINER_REGISTRY_NAME="myacr"
    $RESOURCE_GROUP="my-resource-group"
    $ENVIRONMENT_NAME="my-environment"
    $CONTAINER_APP_NAME="my-container-app"
    $LOCATION="eastus"
    ```

1. Retrieve the connection string of Application Insights.

    # [Bash](#tab/bash)

    ```azurecli
    CONNECTION_STRING=$(az monitor app-insights component show \
      --ids $APP_INSIGHTS_RESOURCE_ID \
      --query connectionString)
    ```

    # [PowerShell](#tab/powershell)

    ```azurecli
    $CONNECTION_STRING=(az monitor app-insights component show `
      --ids $APP_INSIGHTS_RESOURCE_ID `
      --query connectionString)
    ```

## Prepare an image to set up Java agent and push to Azure Container Registry

> [!TIP]
> You can skip this step and use `croff.azurecr.io/app-insights-java-agent-setup:3.5.4` as the init-container image.

1. Build setup image for Application Insights Java agent.

    Save the Dockerfile along with the setup script, and run `docker build` in the same directory.
    
    ```Dockerfile
    FROM mcr.microsoft.com/cbl-mariner/base/core:2.0
    
    ARG version="3.5.4"
    
    RUN tdnf update -y && tdnf install -y curl ca-certificates
    
    RUN curl -L "https://github.com/microsoft/ApplicationInsights-Java/releases/download/${version}/applicationinsights-agent-${version}.jar" > agent.jar
    
    ADD setup.sh /setup.sh
    
    ENTRYPOINT ["/bin/sh", "setup.sh"]
    ```

    ---

    ```setup.sh
    #!/bin/sh

    if [[ -z "$CONNECTION_STRING" ]]; then
      echo "Environment variable CONNECTION_STRING is not found. Exiting..."
      exit 1
    else
      echo "{\"connectionString\": \"$CONNECTION_STRING\"}" > /java-agent/applicationinsights.json
      cp agent.jar /java-agent/agent.jar
    fi
    ```

    ---

    # [Bash](#tab/bash)

    ```azurecli
    docker build . -t "$CONTAINER_REGISTRY_NAME.azurecr.io/samples/java-agent-setup:1.0.0"
    ```

    # [PowerShell](#tab/powershell)

    ```powershell
    docker build . -t "$CONTAINER_REGISTRY_NAME.azurecr.io/samples/java-agent-setup:1.0.0"
    ```

1. Push the image to Azure Container Registry or other container image registries.
    
    # [Bash](#tab/bash)

    ```azurecli
    az acr login --name $CONTAINER_REGISTRY_NAME
    docker push "$CONTAINER_REGISTRY_NAME.azurecr.io/samples/java-agent-setup:1.0.0"
    ```

    # [PowerShell](#tab/powershell)

    ```powershell
    az acr login --name $CONTAINER_REGISTRY_NAME
    docker push "$CONTAINER_REGISTRY_NAME.azurecr.io/samples/java-agent-setup:1.0.0"
    ```

## Create a Container Apps environment and a Container App as the target Java app

1. Create a Container Apps environment.

    # [Bash](#tab/bash)

    ```azurecli
    az containerapp env create \
      --name $ENVIRONMENT_NAME \
      --resource-group $RESOURCE_GROUP \
      --location "$LOCATION" \
      --query "properties.provisioningState"
    ```

    # [PowerShell](#tab/powershell)

    ```azurecli
    az containerapp env create `
      --name $ENVIRONMENT_NAME `
      --resource-group $RESOURCE_GROUP `
      --location "$LOCATION" `
      --query "properties.provisioningState"
    ```

  Once created, the command returns a "Succeeded" message.

1. Create a Container app for further configurations.

    # [Bash](#tab/bash)

    ```azurecli
    az containerapp create \
      --name $CONTAINER_APP_NAME \
      --environment $ENVIRONMENT_NAME \
      --resource-group $RESOURCE_GROUP \
      --query "properties.provisioningState"
    ```

    # [PowerShell](#tab/powershell)

    ```azurecli
    az containerapp create `
      --name $CONTAINER_APP_NAME `
      --environment $ENVIRONMENT_NAME `
      --resource-group $RESOURCE_GROUP `
      --query "properties.provisioningState"
    ```

  Once created, the command returns a "Succeeded" message.

## Configure init-container, environment variables, and volumes to set up Application Insights integration

1. Get current configurations of the running Container App.

    # [Bash](#tab/bash)

    ```azurecli
    az containerapp show \
      --name $CONTAINER_APP_NAME \
      --resource-group $RESOURCE_GROUP \
      -o yaml > app.yaml
    ```

    # [PowerShell](#tab/powershell)

    ```azurecli
    az containerapp show `
      --name $CONTAINER_APP_NAME `
      --resource-group $RESOURCE_GROUP `
      -o yaml > app.yaml
    ```

  The YAML file `app.yaml` is created in current directory.

1. Edit the app YAML file.

    - Add secret for Application Insights connection string

    ```yaml
    properties:
      configuration:
        secrets:
        - name: app-insights-connection-string
          value: $CONNECTION_STRING
    ```

    Replace $CONNECTION_STRING with your Azure Application Insights connection string.

    - Add ephemeral storage volume for Java agent files
  
    ```yaml
    properties:
      template:
        volumes:
        - name: java-agent-volume
          storageType: EmptyDir
    ```

    - Add init-container with volume mounts and environment variables
  
    ```yaml
    properties:
      template:
        initContainers:
        - image: $CONTAINER_REGISTRY_NAME.azurecr.io/samples/java-agent-setup:1.0.0
          name: java-agent-setup
          resources:
            cpu: 0.25
            memory: 0.5Gi
          env:
          - name: CONNECTION_STRING
            secretRef: app-insights-connection-string
          volumeMounts:
          - mountPath: /java-agent
            volumeName: java-agent-volume
    ```

    Replace $CONTAINER_REGISTRY_NAME with your Azure Container Registry name or replace the image from other container image registries.

    - Update app container with volume mounts and environment variables
  
    ```yaml
    properties:
      template:
        containers:
        - name: test-java-app
          image: mcr.microsoft.com/azurespringapps/samples/hello-world:0.0.1
          resources:
            cpu: 0.5
            memory: 1Gi
          env:
          - name: JAVA_TOOL_OPTIONS
            value: -javaagent:/java-agent/agent.jar
          volumeMounts:
          - mountPath: /java-agent
            volumeName: java-agent-volume
    ```

1. Update the Container App with modified YAML file.

    # [Bash](#tab/bash)

    ```azurecli
    az containerapp update \
      --name $CONTAINER_APP_NAME \
      --resource-group $RESOURCE_GROUP \ 
      --yaml app.yaml \
      --query "properties.provisioningState"
    ```

    # [PowerShell](#tab/powershell)

    ```azurecli
    az containerapp update `
      --name $CONTAINER_APP_NAME `
      --resource-group $RESOURCE_GROUP `
      --yaml app.yaml `
      --query "properties.provisioningState"
    ```

  Once updated, the command returns a "Succeeded" message. Then you can check out your Application Insights in Azure portal to see your Container App is connected.

## Clean up resources

The resources created in this tutorial contribute to your Azure bill. If you aren't going to keep them in a long run, run the following commands to clean them up.

# [Bash](#tab/bash)
```azurecli
az group delete --resource-group $RESOURCE_GROUP
```
# [PowerShell](#tab/powershell)
```azurecli
az group delete --resource-group $RESOURCE_GROUP
```

## Other APM solutions

Other than [Azure Application Insights](/azure/azure-monitor/app/java-standalone-config), there are other popular APM solutions in the community. If you want to integrate your Azure Container App with other APM providers, just replace the Java agent JAR and related config files.

- [AppDynamics](https://docs.appdynamics.com/appd/21.x/21.4/en/application-monitoring/install-app-server-agents/java-agent/install-the-java-agent)
- [Dynatrace](https://docs.dynatrace.com/docs/setup-and-configuration/technology-support/application-software/java)
- [Elastic](https://www.elastic.co/guide/en/apm/agent/java/index.html)
- [NewRelic](https://docs.newrelic.com/docs/apm/agents/java-agent/getting-started/introduction-new-relic-java/)
