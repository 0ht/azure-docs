---
title: Azure Operator Nexus Troubleshooting BareMetal Machine Provisioning
description: Troubleshoot BareMetal Machine Provisioning for Azure Operator Nexus.
ms.service: azure-operator-nexus
ms.custom: troubleshooting
ms.topic: troubleshooting
ms.date: 07/08/2024
author: bpinto
ms.author: bpinto
---

# Troubleshoot BareMetal Machine Provisioning in Nexus Cluster

As part of Cluster deploy action, BareMetal Machines (BMM) are provisioned with required roles to participate in the Nexus Cluster. This document supports troubleshooting for common provisioning issues.

## Prerequisites
1. Install the latest version of the [appropriate CLI extensions](./howto-install-cli-extensions.md)
2. Gather the following information:
  - Subscription ID (SUBSCRIPTION)
  - Cluster name (CLUSTER), Resource Group (CLUSTER_RG) and Managed Resource Group (CLUSTER_MRG)
3. The user needs access to the subscription to run networkfabric and networkcloud CLI extension commands
4. Login to Azure CLI and select the subscription where the cluster is deployed

## BMM roles
For a given SKU, there are required roles to manage and operate the underlying kubernetets cluster.

The following roles are assigned to BMM resources (see [BMM Roles](reference-near-edge-baremetal-machine-roles.md)):

  - `Control plane`: These BMM runs the Kubernetes control plane agents for Nexus platform cluster.
  - `Management plane`: The BMM runs the Nexus platform agents including controllers and extensions.
  - `Compute plane`: The BMM responsible for running actual tenant workloads including Nexus Kubernetes Clusters and Virtual Machines.

## Listing BareMetal Machine status
This command will `list` all `bareMetalMachineName` resources in the Managed Resource Group with simple status:

```azurecli
az networkcloud baremetalmachine list -g $CLUSTER_MRG -o table

Name          ResourceGroup                  DetailedStatus    DetailedStatusMessage
------------  -----------------------------  ----------------  ---------------------------------------
BMM_NAME      CLUSTER_MRG                    STATUS            STATUS_MSG
```

`STATUS` will go through the following phases throughout the BareMetal Machine provisioning process:
`Registering` -> `Preparing` -> `Inspecting` -> `Available` -> `Provisioning` -> `Provisioned`

Where the output fields are the following:
| Phase | Definition |
| --- | --- |
| `Registering` | Verify BMC Connectivity and BMC Credentials, Add BMM to Provisioning Service |
| `Preparing` | Reboot BMM, reset BMC, verify power state |
| `Inspecting` | Update firmware, apply BIOS settings, configure RAID |
| `Available` | BMM ready to install OS |
| `Provisioning` | OS image installing on the BMM, BMM attempts to join Cluster |
| `Provisioned` | BMM successfully provisioned and joined Cluster |
| `Deprovisioning` | BMM provisioning failed and retrying |
| `Failed` | BMM provisioning failed and requires recovery action, all retries exhausted |

During any phase, if BMC becomes unavailable, a network port is down or a hardware component fails, the BMM detailed status will be set to failed and the phase will be blocked from continuing.

To get a more detailed status of the BMM:
```azurecli
az networkcloud baremetalmachine list -g $CLUSTER_MRG --query "sort_by([].{name:name,readyState:readyState,provisioningState:provisioningState,detailedStatus:detailedStatus,detailedStatusMessage:detailedStatusMessage,powerState:powerState,machineRoles:machineRoles| join(', ', @),createdAt:systemData.createdAt}, &name)" --output table

Name            ReadyState    ProvisioningState    DetailedStatus    DetailedStatusMessage                      PowerState    MachineRoles                                      CreatedAt
------------    ----------    -----------------    --------------    -----------------------------------------  ----------    ------------------------------------------------  -----------
BMM_NAME        RSTATE        PROV_STATE           STATUS            STATUS_MSG                                 POWER_STATE   BMM_ROLE                                          CREATE_DATE
```

