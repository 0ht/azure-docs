---
title: Use managed identities with the Deidentification service in Azure Health Data Services
description: Learn how to use managed identities with the Azure Health Data Services Deidentification service using the Azure portal and ARM template.
author: jovinson-ms
ms.author: jovinson
ms.service: healthcare-apis
ms.subservice: workspace
ms.topic: how-to
ms.date: 07/17/2024
---

# Use managed identities with the Deidentification service

Managed identities provide Azure services with a secure, automatically managed identity in Microsoft Entra ID. Using managed identities eliminates the need for developers having to manage credentials by providing an identity. There are two types of managed identities: system-assigned and user-assigned. The Deidentification service supports both.

Managed identities can be used to grant the Deidentification service access to your storage account for batch processing. In this article, you learn how to assign a managed identity to your Deidentification service.

## Prerequisites

- Understand the differences between **system-assigned** and **user-assigned** described in [What are managed identities for Azure resources?](/entra/identity/managed-identities-azure-resources/overview)
- A Deidentification service in your Azure subscription. If you don't have a Deidentification service, follow the steps in [Quickstart: Deploy the Deidentification service](quickstart.md).

## Create an instance of the Deidentification service in Azure Health Data Services with a system-assigned managed identity

# [Azure portal](#tab/portal)

1. Access the Deidentification service settings in the Azure portal under the **Security** group in the left navigation pane.
1. Select **Identity**.
1. Within the **System assigned** tab, switch **Status** to **On** and choose **Save**.

# [ARM template](#tab/azure-resource-manager)

Any resource of type ``Microsoft.HealthDataAIServices/deidServices`` can be created with a system-assigned identity by including the following block in 
the resource definition:

```json
"identity": {
    "type": "SystemAssigned"
}
```

---

## Assign a user-assigned managed identity to a service instance

# [Azure portal](#tab/portal)

1. Create a user-assigned managed identity resource according to [these instructions](/entra/identity/managed-identities-azure-resources/how-manage-user-assigned-managed-identities).
1. In the navigation pane of your Deidentification service, scroll to the **Security** group.
1. Select **Identity**.
1. Select the **User assigned** tab, and then choose **Add**.
1. Search for the identity you created, select it, and then choose **Add**.

# [ARM template](#tab/azure-resource-manager)

Any resource of type ``Microsoft.HealthDataAIServices/deidServices`` can be created with a user-assigned identity by including the following block in
the resource definition, replacing **resource-id** with the Azure Resource Manager (ARM) resource ID of the desired identity:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<resource-id>": {}
    }
}
```

---

## Supported scenarios using managed identities

Managed identities assigned to the Deidentification service can be used to allow access to Azure Blob Storage for batch deidentification jobs. The service acquires a token as
the managed identity to access Blob Storage and deidentify blobs that match a specified pattern. For more information, including how to grant access to your managed identity,
see [Batch deidentify files in Azure Blob Storage](batch-job.md).

## Clean-up steps

When you remove a system-assigned identity, you delete it from Microsoft Entra ID. System-assigned identities are also automatically removed from Microsoft Entra ID
when you delete the Deidentification service.

# [Azure portal](#tab/portal)

1. In the navigation pane of your Deidentification service, scroll down to the **Security** group.
1. Select **Identity**, then follow the steps based on the identity type:
   - **System-assigned identity**: Within the **System assigned** tab, switch **Status** to **Off**, and then choose **Save**.
   - **User-assigned identity**: Select the **User assigned** tab, select the checkbox for the identity, and select **Remove**. Select **Yes** to confirm.

# [ARM template](#tab/azure-resource-manager)

Any resource of type ``Microsoft.HealthDataAIServices/deidServices`` can have system-assigned identities deleted and user-assigned identities unassigned by 
including this block in the resource definition:

```json
"identity": {
    "type": "None"
}
```

---

## Related content

- [What are managed identities for Azure resources?](/azure/active-directory/managed-identities-azure-resources/overview)