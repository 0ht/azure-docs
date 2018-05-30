# Storage

CycleCloud supports automatically attaching volumes (disks) to your nodes for additional storage space.
For example, to create a 100GB volume, add the following to your `[[node]]` element in your cluster template:

    [[[volume example-vol]]]
    Size = 100

This volume will be created when the instance is started, and deleted when the instance is terminated.
If you want to preserve the data on the volume even after the instance is terminated, make it a **persistent** volume:

    [[[volume example-vol]]]
    Size = 100
    Persistent = true

This volume will be created the first time the instance is started, but will not be deleted when the instance is terminated. Instead, it will be kept and re-attached to the instance the next time the node is started. Persistent volumes are not deleted until the cluster is deleted.

To use Premium Storage for the disk (or the equivalent cloud provider SSD storage), use `SSD = true`:

    [[[volume example-vol]]]
    Size = 100
    Persistent = true
    SSD = true

> [!NOTE]
Azure SSD will round up to the next size for [pricing](https://azure.microsoft.com/en-us/pricing/details/managed-disks). For example, if you create a disk size of 100GB, you will be charged at the 128GB rate.

> [!WARNING]
> When your cluster is deleted, all persistent volumes are deleted as well! If you want your storage to persist longer than your cluster, you must attach a preexisting volume by id.

For Linux-based operating systems, you can control what device to attach the volume to, using the `Device` attribute:

    [[[volume example-vol]]]
    Size = 100
    Device = /dev/sdk

If you do not specify a device, CycleCloud will automatically pick a device that is not in use.
The specific device chosen depends on the cloud provider configuration and the image.


# Mounting Volumes

Specifying a volume attaches the device(s) to your instance, but does not mount and format the device.
If you prefer to have the volumes mounted and formatted when the node is started, set
the optional attribute `Mount` to the name of the mountpoint configuration you wish to use with that volume:

    [[[volume reference-data]]]
    Size = 100
    Mount = data              # The name of the mountpoint to use with this volume

The mountpoint named `data` is then defined in the configuration section on the node:

    [[[configuration cyclecloud.mounts.data]]]
    mountpoint = /mount
    fs_type = ext4

The above configuration specifies that you are configuring a `cyclecloud.mountpoint`
named `data` using all volumes which include `Mount = data`.
This volume would be formatted with the `ext4` filesystem and would appear at `/mount`.

## Devices

By defining volumes with a `Mountpoint` attribute, the device names will be automatically assigned and used for a given mountpoint. You can, however, customize a mountpoint with your own device names if there is a need. For example:

    [[node master]]
      [[[configuration cyclecloud.mounts.data]]]
      mountpoint = /data
      Azure.LUN=0

In Azure, devices are assigned using [Logical Unit Numbers (LUN)](https://docs.microsoft.com/en-us/powershell/module/azure/add-azuredatadisk?view=azuresmps-4.0.0). The `devices` parameter is used to manually specify each underlying device that is part of the mountpoint configuration.

In most cases, Azure CycleCloud will automatically assign devices for you. Specifying devices manually is advanced usage, and useful in cases where the AMI you are using for your node has volumes that will be automatically attached because their attachment was baked into the image. Specifying the devices by hand can also be useful when the ordering of devices has special meaning.

> [!NOTE]
> A volume named `boot` has special meaning.

### AWS

For AWS, use `device=/dev/sdc` in place of LUN.

### Google Cloud

Google Cloud has the capability to resize the boot volume by defining the size attribute:

  [[[volume boot]]]
  Size = 50

## Advanced Usage

The previous example was a fairly simple: mounting a single, pre-formatted snapshot to a node. However, more advanced mounting can take place, including RAIDing multiple devices together, encrypting, and formatting new filesystems. As an example, the following will describes how to RAID several EBS volumes together and encrypt them before mounting them as a single device on a node:

  [[node master]]
  ....
    [[[volume vol1]]]
    VolumeId = vol-1234abcd
    Mount = giant

    [[[volume vol2]]]
    VolumeId = vol-5678abcd
    Mount = giant

    [[[volume vol3]]]
    VolumeId = vol-abcd1234
    Mount = giant

    [[[configuration cyclecloud.mounts.giant]]]
    mountpoint = /mnt/giant
    fs_type = xfs
    raid_level = 0
    encryption.bits = 256
    encryption.key = "0123456789abcdef9876543210"

The above example shows there are three EBS volumes that should be attached to the node named `master`, and that their mountpoint is named `giant`. The configuration for the mountpoint says that these three volumes should be RAIDed together using `raid_level = 0` for RAID0, formatted using the `xfs` filesystem, and the resulting device should be mounted at `/mnt/giant`. The device should also have block level encryption using 256-bit AES with an encryption key as defined in the template.

## Ephemeral Storage and Mounting

By default, ephemeral devices for a node will be automatically attached (see volume section above) and then RAIDed with using RAID0 and mounted to `/mnt`. This is the suggested way of using ephemeral devices within CycleCloud, however you can override the default behavior if necessary. All ephemeral devices are automatically assigned a mountpoint of `ephemeral`, so you can use this default behavior to customize the mountpoint as follows:

    [[[configuration cyclecloud.mounts.ephemeral]]]
    mountpoint = /mnt/ephemeral
    fs_type = ext4
    raid_level = 1

This configuration will instruct Azure CycleCloud to combine all the ephemeral device using RAID1, format them using ext4, then mount them at the alternative location of `/mnt/ephemeral`, which is different from the default of `/mnt`.

You can manually define ephemeral volumes using the following syntax, although it is not required in most cases as reasonable defaults are already in place:

    [[node master]]
      [[[volume ephemeral0]]]
      Ephemeral = true
      Mount = ephemeral

      [[[volume ephemeral1]]]
      Ephemeral = true
      Mount = ephemeral

> [!NOTE]
> If you do not want any ephemeral automatically mapped for you, meaning you will either use no ephemeral storage or will rely on another form of attaching/mounting, you can set the `DisableAutomaticEphemeral` to true:

    [[node master]]
    DisableAutomaticEphemeral = true  # No ephemeral disks will be automatically attached to this instance


## Mounting Configuration Options

| Option                | Definition                                                                                                                                                                                                                                                                                               |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| mountpoint            | The place where the device(s) will be mounted after any additional configuration is applied. If a mountpoint is not specified, the name of the mount will be used as part of the mountpoint. For example, if your mount was named ‘data’, the mountpoint would default to ‘/media/data’.                 |
| options               | Any non-default options to use when mounting the device.                                                                                                                                                                                                                                                 |
| fs_type               | The filesystem to use when formatting and/or mounting. Available options are: ext3, ext4, xfs.                                                                                                                                                                                                           |
| size                  | The size of the filesystem to create when formatting the device(s). Omitting this parameter will use all the space on the device. Size can be specified using M for megabytes (e.g. 150M for 150MB) G for gigabytes (e.g. 200G for 20GB), or percentages (e.g. 100% to use all of the available space).  |
| disabled              | If true, the mountpoint will not be created. Useful for quick toggling of mounts for testing and to disable automatic ephemeral mounting. Default: false.                                                                                                                                                |
| raid_level            | The type of RAID configuration to use when multiple devices/volumes are being used. Defaults to a value of 0, meaning RAID0, but other raid levels can be used such as 1 or 10.                                                                                                                          |
| raid_device_symlink   | When a raid device is created, specifying this attribute will create a symbolic link to the raid device. By default, this attribute is not set and therefore no symlink is created. This should be set in cases where you need access to the underlying raid device.                                     |
| devices               | This is a list of devices that should compose the mountpoint. In general, this parameter shouldn’t need to be specified (as CycleCloud will set this for you based on [[[volume]]] sections), but you can manually specify the devices if so desired.                                                    |
| vg_name               | Devices are configured on Linux using the Logical Volume Manager (LVM). The volume group name will be automatically assigned, but in cases where a specific name is used, this attribute can be set. The default is set to cyclecloud-vgX, where X is an automatically assigned number.                  |
| lv_name               | Devices are configured on Linux using the Logical Volume Manager (LVM). This value is automatically assigned and does not need specification, but if you want to use a custom logical volume name, it can be specified using this attribute. Defaults to lv0.                                            |
| order                 | By specifying an order, you can control the order in which mountpoints are mounted. The default order value for all mountpoints is 1000, except for ‘ephemeral’ which is 0 (ephemeral is always mounted first by default). You can override this behaviour on a case-by-case basis as needed.            |
| encryption.bits       | The number of bits to use when encrypting the filesystem. Standard values are 128 or 256bit AES encryption. This value is required if encryption is desired.                                                                                                                                             |
| encryption.key        | The encryption key to use when encrypting the filesystem. If omitted, a random 2048 bit key will be generated. The automatically generated key is useful for when you are encrypting disks that do not persist between reboots (e.g. encrypting ephemeral devices).                                      |
| encryption.name       | The name of the encrypted filesystem, used when saving encryption keys. Defaults to cyclecloud_cryptX, where X is an automatically generated number.                                                                                                                                                     |
| encryption.key_path   | The location of the file the key will be written on disk to. Defaults to `/root/cyclecloud_cryptX.key`, where X is a automatically generated number.                                                                                                                                                       |

Mounting Configuration Defaults
*******************************

There are times when many different mountpoints are defined, and specifying the same options over and over can become tedious.
The following options will allow you to set the system defaults for various mountpoints, and will be used unless otherwise specified:

+--------------------------------------------+--------------------------------------------------------------------------------------------------------------------+
| Options                                    | Definition                                                                                                         |
+============================================+====================================================================================================================+
| cyclecloud.mount_defaults.fs_type          | The filesystem type to use for mounts, if not otherwise specified. Default: ext3/ext4 (depending on the platform). |
+--------------------------------------------+--------------------------------------------------------------------------------------------------------------------+
| cyclecloud.mount_defaults.size             | The default filesystem size to use, if not otherwise specified. Default: 50GB.                                     |
+--------------------------------------------+--------------------------------------------------------------------------------------------------------------------+
| cyclecloud.mount_defaults.raid_level       | The default raid level to use if multiple devices are assigned to the mountpoint. Default: 0 (RAID0).              |
+--------------------------------------------+--------------------------------------------------------------------------------------------------------------------+
| cyclecloud.mount_defaults.encryption.bits  | The default encryption level unless otherwise specified. Default: undefined.                                       |
+--------------------------------------------+--------------------------------------------------------------------------------------------------------------------+