Where the output fields are the following:
| Output | Definition |
| --- | --- |
| BMM_NAME | BareMetal Machine Name |
| RSTATE | Cluster Participation Status (`True`,`False`) |
| PROV_STATE | Provisioning State (`Succeeded`,`Failed`) |
| STATUS | Provisioning Detailed Staus (`Registering`,`Preparing`,`Inspecting`,`Available`,`Provisioning`,`Provisioned`,`Deprovisioning`,`Failed`) |
| STATUS_MSG | Detailed Provisioning Status Message |
| POWER_STATE | Power State of BMM (`On`,`Off`) |
| BMM_ROLE | BMM Cluster Role contains (`control-plane`,`management-plane`,`compute-plane`) |
| CREATE_DATE | BMM Creation Date |

For example:
```azurecli
x01dev01c01w01  True          Succeeded            Provisioned       The OS is provisioned to the machine       On            platform.afo-nc.microsoft.com/compute-plane=true  2024-05-03T15:12:48.0934793Z
x01dev01c01w01  False         Failed               Preparing         Preparing for provisioning of the machine  Off           platform.afo-nc.microsoft.com/compute-plane=true  2024-05-03T15:12:48.0934793Z
```

## BareMetal Machine details
To show details and status of a single BMM:
```azurecli
az networkcloud baremetalmachine show -g $CLUSTER_MRG -n $BMM_NAME
```
For important BareMetal Machine details:
```azurecli
az networkcloud baremetalmachine show -g $CLUSTER_MRG -n $BMM_NAME --query "{name:name,BootMAC:bootMacAddress,BMCMAC:bmcMacAddress,Connect:bmcConnectionString,SN:serialNumber,rackId:rackId,RackSlot:rackSlot}" -o table
```

## Troubleshooting failed provisioning state

The following conditions can cause provisioning failures
| Error Type | Resolution |
| BMC shows Backplane Comm | Remote Flea drain, Physical Flea Drain, BareMetal Machine Replace |
| Boot MAC Address mismatch | BareMetal Machine Replace |
| Boot Network Data not Retrieved from Redfish | Bounce Port, Remote Flea drain, Physical Flea Drain, BareMetal Machine Replace |
| Disk Data not retrieved from Redfish | Re-seat Disk, Re-seat PERC, Remote Flea drain, Physical Flea Drain, BareMetal Machine Replace |
| BMC Unreachable | Bounce Port, Reseat Cable, Remote Flea drain, Physical Flea Drain, BareMetal Machine Replace |
| BMC fails login | Update Credentials on BMC, BareMetal Machine Replace |
| DIMM, CPU, OEM Critical Errors | Resolve Hardware Issue, BareMetal Machine Replace |
| Stuck at Grub Loader | Reset NVRAM, BareMetal Machine Replace |

### Azure Bare Metal Machine activity log

