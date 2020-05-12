---
title: Cluster Template Reference - Volumes
description: Volume reference for cluster templates for use with Azure CycleCloud
author: adriankjohnson
ms.date: 03/10/2020
ms.author: adjohnso
---

# Volumes

[Volume](~/how-to/mount-disk.md) objects are rank 3 and subordinate to `node` and `nodearray`. A Volume represents an Azure Disk.

## Example

Adding a `[[[volume]]]` section to a node will create an Azure Disk and attach it to the VM.

::: moniker range="=cyclecloud-7"
``` ini
[cluster my-cluster]
  [[node my-node]]
    Credentials = $Credentials
    SubnetId = $SubnetId
    MachineType = $MachineType
    ImageName = $ImageName

    [[[volume my-volume]]]
      Size = 500

    [[[volume another-volume]]]
      Size = 1024
      SSD = true

    [[[volume data]]]
      VolumeId = /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/my-rg/providers/Microsoft.Compute/disks/datadisk
```
::: moniker-end

::: moniker range=">=cyclecloud-8"
``` ini
[cluster my-cluster]
  [[node my-node]]
    Credentials = $Credentials
    SubnetId = $SubnetId
    MachineType = $MachineType
    ImageName = $ImageName

    [[[volume my-volume]]]
      Size = 500

    [[[volume another-volume]]]
      Size = 1024
      StorageAccountType = StandardSSD_LRS

    [[[volume data]]]
      VolumeId = /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/my-rg/providers/Microsoft.Compute/disks/datadisk
```
::: moniker-end

Attribute values that begin with `$` are referencing parameters.

## Attribute Reference

::: moniker range="=cyclecloud-7"
Attribute | Type | Definition
------ | ----- | ----------
Size | String | (Required) Size of disk in GB
VolumeId | String | Resource ID for existing Azure Disk.
SSD | Boolean | If true, use premium disk sku. Otherwise, use standard disk. Default: `false`.  
Azure.Lun | Integer | Override the auto-assigned LUN ID.
Mount | String | Name of mount construct, described in `configuration` object
Azure.Caching | String | [none, readonly, readwrite] Default: `none`.
Persistent | Boolean | If false, disk will be deleted when VM is deleted. Default: `false`.
Disabled | Boolean | If true, this volume will be ignored. Default: `false`.
Azure.SourceUri | String | URI of blob to import into managed disk.
Azure.StorageAccountId | String | Azure resource ID of storage account containing SourceUri blob. Required if blob is in a different subscription.
Azure.SourceResourceId | String | Azure resource ID of source snapshot or managed disk.
::: moniker-end

::: moniker range=">=cyclecloud-8"
Attribute | Type | Definition
------ | ----- | ----------
Size | String | (Required) Size of disk in GB
VolumeId | String | Resource id for existing Azure Disk.
StorageAccountType | String | UltraSSD_LRS, Premium_LRS, StandardSSD_LRS, Standard_LRS ([Azure Disk Types](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)) If not set, defaults to Standard_LRS or Premium_LRS depending on VM size capabilities.
DiskIOPSReadWrite | Integer | Provisioned IOPS see [Ultra Disks](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types#ultra-disk)
DiskMBPSReadWrite | Integer | Disk throughput MB/s see [Ultra Disks](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types#ultra-disk) 
Azure.Lun | Integer | Override the auto-assigned LUN ID.
Mount | String | Name of mount construct, described in `configuration` object
Azure.Caching | String | None, readonly, readwrite. Default is none.
Persistent | Boolean | If false, disk will be deleted with vm is deleted. Default is false.
Disabled | Boolean | If true, this volume will be ignored. Default is false.
Azure.SourceUri | String | URI of blob to import into managed disk.
Azure.StorageAccountId | String | Azure resource ID of storage account containing SourceUri blob. Required if blob is in a different subscription.
Azure.SourceResourceId | String | Azure resource ID of source snapshot or managed disk.
::: moniker-end

### Boot Volume
::: moniker range="=cyclecloud-7"
For each node, the volume named `boot` exposes some advanced configuration of the OS boot volume.

``` ini
  [[node scheduler]]
    [[[volume boot]]]
      Size = 100
      SSD = true
      Azure.Caching = ReadOnly
```

>[!NOTE]
>This section is ignored if EphemeralOSDisk is set
::: moniker-end

::: moniker range=">=cyclecloud-8"
For each node, the volume named `boot` exposes some advanced configuration of the OS boot volume. Storage type settings for the boot disk are ignored if `EphemeralOSDisk=true` is specified for the node.

``` ini
  [[node scheduler]]
  Zone = 1
    [[[volume boot]]]
      Size = 100
      StorageAccountType = UltraSSD_LRS
      DiskIOPSReadWrite = 38400
      DiskMBPSReadWrite = 2000
```
> [!NOTE]
> UltraSSD disks can only be used with availability zones (availability sets and single VM deployments outside of zones will not have the ability to attach an ultra disk)

::: moniker-end