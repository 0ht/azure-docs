---
title: Configure data boundary
description: Learn how to configure EUDB.
ms.topic: how-to
ms.date: 10/16/2024
ms.custom: devx-track-azurepowershell, devx-track-azurecli
# Customer intent: As an Azure user, I want to create a new data boundary.
---

# Configure data boundary

Currently, the only supported data boundary configuration is for the European Union. The EU (European Union) Data Boundary is a geographically defined boundary within which Microsoft has committed to store and process personal data for Microsoft enterprise online services, including Azure, Dynamics 365, Power Platform, and Microsoft 365, subject to limited circumstances where personal data continue to be transferred outside the EU Data Boundary. For more information, see [Overview of the EU Data Boundary](/privacy/eudb/eu-data-boundary-learn).

Azure Resource Manager is the deployment and management service for Azure. To provide maximum availability and performance, Azure Resource Manager was architected to distribute all data it stores and processes globally across the Azure cloud. As part of the EU Data Boundary and Microsoft's regional data residency commitments, Azure Resource Manager has been rearchitected to allow Customer Data and pseudonymized personal data to be stored and processed regionally. This documentation provides details on how customers can configure Azure Resource Manager for use in the EU Data Boundary.

A data boundary can only be established in new tenants that have no existing subscriptions or deployed resources. Once in place, the data boundary configuration cannot be removed or modified, and existing subscriptions and resources cannot be moved into or out of a tenant with a data boundary. Each tenant is limited to one data boundary, and after it is established, Azure Resource Manager will restrict resource deployments to regions within that boundary. Customers can opt their tenants into a data boundary by deploying a `Microsoft.Resources/dataBoundaries` resource at the tenant level.

The `DataBoundaryTenantAdministrator` built-in role is required to configure data boundary. For more information, see [Assign Azure roles](../../role-based-access-control/role-assignments-portal.yml).

To opt your tenant into an Azure EU Data Boundary:

- Create a new tenant within an EU country to configure an Entra EU Data Boundary.
- Before creating any new subscriptions or resources, deploy a `Microsoft.Resources/dataBoundaries` resource with an EU configuration.
- Create a subscription and deploy Azure resources.  

(The CLI PR: https://github.com/Azure/azure-cli/pull/29961/file)
(The PowerShell PR: https://github.com/Azure/azure-powershell/pull/26158)
(The RESTAPI PR: https://github.com/Azure/azure-rest-api-specs/pull/29950)

## Create data boundary

To opt-in a tenant to data boundary.

# [Portal](#tab/azure-portal)

(Pending Joy Shah)

# [Azure CLI](#tab/azure-cli)

```azurecli
az data-boundary create --data-boundary EU
```

For more information, see [Azure CLI Reference](/cli/azure/reference-index).

# [PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzDataBoundary -DataBoundary DataBoundaryName
```

For more information, see [Azure PowerShell Reference](/powershell/module/az.resources).

# [REST API](#tab/rest-api)

```http
PUT https://management.azure.com/providers/ Microsoft.Resources/dataBoundaries/default?api-version=2024-08-01 
```

Request body:

```json
{ 
  "properties": { 
    "dataBoundary": "EU" 
  } 
} 
```

```Response body:

```json
{ 
  "name": "{tenantId}", 
  "id": " /providers/Microsoft.Resources/dataBoundaries/{tenantId}",   
  "properties": { 
    "dataBoundary": "EU", 
    "provisioningState": "Created" 
  } 
} 
```

For more information, see [Azure REST API Reference](/rest/api/azure/).

---

## Read data boundary

To get data boundary.

# [Portal](#tab/azure-portal)

(Pending Joy Shah)

# [Azure CLI](#tab/azure-cli)

To get data boundary at specified scope:

```azurecli
az data-boundary show --scope scopePath
```

Get data boundary of tenant:

```azurecli
az data-boundary show-tenant 
```

For more information, see [Azure CLI Reference](/cli/azure/reference-index).

# [PowerShell](#tab/azure-powershell)

To get data boundary at specified scope:

```azurepowershell
Get-AzDataBoundaryScope -Scope ScopePath
```

Get data boundary of tenant:

```azurepowershell
Get-AzDataBoundaryTenant
```

For more information, see [Azure PowerShell Reference](/powershell/module/az.resources).

# [REST API](#tab/rest-api)

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Resources/dataBoundaries/default?api-version=2024-08-01 
```

Response body:

```json
{ 
  "name": "{tenantId}", 
  "id": " /providers/Microsoft.Resources/dataBoundaries/{tenantId}",   
  "properties": { 
    "dataBoundary": "EU", 
    "provisioningState": "Created" 
  } 
} 
```

For more information, see [Azure REST API Reference](/rest/api/azure/).

---

## Next steps

- To learn about creating Resource Manager templates, see [Authoring Azure Resource Manager templates](../templates/syntax.md).
- To view the resource provider template schemas, see [Template reference](/azure/templates/).
