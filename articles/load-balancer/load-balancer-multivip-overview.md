---
title: Multiple frontends
titleSuffix: Azure Load Balancer
description: This article describes the fundamentals of load balancing across multiple IP addresses using the same port and protocol using multiple frontends on Azure Load Balancer.
services: load-balancer
author: mbender-ms
ms.service: load-balancer
ms.topic: conceptual
ms.date: 04/12/2024
ms.author: mbender
ms.custom: template-concept
---

# Multiple frontends for Azure Load Balancer

Azure Load Balancer allows you to load balance services on multiple frontend IPs. You can use a public or internal load balancer to load balance traffic across a set of services like virtual machine scale sets or virtual machines (VMs).

This article describes the fundamentals of load balancing across multiple frontend IP addresses. If you only intend to expose services on one IP address, you can find simplified instructions for [public](./quickstart-load-balancer-standard-public-portal.md) or [internal](./quickstart-load-balancer-standard-internal-portal.md) load balancer configurations. Adding multiple frontends is incremental to a single frontend configuration. Using the concepts in this article, you can expand a simplified configuration at any time.

When you define an Azure Load Balancer, a frontend and a backend pool configuration are connected with a load balancing rule. The health probe referenced by the load balancing rule is used to determine the health of a VM on a certain port and protocol. Based on the health probe results, new flows are sent to VMs in the backend pool. The frontend is defined using a three-tuple comprised of a frontend IP address (public or internal), a protocol, and a port number from the load balancing rule. The backend pool is a collection of Virtual Machine IP configurations. Load balancing rules can deliver traffic to the same backend pool instance on different ports. This is done by varying the destination port on the load balancing rule.

You can use multiple frontends (and the associated load balancing rules) to load balance to the same backend port or a different backend port. If you want to load balance to the same backend port, you must enable [Azure Load Balancer Floating IP configuration](load-balancer-floating-ip.md) as part of the load balancing rules for each frontend.

## Add Load Balancer frontend 
In this example, add another frontend to your Load Balancer.

1. Sign in to the [Azure portal](https://portal.azure.com).

2. In the search box at the top of the portal, enter **Load balancer**. Select **Load balancers** in the search results.

3. Select **myLoadBalancer** or your load balancer.

4. In the load balancer page, select **Frontend IP configuration** in **Settings**.

5. Select **+ Add** in **Frontend IP configuration** to add a frontend.

6. Enter or select the following information in **Add frontend IP configuration**.
If **myLoadBalancer** is a _Public_ Load Balancer:

    | Setting           | Value                                                     |
    |-------------------|-----------------------------------------------------------|
    | Name              | **myFrontend2**                                           |
    | IP Version        | Select **IPv4** or **IPv6**.                              |
    | IP type           | Select **IP address** or **IP prefix**.                   |
    | Public IP address | Select an existing Public IP address or create a new one. |
   
    If **myLoadBalancer** is an _Internal_ Load Balancer:

    | Setting           | Value                                                                                    |
    |-------------------|------------------------------------------------------------------------------------------|
    | Name              | **myFrontend2**                                                                          |
    | IP Version        | Select **IPv4** or **IPv6**.                                                             |
    | Subnet            | Select an existing subnet.                                                               |
    | Availability zone | Select *zone-redundant* for resilient applications. You can also select a specific zone. |

    
7. Select **Save**.

Next you must associate the frontend IP configuration you have created with an appropriate load balancing rule. Refer to [Manage rules for Azure Load Balancer](manage-rules-how-to.md#load-balancing-rules) for more information on how to do this.

## Remove a frontend

In this example, you remove a frontend from your Load Balancer.

1. Sign in to the [Azure portal](https://portal.azure.com).

2. In the search box at the top of the portal, enter **Load balancer**. Select **Load balancers** in the search results.

3. Select **myLoadBalancer** or your load balancer.

4. In the load balancer page, select **Frontend IP configuration** in **Settings**.

5. Select the delete icon next to the frontend you would like to remove.

6. Note the associated resources that will also be deleted. Check the box that says 'I have read and understood that this frontend IP configuration as well as the associated resources listed above will be deleted'

7. Select **Delete**.


## Limitations

* With the Floating IP rule, your application must use the primary IP configuration of the network interface of your virtual machine for outbound flows. If your application binds to the frontend IP address configured on the loopback interface in the guest OS, Azure's outbound won't rewrite the outbound flow, and the flow fails. Review [outbound scenarios](load-balancer-outbound-connections.md).
* Floating IP isn't currently supported on secondary IP configurations.
* Public IP addresses have an effect on billing. For more information, see [IP Address pricing](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Subscription limits apply. For more information, see [Service limits](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) for details.

## Next steps

- Review [Outbound connections](load-balancer-outbound-connections.md) to understand the effect of multiple frontends on outbound connection behavior.
