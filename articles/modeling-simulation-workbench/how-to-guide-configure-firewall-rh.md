---
title: "Configure Red Hat firewalls: Azure Modeling and Simulation Workbench"
description: Configure firewalls in Red Hat VMs
author: yousefi-msft
ms.author: yousefi
ms.service: modeling-simulation-workbench
ms.topic: how-to
ms.date: 08/18/2024

#CustomerIntent: As a Chamber Admin, I want to configure firewalls on individual VMs to allow applications to communicate within a Chamber.
---
# Configure firewalls in Red Hat

Chamber VMs run Red Hat Enterprise Linux as the operating system. By default, the firewall in these images are configured to deny all inbound connections except to managed services. To allow inbound communication, rules must be added to the firewall to allow traffic to pass. Similarly, if a rule is no longer needed, it should be removed.

This article presents the most common firewall configuration commands. For full documentation or more complex scenarios, see [Chapter 40. Using and configuring firewalld](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html/configuring_and_managing_networking/using-and-configuring-firewalld_configuring-and-managing-networking) of the Red Hat Enterprise Linux 8 documentation.

All the operations referenced here require `sudo` privileges and thus need the Chamber Admin role.

> [!IMPORTANT]
> Modifying firewall rules only allows the individual VM to communicate with other VMs in the same Chamber. Chamber-to-Chamber traffic is never permitted.

## Prerequisites

[!INCLUDE [prereq-user-chamber-admin](includes/prereq/prereq-user-chamber-admin.md)]

## List all open ports

List all currently open ports. This command will list ports and associated protocol.

```bash
$ sudo firewall-cmd --list-all
public (active)
 target: default
 icmp-block-inversion: no
 interfaces: eth0
 sources: 
 services: cockpit dhcpv6-client ssh
 ports: 6817-6819/tcp 60001-63000/tcp
 protocols: 
 forward: no
 masquerade: no
 forward-ports: 
 source-ports: 
 icmp-blocks: 
 rich rules: 
```

## Open ports for traffic

You can open a single or consecutive range of ports for network traffic. Changes to `firewall-d` are temporary and won't persist if the service is restarted or reloaded unless committed.

### Open a single port

Open a single port with `firewalld` for a given protocol using the `--add-port=portnumber/porttype` option. The following example opens port 5510 for TCP.

```bash
$ sudo firewall-cmd --add-port=33500/tcp
success
```

Commit the rule to the permanent set by executing the following:

```bash
$ sudo firewall-cmd --runtime-to-permanent
success
```

### Open a range of ports

Open a range of ports with `firewalld` for a specified protocol with the `--add-port=startport-endport/porttype` otpion. This command is often useful in distributed computing scenarios where workers are dispatched to a large number of nodes and multiple workers may be dispatched to the same physical node. The following example opens 100 consecutive ports starting at port 5000 with the UDP protocol.

```bash
$ sudo firewall-cmd --add-port=5000-5099/udp
success
```

Commit the rule to the permanent set by executing the following:

```bash
$ sudo firewall-cmd --runtime-to-permanent
success
```

## Remove port rules

If rules are no longer needed, they can be removed with the same notation as adding and using the `--remove-port=portnumber/porttype`. The following example removes the single port example we used above.

```bash
$ sudo firewall-cmd --remove-port=33500/tcp
success
```

Commit the rule to the permanent set by executing the following:

```bash
$ sudo firewall-cmd --runtime-to-permanent
success
```

## Related content

* [Upload data to a Chamber](./how-to-guide-upload-data.md)
* [Download data from a Chamber](./how-to-guide-download-data.md)
