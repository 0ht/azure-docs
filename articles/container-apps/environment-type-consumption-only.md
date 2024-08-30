---
title: Consumption-only environment type in Azure Container Apps
description: Learn about features and billing considerations for Consumption-only environment types in Azure Container Apps. 
services: container-apps
author: craigshoemaker
ms.service: azure-container-apps
ms.topic: conceptual
ms.date: 08/30/2024
ms.author: cshoe
---

# Consumption-only environment type in Azure Container Apps

In Azure Container Apps, the environment type determines functionality and billing methods associated with your applications.

The Consumption-only environment runs your application using compute resources exclusively allocated on-demand. You only pay for resources consumed by your application.

This article explains features of the *Consumption-only (v1)* environment type. For more information on the default *Workload profiles (v2)* environment type, see [Compute and billing structures in Azure Container Apps](structure.md).

While Consumption-only environments are an option, for new Container Apps environments that need the consumption model, creating a *Workload profiles v2* environment with the built-in [consumption workload profile](./structure.md#workload-profiles) is recommended.

The Consumption-only environment type works on the consumption plan. Apps running in the The Consumption-only have access to 4 vCPUs with 8GB of memory. There is no access to GPUs in a Consumption-only environment.

If your application requires any capability outside these parameters, then run your apps on dedicated [workload profile](structure.md).

*Consumption-only (v1)* environment run exclusively on the consumption plan, which bills as your application consumes resources.

## FAQ

### Does the consumption plan work the same way in a Workload profiles (v2) environment vs. a Consumption-only (v1) environment?

No. There are some distinctions between how the consumption plan operates among the workload profiles and consumption-only plans. Some networking features are different in a workload profiles environment. For instance, user defined routes (UDR) are only available in a workload profiles environment, and subnet sizes differ, and IP addresses are assigned differently depending on the environment type.

### I need consumption pricing. Should I use a Consumption-only (v1) environment, or a Workload profiles (v2) environment with the consumption profile?

If you need the features of a consumption model, and you're creating a new Azure Container Apps environment, use the Workload profiles (v2) environment with the consumption profile. Using this approach gives you the flexibility to add dedicated resources to your environment should you need them in the future.

## Related content

- [Environments](environment.md)
- [Plans](plans.md)
- [Workload profiles](workload-profiles-overview.md)