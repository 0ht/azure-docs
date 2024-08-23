---
title: "Quickstart: Send telemetry from your assets to the cloud"
description: "Quickstart: Use a dataflow to send asset telemetry from the MQTT broker to an event hub in the cloud."
author: dominicbetts
ms.author: dobett
ms.topic: quickstart
ms.subservice: azure-data-processor
ms.custom:
  - ignite-2023
ms.date: 04/19/2024

#CustomerIntent: As an OT user, I want to send my OPC UA data to the cloud so that I can derive insights from it by using a tool such as Real-Time Dashboards.
---

# Quickstart: Send asset telemetry to the cloud using a dataflow

[!INCLUDE [public-preview-note](../includes/public-preview-note.md)]

In this quickstart, you use a dataflow to forward messages from the MQTT broker to an event hub in the Azure Event Hubs service. The event hub can deliver the data to other cloud services for storage and analysis. In the next quickstart, you use a Real-Time Dashboard to visualize the data.

## Prerequisites

Before you begin this quickstart, you must complete the following quickstarts:

- [Quickstart: Run Azure IoT Operations Preview in GitHub Codespaces with K3s](quickstart-deploy.md)
- [Quickstart: Add OPC UA assets to your Azure IoT Operations Preview cluster](quickstart-add-assets.md)

## What problem will we solve?

To use a tool such as Real-Time Dashboard to analyze your OPC UA data, you need to send the data to a cloud service such as Azure Event Hubs. A dataflow can subscribe to an MQTT topic and forward the messages to an event hub in your Azure Event Hubs namespace. The next quickstart shows you how to use Real-Time Dashboards to visualize and analyze your data.

## Set your environment variables

If you're using the Codespaces environment, the required environment variables are already set and you can skip this step. Otherwise, set the following environment variables in your shell:

# [Bash](#tab/bash)

```bash
# The name of the resource group where your Kubernetes cluster is deployed
export RESOURCE_GROUP=<resource-group-name>

# The name of your Kubernetes cluster
export CLUSTER_NAME=<kubernetes-cluster-name>
```

# [PowerShell](#tab/powershell)

```powershell
# The name of the resource group where your Kubernetes cluster is deployed
$RESOURCE_GROUP = "<resource-group-name>"

# The name of your Kubernetes cluster
$CLUSTER_NAME = "<kubernetes-cluster-name>"
```

---

## Create an Event Hubs namespace

To create an Event Hubs namespace and an event hub, run the following Azure CLI commands in your shell. These commands create the Event Hubs namespace in the same resource group as your Kubernetes cluster:

# [Bash](#tab/bash)

```bash
az eventhubs namespace create --name ${CLUSTER_NAME:0:24} --resource-group $RESOURCE_GROUP

az eventhubs eventhub create --name destinationeh --resource-group $RESOURCE_GROUP --namespace-name ${CLUSTER_NAME:0:24} --retention-time 1 --partition-count 1 --cleanup-policy Delete
```

# [PowerShell](#tab/powershell)

```powershell
az eventhubs namespace create --name $CLUSTER_NAME.Substring(0, 24) --resource-group $RESOURCE_GROUP

az eventhubs eventhub create --name destinationeh --resource-group $RESOURCE_GROUP --namespace-name $CLUSTER_NAME.Substring(0, 24) --retention-time 1 --partition-count 1 --cleanup-policy Delete
```

---

To grant the Azure IoT Operations extension in your cluster access to your Event Hubs namespace, run the following Azure CLI commands:

# [Bash](#tab/bash)

```bash
EVENTHUBRESOURCE=$(az eventhubs namespace show --resource-group $RESOURCE_GROUP --namespace-name ${CLUSTER_NAME:0:24} --query id -o tsv)

PRINCIPAL=$(az k8s-extension list --resource-group $RESOURCE_GROUP --cluster-name $CLUSTER_NAME --cluster-type connectedClusters -o tsv --query "[?extensionType=='microsoft.iotoperations'].identity.principalId")

az role assignment create --role "Azure Event Hubs Data Sender" --assignee $PRINCIPAL --scope $EVENTHUBRESOURCE
```

# [PowerShell](#tab/powershell)

```powershell
$EVENTHUBRESOURCE = $(az eventhubs namespace show --resource-group $RESOURCE_GROUP --namespace-name $CLUSTER_NAME.Substring(0, 24) --query id -o tsv)

$PRINCIPAL = $(az k8s-extension list --resource-group $RESOURCE_GROUP --cluster-name $CLUSTER_NAME --cluster-type connectedClusters -o tsv --query "[?extensionType=='microsoft.iotoperations'].identity.principalId")

az role assignment create --role "Azure Event Hubs Data Sender" --assignee $PRINCIPAL --scope $EVENTHUBRESOURCE
```

---

## Create a dataflow to send telemetry to an event hub

To create and configure a dataflow in your cluster, run the following commands in your shell. This dataflow forwards messages from the MQTT topic to the event hub you created without making any changes:

# [Bash](#tab/bash)

```bash
wget https://raw.githubusercontent.com/Azure-Samples/explore-iot-operations/main/samples/quickstarts/dataflow.yaml
sed -i 's/<NAMESPACE>/'"${CLUSTER_NAME:0:24}"'/' dataflow.yaml

kubectl apply -f dataflow.yaml
```

# [PowerShell](#tab/powershell)

```powershell
Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure-Samples/explore-iot-operations/main/samples/quickstarts/dataflow.yaml -OutFile dataflow.yaml

(Get-Content dataflow.yaml) | ForEach-Object { $_ -replace '<NAMESPACE>', $CLUSTER_NAME.Substring(0, 24) } | Set-Content dataflow.yaml

kubectl apply -f dataflow.yaml
```


---

## How did we solve the problem?

In this quickstart, you used a dataflow to connect an MQTT topic to an event hub in your Azure Event Hubs namespace. In the next quickstart, you use Microsoft Fabric Real-Time Intelligence to visualize the data.

## Clean up resources

If you're continuing on to the next quickstart, keep all of your resources.

[!INCLUDE [tidy-resources](../includes/tidy-resources.md)]

> [!NOTE]
> The resource group contains the Event Hubs namespace you created in this quickstart.

## Next step

[Quickstart: Get insights from your asset telemetry](quickstart-get-insights.md)
