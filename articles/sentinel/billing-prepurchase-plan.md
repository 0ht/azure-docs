---
title: Optimize costs with a prepurchase plan
titleSuffix: Microsoft Sentinel
description: Learn how to save costs and buy a Microsoft Sentinel prepurchase plan
author: austinmccollum
ms.topic: how-to
ms.date: 07/10/2024
ms.author: austinmc
ms.collection: usx-security
#customerintent: As a SOC administrator or a billing specialist, I want to know how to buy a prepurchase plan and whether commit units will benefit us financially.
---

# Optimize Microsoft Sentinel costs with a prepurchase plan

Save on your Microsoft Sentinel costs when you buy a prepurchase plan. Prepurchase plans are commit units (CUs) bought at discounted tiers in your purchasing currency for a specific product. The more you buy, the greater the discount. Purchased CUs pay down qualifying costs in US dollars (USD). So, if Microsoft Sentinel generates a retail cost of $100, then 100 Sentinel CUs (SCUs) are consumed. 

Any eligible Microsoft Sentinel retail costs automatically deduct first from your SCUs over the course of its one year term or until they are depleted. Your prepurchase plan SCUs start paying for your Microsoft Sentinel workspace costs without needing to redeploy or reassign the plan, and by default automatically renew to ensure you continue saving.

## Prerequisites

To buy a prepurchase plan, you must have one of the following Azure subscriptions and roles:
- For an Azure subscription, the owner role or reservation purchaser role is required.
- For an Enterprise Agreement (EA) subscription, the [**Reserved Instances** policy option](../cost-management-billing/manage/direct-ea-administration.md#view-and-manage-enrollment-policies) must be enabled. To enable that policy option, you must be an EA administrator of the subscription.
- For a Cloud Solution Provider (CSP) subscription, follow one of these articles:
   - [Buy Azure reservations on behalf of a customer](/partner-center/customers/azure-reservations-buying)
   - [Allow the customer to buy their own reservations](/partner-center/customers/give-customers-permission)

## Determine the right size to buy

Prepurchase plans pair nicely with Microsoft Sentinel commitment tiers. Once you plan your Microsoft Sentinel ingestion volume and reduce complexity with a simplified pricing tier, choose an appropriate commitment tier. Then it's easier to decide on the size of a prepurchase plan to buy. Microsoft Sentinel commitment tiers are monthly commitments and considered a retail cost. Microsoft Sentinel prepurchase plans have a term agreement of one year.

Here's an example of the decision making and cost savings for a prepurchase plan. If you have a commitment tier of 200 GB/day, there's an associated monthly estimated cost for both the ingestion to the workspace and the analysis for Microsoft Sentinel. For example purposes, let's say that monthly cost is $20,000 USD with simplified pricing and provides a 39% savings over the pay-as-you-go tier with the same 200 GB/day.

A $100,000 USD prepurchase plan covers five months of that commitment tier but is valid for paying Microsoft Sentinel costs for 12 months. That prepurchase plan is bought at a 22% discount for $78,000 USD. 

The savings for the commitment tier and the prepurchase plan combine. The original pay-as-you-go price for five months of 200 GB/day ingestion and analysis costs is about $160,000 USD. With an accurate commitment tier and a prepurchase plan, the cost reduced to $78,000 USD for a combined savings of over 51%.

For more information, see the following articles:
- [Switch to simplified pricing](enroll-simplified-pricing-tier.md)
- [Set or change commitment tier](billing-reduce-costs.md#set-or-change-pricing-tier)

>[!IMPORTANT]
> The prices mentioned are for example purposes only. To determine the latest commitment tier prices, see [Microsoft Sentinel pricing](https://azure.microsoft.com/pricing/details/microsoft-sentinel/).

Microsoft Sentinel classic pricing tier usage and all simplified pricing is eligible for Microsoft Sentinel prepurchase plans. From your Microsoft Sentinel bill, these costs are the entries with the **Sentinel** service name in the invoice details. 

For more information on how to view Microsoft Sentinel simplified or classic pricing tiers in your invoice details, see [Understand your Microsoft Sentinel bill](billing.md#understand-your-microsoft-sentinel-bill).

Keep in mind, Microsoft Sentinel integrates with many other Azure services that have separate costs not eligible to use with the prepurchase SCUs. For more information, see [Costs and pricing for other services](billing.md#costs-and-pricing-for-other-services).

## Purchase Microsoft Sentinel commit units

Purchase Microsoft Sentinel prepurchase plans in the [Azure portal reservations](https://portal.azure.com/#view/Microsoft_Azure_Reservations/ReservationsBrowseBlade/productType/Reservations). 

1. Go to the [Azure portal](https://portal.azure.com)
1. Navigate to the **Reservations** service.
1. On the **Purchase reservations page**, select **Microsoft Sentinel Pre-Purchase Plan**.
1. On the **Select the product you want to purchase** page, select a subscription. Use the **Subscription** list to select the subscription used to pay for the reserved capacity. The payment method of the subscription is charged the upfront costs for the reserved capacity. Charges are deducted from the enrollment's Azure Prepayment (previously called monetary commitment) balance or charged as overage.
1. Select a scope.
   - **Single resource group scope** - Applies the reservation discount to the matching resources in the selected resource group only.
   - **Single subscription scope** - Applies the reservation discount to the matching resources in the selected subscription.
   - **Shared scope** - Applies the reservation discount to matching resources in eligible subscriptions that are in the billing context. For Enterprise Agreement customers, the billing context is the enrollment.
   - **Management group** - Applies the reservation discount to the matching resource in the list of subscriptions that are a part of both the management group and billing scope.
1. Select how many Microsoft Sentinel commit units you want to purchase.

   `Need Sentinel screenshot here`
   :::image type="content" source="media/billing-prepurchase-plan/sentinel-prepurchase-plan.png" alt-text="Screenshot showing Microsoft Sentinel prepurchase plan discount tiers and their term lengths." lightbox="media/billing-prepurchase-plan/sentinel-prepurchase-plan.png":::

1. Choose to automatically renew the prepurchase reservation. *The renewal setting is turned by default*. For more information, see [Renew a reservation](../cost-management-billing/reservations/reservation-renew.md).

## Change scope and ownership

You can make the following types of changes to a reservation after purchase:

- Update reservation scope
- Update who can view or manage the reservation. For more information, see [Who can manage a reservation by default](../cost-management-billing/reservations/manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).

You can't split or merge a **Microsoft Sentinel Pre-Purchase Plan**. For more information about managing reservations, see [Manage reservations after purchase](../cost-management-billing/reservations/manage-reserved-vm-instance.md).

## Cancellations and exchanges

Cancel and exchange isn't supported for **Microsoft Sentinel Pre-Purchase Plans**. All purchases are final.

## Related content

To learn more about Azure Reservations, see the following articles:
- [What are Azure Reservations?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Manage Azure Reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md)

To learn more about Microsoft Sentinel costs, see [Plan costs and understand Microsoft Sentinel pricing and billing](billing.md).