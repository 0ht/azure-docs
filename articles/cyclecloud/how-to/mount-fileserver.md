---
title: Network File System Options
description: Attach and manage simple network file systems within Azure CycleCloud.
author: KimliW
ms.date: 02/21/2020
ms.author: adjohnso
---

# Configure NFS Mounts

Azure CycleCloud provides built-in support for mounting a simple Network File System (NFS).
The NFS can be another resource managed by CycleCloud or an external resource.

## Mount an NFS Filesystem

To mount an existing NFS filesystem:

``` ini
[[[configuration cyclecloud.mounts.nfs_data]]]
type = nfs
mountpoint = /mnt/exports/nfs_data
export_path = /mnt/exports/data
```

The `export_path` is the path on the server, and the `mountpoint` is the path to mount the share on the client. The mounted NFS filesystem may be exported from a node in the same CycleCloud cluster, exported from a node in another CycleCloud cluster, or a separate NFS filesystem that allows simple mounts. If the filesystem is exported from a node in the local cluster, then CycleCloud will use search to discover the address automatically. If the filesystem is exported from a different CycleCloud cluster, then the mount configuration may specify attribute `cluster_name` to instruct CycleCloud to search the cluster with that name:

``` ini
[[[configuration cyclecloud.mounts.other_cluster_fs]]]
type = nfs
mountpoint = /mnt/exports/other_cluster_fs
export_path = /mnt/exports/data
cluster_name = filesystem_cluster
```

To specify the location of the filesystem explicitly (required for mounting non-CycleCloud filesystems), the mount configuration may specify the attribute `address` with the hostname or IP of the filesystem:

``` ini
[[[configuration cyclecloud.mounts.external_filer]]]
type = nfs
mountpoint = /mnt/exports/external_filer
address = 54.83.20.2
```

## Default Shares

By default, most CycleCloud cluster types include at least one shared drive mounted at _/shared_ and _/mnt/exports/shared_. For clusters that need a simple shared filesystem, this mount is often sufficient.

Many cluster types also include a second NFS mount at _/sched_ and _/mnt/exports/sched_ which is reserved for use by the chosen scheduler. In general, this mount should not be accessed by applications.

The mount configurations for the default shares reserve filesystem names `cyclecloud.mounts.shared` and `cyclecloud.mounts.sched`. Modifying the default configurations for these shares is possible, but may result in unexpected behavior since many cluster types rely on the default mounts.

## Disabling NFS Mounts

Azure CycleCloud NFS mounts may be disabled by setting the `disabled` attribute to true. The default shares may also be disabled this way:

``` ini
[[[configuration]]]
    cyclecloud.mounts.sched.disabled = true
    cyclecloud.mounts.shared.disabled = true
    cshared.server.legacy_links_disabled = true
```

Many clusters assume a shared storage device to be available cluster-wide at _/shared_. Therefore if you use these configurations
enable a fileserver and mount it on each cluster node with:

``` ini
[[[configuration cyclecloud.mounts.external_shared]]]
    type = nfs
    mountpoint = /shared
    export_path = /mnt/raid/export
    address = 54.83.20.2
```

## Mount Configuration Options

| Option | Definition |
| ------ | ---------- |
| type          | *REQUIRED* The type attribute must be set to `nfs` for all NFS exports to differentiate from volume mounts and other shared filesystem types.   |
| export_path   | The location of the export on the NFS filer.  If an export_path is not specified, the  mountpoint of the mount will be used as the export_path.  |
| mountpoint    | The location where the filesystem will be mounted after any additional configuration is applied.  If the directory does not already exist, it will be created. |
| cluster_name  | The name of the CycleCloud cluster which exports the filesystem.  If not set, the node's local cluster is assumed.   |
| address       | The explicit hostname or IP address of the filesystem.  If not set, search will attempt to find the filesystem in a CycleCloud cluster. |
| options       | Any non-default options to use when mounting the filesystem.    |
| disabled      | If set to `true`, the node will not mount the filesystem.  |

> [!NOTE]
> Changing the hostname scheme is not supported for most schedulers.

## Further Reading

* [How to Mount a Disk](./mount-disk.md)
* [How to Create a File Share and File Server](./create-fileserver.md)