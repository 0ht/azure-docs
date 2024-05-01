---
title: Activate eligible Azure role assignments (Preview) - Azure RBAC
description: Learn how to activate eligible Azure role assignments in Azure role-based access control (Azure RBAC) using the Azure portal.
author: rolyon
manager: amycolannino
ms.service: role-based-access-control
ms.topic: how-to
ms.date: 05/01/2024
ms.author: rolyon
---

# Activate eligible Azure role assignments (Preview)

> [!IMPORTANT]
> Azure role assignment integration with Privileged Identity Management is currently in PREVIEW.
> See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

Eligible Azure role assignments provide just-in-time access to a role for a limited period of time. If you have made eligible for an Azure role, you can activate that role using the Azure portal. Microsoft Entra Privileged Identity Management role activation has been integrated into the Access control (IAM) page in the Azure portal.

## Prerequisites

- Microsoft Entra ID P2 license or Microsoft Entra ID Governance license
- Eligible role assignment

## Activate using the Azure portal

1. Sign in to the [Azure portal](https://portal.azure.com).

1. Click **All services** and then select the scope. For example, you can select **Management groups**, **Subscriptions**, **Resource groups**, or a resource.

1. Click the specific resource.

1. Click **Access control (IAM)**.

1. Click **Activate role**.

    The **assignments** pane appears and lists your eligible role assignments.

    :::image type="content" source="./media/role-assignments-eligible-activate/activate-role.png" alt-text="Screenshot of Access control page and Activate role assignments pane." lightbox="./media/role-assignments-eligible-activate/activate-role.png":::

1. Add a check mark next to a role you want to activate and then click **Activate role**.

    The **Activate** pane appears with activate settings.

    :::image type="content" source="./media/role-assignments-eligible-activate/activate-role-settings.png" alt-text="Screenshot of Activate pane that shows start time, duration, and reason settings." lightbox="./media/role-assignments-eligible-activate/activate-role-settings.png":::

1. On the **Activate** tab, specify the start time, duration, and reason.

1. When finished, click the **Activate** button to activate the role with the selected settings.

    Progress messages appear to indicate the status of the activation.

    When activation is complete, you see a message that the role was successfully activated.

    :::image type="content" source="./media/role-assignments-eligible-activate/activate-role-status.png" alt-text="Screenshot of Activate pane that shows activation status." lightbox="./media/role-assignments-eligible-activate/activate-role-status.png":::

## Next steps

- [Integration with Privileged Identity Management (Preview)](./role-assignments.md#integration-with-privileged-identity-management-preview)
