---
title: What is Azure DNS?
description: Overview of DNS hosting service on Microsoft Azure. Host your domain on Microsoft Azure.
author: greg-lindsay
ms.service: dns
ms.topic: overview
ms.date: 05/20/2024
ms.author: greglin
#Customer intent: As an administrator, I want to evaluate Azure DNS so I can determine if I want to use it instead of my current DNS service.
---

# What is Azure DNS?

Azure DNS provides DNS hosting and resolution using the Microsoft Azure infrastructure. Azure DNS consists of three services:
- [Azure Public DNS](public-dns-overview.md) is a hosting service for DNS domains. By hosting your domains in Azure, you can manage your DNS records by using the same credentials, APIs, tools, and billing as your other Azure services.
- [Azure Private DNS](private-dns-overview.md) is a DNS service for your virtual networks. Azure Private DNS manages and resolves domain names in the virtual network without the need to configure a custom DNS solution. 
- [Azure DNS Private Resolver](dns-private-resolver-overview.md) is a service that enables you to query Azure DNS private zones from an on-premises environment and vice versa without deploying VM based DNS servers.

Using Azure DNS, you can host and resolve public domains, manage DNS resolution in your virtual networks, and enable name resolution between Azure and your on-premises resources.

## Next steps

* To learn about DNS zones and records, see [DNS zones and records overview](dns-zones-records.md).
* To learn how to create a zone in Azure DNS, see [Create a DNS zone](./dns-getstarted-portal.md).
* For frequently asked questions about Azure DNS, see the [Azure DNS FAQ](dns-faq.yml).
* [Learn module: Introduction to Azure DNS](/training/modules/intro-to-azure-dns).
