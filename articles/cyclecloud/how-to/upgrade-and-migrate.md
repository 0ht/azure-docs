---
title: Upgrade or Migrate
description: Upgrading to a newer CycleCloud version or migrate to a new host.
author: mvrequa
ms.date: 02/04/2020
ms.author: mirequa
---

# Upgrade CycleCloud

It is possible to upgrade the Azure CycleCloud application in place as new versions become available. CycleCloud is released via [Download Center](https://www.microsoft.com/download/details.aspx?id=57182) as either a Debian or RPM package.

To upgrade, copy the installer to the host running CycleCloud and run the platform-specific package upgrade command.

For Debian, use:

```bash
dpkg -i cyclecloud_7.5.2-amd64.deb
```

For RedHat variants, use:

```bash
rpm -U cyclecloud_7.5.2.rpm
```

> [!NOTE]
> Legacy versions of CycleCloud ( < 7.5.0) allowed the installation directory to be configured. If CycleCloud is installed in a directory other than _/opt/cycle_server_ please follow the local migration steps below.

## Migrate CycleCloud to a New Host

The first installation of CycleCloud configures the service user and startup configuration. These will be absent if the installation data is simply
copied from host to host. The following instructions describe how to migrate a CycleCloud installation to another host.

### A Note on Running Clusters

Clusters managed by CycleCloud are sending information to CycleCloud via HTTPS and AMQP. The access information to setup these communication protocols are received by the nodes at launch time. So if the hostname or IP address of CycleCloud changes while nodes are running then communication might be broken. It's recommended to terminate all clusters before migrating.

One exception to this is nodes that are configured with `IsReturnProxy = true`. In this case, the channels of communication are initiated outbound from CycleCloud and will be automatically re-established after migration.

To migrate a CycleCloud host:

1. Stop cycle_server on the source host: `service cycle_server stop` (LSB init scripts) or `systemctl stop cycle_server` (*systemd* init)
2. Run `groupadd cycle_server` and `useradd cycle_server` on the target host. Use the original GID and UID if possible.
3. Install openjdk version 8 on the target host by running `apt-get -y install openjdk-8-jre-headless` or `yum install -y java-1.8.0-openjdk`
4. Transfer to the target host using `rsync -a /opt/cycle_server username@remote_host:/opt/cycle_server` or another meta-data preserving transfer tool.
5. Enable the LSB init or *systemd* init for CycleCloud by running `/opt/cycle_server/util/autostart.sh on`
6. Start the CycleCloud service with either `service cycle_server start` or `systemctl start cycle_server`

The instructions can be simplified if, instead of migrating to a new host, the installation migration is from a non-standard directory to _/opt/cycle_server_:

1. Stop cycle_server on the source host: `service cycle_server stop` (LSB init scripts) or `systemctl stop cycle_server` (*systemd* init)
2. Transfer to the default location `rsync -a /usr/share/hpc/cycle_server /opt/cycle_server`.
3. Enable the LSB init or *systemd* init for CycleCloud by running `/opt/cycle_server/util/autostart.sh on`
4. Start the CycleCloud service with either `service cycle_server start` or `systemctl start cycle_server`

After migrating to a new host, or migrating to the default installation directory, upgrades can be performed as described in the first section.