1. Login to [Azure Portal](https://portal.azure.com/).
2. Search on the BMM Name in the top `Search` box.
3. Select the `Bare Metal Machine (Operator Nexus)` from the search results.
4. Select `Activity log` on the left side menu.
5. Make sure the `Timespan` encompasses the provisioning period.
6. Expand the `BareMetalMachines_Update` operation and select any that show `Failed` status.
7. Select `JSON` tab to get the detailed status message.

Look for failures related to invalid credentials or BMC unavailable.

### Determine BMC IPv4 address
The IPv4 address of the BMC (BMC_IP) is the `Connect` value returned from the `BareMetal Machine Details` section above.

### Validate MAC address of BMM against BMC data

To get the MAC address information from the BareMetal Machine:
```azurecli
az networkcloud baremetalmachine show -g $CLUSTER_MRG -n $BMM_NAME --query "{name:name,BootMAC:bootMacAddress,BMCMAC:bmcMacAddress,SN:serialNumber,rackId:rackId,RackSlot:rackSlot}" -o table
```

Verify the MAC address data against the BMC through the WEB UI: 
`BMC` -> `Dashboard` # Shows BMC MAC Address
`BMC` -> `System Info` -> `Network` -> `Embedded.1-1-1` # Shows Boot MAC Address

Verify the MAC address using `racadm` from a Jumpbox that has access the BMC network:
```bash
racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD getsysinfo | grep "MAC Address "        #BMC MAC
racadm --nocertwarn -r $IP -u $BMC_USR -p $BMC_PWD getsysinfo | grep "NIC.Embedded.1-1-1"  #Boot MAC
```

If the MAC address supplied to the cluster is incorrect, use the BareMetal Machine replace action at [BMM actions](howto-baremetal-functions.md) to correct the addresses.

### Ping test BMC connectivity

Attempt to run ping against the BMC IPv4 address:
1. Obtain the IPv4 address (BMC_IP) from `Determine BMC IPv4 Address` above.
2. Test ping to the BMC:
   To check fr0m a Jumpbox that has access the BMC network:
   ```bash 
   ping $BMC_IP -c 3
   ```
   
   To test from a  BareMetal Machine control-plane host:
   ```azurecli
   az networkcloud baremetalmachine run-read-command -g $CLUSTER_MRG -n $BMM_NAME --limit-time-seconds 60 --commands "[{command:'ping',arguments:['$BMC_IP',-c,3]}]"
   ```

### Reset Port on Fabric Device
If the BMC_IP is not responsive, a reset of the fabric port will retry the auto-negotiation on the port and may bring it back online.

To find the `Network Fabric` port from Azure:
1. Obtain the RackID, RackSlot from the `BareMetal Machine Details` section above.
2. In `Azure Portal`, drill-down to the `Network Rack` RackID for the BareMetal Machine Rack.
3. Select `Network Devices` tab and the Management (Mgmt) swich for the rack.
4. Under `Resources`, select `Network Interfaces` and select the interface for the BMC (iDRAC) or Boot (PXE) interface for the port that requires reset and collect the, Network Fabric Resource Group (NF_RG), the Device Name (NF_DEVICE_NAME) and the interface (NF_DEVICE_INTERFACE_NAME).
5. To reset the port from Azure CLI:
   ```azurecli
   az networkfabric interface update-admin-state -g $NF_RG --network-device-name $NF_DEVICE_NAME --resource-name $NF_DEVICE_INTERFACE_NAME --state Disable
   az networkfabric interface update-admin-state -g $NF_RG --network-device-name $NF_DEVICE_NAME --resource-name $NF_DEVICE_INERFACE_NAME --state Enable
   ```

### BMM remote power drain (flea drain)
Perform a remote Flea Drain against the BareMetal Machine through the WEB UI:
`BMC` -> `Configuration` -> `BIOS Settings` -> `Miscellaneous Settings` -> `Select "Full Power Cycle" under Power Cycle Request` -> `Apply and reboot`

Perform a remote flea drain using `racadm` from a Jumpbox that has access the BMC network:
```bash
racadm set bios.miscsettings.powercyclerequest FullPowerCycle
racadm jobqueue create BIOS.Setup.1-1
racadm serveraction powercycle
```

### BMM physical power drain (flea drain)
For a physical flea drain, the local site hands physically disconnects the power cables from both power adapters for 5 minutes and then restores power. This will ensure the server, capacitors and all components have complete power removal and all cached data will be cleared.

### Reset NVRAM
If the BareMetal Machine fails to provision but the BMC is available, the credentials are correct and the MAC addresses are correct, it is possible that a OEM or hardware error has locked the boot sequence in the BMC to PXE boot only. 

For this condition, typically the BMC console shows the BareMetal Machine at the GRUB Bootloader and will not continue without keyboard intervention. 

To reset the NVRAM, use the following BMC Sequence:
`Maintenance` -> `Diagnostics` -> `Reset iDrac to Factory Defaults` -> `Discard All Settings, but preserve user and network settings` -> `Apply and reboot`

### Reset BMC password
If Actvity Log indicates invalid credentials on the BMC, the following can be run from a Jumpbox that has access the BMC network:
```bash
racadm -r $BMC_IP -u $BMC_USER -p $CURRENT_PASSWORD  set iDRAC.Users.2.Password $BMC_PWD
```

## Adding servers back into the Cluster after a repair

After Hardware is fixed, run BMM Replace following instructions from the following page [BMM actions](howto-baremetal-functions.md).
