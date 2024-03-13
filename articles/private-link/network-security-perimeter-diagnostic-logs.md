---
title: 'Diagnostic logging for Azure Network Security Perimeter'
description: Learn the options for storing diagnostic logs for Network Security Perimeter and how to enable logging through the Azure portal.
author: mbender-ms
ms.author: mbender
ms.service: private-link
ms.topic: concept
ms.date: 02/05/2023
#CustomerIntent:
---

# Diagnostic logging for Azure Network Security Perimeter

In this article, you will learn about the diagnostic logs for network security perimeter and how to enable logging. You'll learn access logs categories used. Then, you'll discover the options for storing diagnostic logs and how to enable logging through the Azure portal.

[!INCLUDE [network-security-perimeter-preview-message](../../includes/network-security-perimeter-preview-message.md)]

## Access logs categories

Network security perimeter access logs categories are based on the results of access rules evaluation. The log categories chosen in the diagnostic settings are sent to the customer chosen storage location. The following are the descriptions for each of the access log categories including the modes in which they are applicable:

| **Log category** | **Description** | **Applicable to Modes** |
| --- | --- | --- |
| network security perimeterPublicInboundPerimeterRulesAllowed | Inbound access allowed based on network security perimeter access rules | Learning/Enforced |
| network security perimeterPublicInboundPerimeterRulesDenied | Public inbound access denied by network security perimeter | Enforced |
| network security perimeterPublicOutboundPerimeterRulesAllowed | Outbound access allowed based on network security perimeter access rules | Learning/Enforced |
| network security perimeterPublicOutboundPerimeterRulesDenied | Public outbound access denied by network security perimeter | Enforced |
| network security perimeterOutboundAttempt | Outbound attempt within network security perimeter or between two 'linked' network security perimeters | Learning/Enforced |
| network security perimeterIntraPerimeterInboundAllowed | Inbound access within perimeter | Learning/Enforced |
| network security perimeterPublicInboundResourceRulesAllowed | When network security perimeter rules denies, and Inbound access allowed based on PaaS resource rules | Learning |
| network security perimeterPublicInboundResourceRulesDenied | When network security perimeter rules denies, Inbound access denied by PaaS resource rules | Learning |
| network security perimeterPublicOutboundResourceRulesAllowed | When network security perimeter rules denies, Outbound access allowed based on PaaS resource rules | Learning |
| network security perimeterPublicOutboundResourceRulesDenied | When network security perimeter rules denies, Outbound access denied by PaaS resource rules | Learning |
| network security perimeterCrossPerimeterInboundAllowed | Inbound access based on 'Link' rules | Learning/Enforced |
| network security perimeterCrossPerimeterOutboundAllowed | Outbound access based on 'Link' rules | Learning/Enforced |
| network security perimeterPrivateInboundAllowed | Private endpoint traffic | Learning/Enforced |

## Storage options for access logs

You can store the diagnostic logs in the following locations:

| **Service** | **Description** |
| --- | --- |
| **Log Analytic workspace** | Log Analytic workspaces are recommended since they all you to use the predefined queries, visualizations and set alerts based on specific log conditions. |
|** Azure Storage account** | Storage accounts are best used for logs when logs are stored for a longer duration and reviewed when needed. |
| **Azure Event Hubs** | Event hubs are a great option for integrating with other security information and event management (SIEM) tools to get alerts on your resources. |

## Enable logging through the Azure portal

1. In the Azure portal, navigate to your network security perimeter.
1. Select **Diagnostic settings**.
1. Click on 'Add Diagnostic setting' to configure the collection of the following data:

Public inbound access allowed by network security perimeter access rules.
Public inbound access denied by network security perimeter access rules.
Public outbound access allowed by network security perimeter access rules.
Public outbound access denied by network security perimeter access rules.
Inbound access allowed within same perimeter.
Public inbound access allowed by PaaS resource rules.
Public inbound access denied by PaaS resource rules.
Public outbound access allowed by PaaS resource rules.
Public outbound access denied by PaaS resource rules.
Private endpoint traffic allowed.
Cross perimeter outbound access allowed by perimeter link.
Cross perimeter inbound access allowed by perimeter link
Outbound attempted to same or different perimeter.

The Diagnostics settings page provides the settings for the diagnostic logs. You can use Log Analytics, storage account and/or event hubs to save the diagnostic logs. 

1. Type a name for the settings, confirm the settings, and select Save.

## Activity log

Azure generates the activity log by default. The logs are preserved for 90 days in the Azure event logs store. Learn more about these logs by reading the [View events and activity log](../azure-monitor/essentials/activity-log.md) article.

## Next steps

- [Create a Network Security Perimeter](create-network-security-perimeter-portal.md)