---
title: Azure Update Manager support matrix for updates
description: This article provides a summary of support for updates, one time updates, periodic assessments and scheduled patching.
ms.service: azure-update-manager
author: SnehaSudhirG
ms.author: sudhirsneha
ms.date: 01/03/2024
ms.topic: overview
zone_pivot_groups: support-matrix-updates
---

# Support for Updates/One time Updates/Periodic assessments and Scheduled patching

**Applies to:** :heavy_check_mark: Windows VMs :heavy_check_mark: Linux VMs :heavy_check_mark: On-premises environment :heavy_check_mark: Azure Arc-enabled servers :heavy_check_mark: Azure VMs.

This article details the Windows and Linux operating systems supported and system requirements for machines or servers managed by Azure Update Manager

::: zone pivot="azure-vm"

## Azure virtual machines

### Azure Marketplace/PIR images

The Azure Marketplace image has the following attributes:

- **Publisher**: The organization that creates the image. Examples are `Canonical` and `MicrosoftWindowsServer`.
- Offer: The name of the group of related images created by the publisher. Examples are `UbuntuServer` and `WindowsServer`.
- **SKU**: An instance of an offer, such as a major release of a distribution. Examples are `18.04LTS` and `2019-Datacenter`.
- **Version**: The version number of an image SKU.

Update Manager supports the following operating system versions on VMs for all operations except automatic VM guest patching. You might experience failures if there are any configuration changes on the VMs, such as package or repository.

Following is the list of supported images and no other marketplace images released by any other publisher are supported for use with Azure Update Manager.

### Supported Windows OS versions

| **Publisher**| **Offer** | **Plan** | **Unsupported image(s)** |
| ----| --- | --- | --- |
| Center for Internet Security Inc | cis-windows-server-2012-r2-v2-2-1-l1 | cis-ws2012-r2-l1 | |
| |cis-windows-server-2012-r2-v2-2-1-l2 | cis-ws2012-r2-l2 | |
| |cis-windows-server-2016-v1-0-0-l1  | cis-ws2016-l1 | |
| |cis-windows-server-2016-v1-0-0-l2 | cis-ws2016-l2 | |
| |cis-windows-server-2019-v1-0-0-l1 | cis-ws2019-l1 | |
| |cis-windows-server-2019-v1-0-0-l2 | cis-ws2019-l2 | |
| |cis-windows-server-2022-l1 | * | |
| |cis-windows-server-2022-l2 | * | |
| |cis-windows-server| cis-windows-server2016-l* </br> cis-windows-server2019-l* </br> cis-windows-server2022-l </br> cis-windows-server2019-stig-gen1 | cis-windows-server-l*-azure-observability |
| |cis-win-2016-stig | cis-win-2016-stig | |
| | cis-win-2019-stig | cis-win-2019-stig | |
| cloud-infrastructure-services | hpc2019-windows-server-2019 | hpc2019-windows-server-2019 | |
| microsoftsqlserver | sql2016sp1-ws2016 | standard | |
| | sql2016sp2-ws2016 | standard | |
| | sql2017-ws2016 | enterprise | |
| | sql2017-ws2016 | standard | |
| | sql2019-ws2019 | enterprise | |
| | sql2019-ws2019 | sqldev | |
| | sql2019-ws2019 | standard | |
| | sql2019-ws2019 | standard-gen2 | |
| cognosys | sql-server-2016-sp2-std-win2016-debug-utilities |sql-server-2016-sp2-std-win2016-debug-utilities | |
| filemagellc | filemage-gateway-vm-win-001 | | | 
| | filemage-gateway-vm-win-002 | | | 
| github | github-enterprise | | |
| | github-enterprise | | | 
|  matillion | matillion | | | 
| | matillion-etl-for-snowflake | | | 
| microsoft-ads | windows-data-science-vm | windows2016 </br> windows2016byol | |
| microsoft-dsvm | ubuntu-1804    | 1804-gen2 | |
| | dsvm-windows   | * | | 
| | dsvm-win-2019  | * | | 
| | dsvm-win-2022  | * | | 
| microsoftazuresiterecovery | process-server |windows-2012-r2-datacenter | |
| microsoftbiztalkserver | biztalk-server | * | |
| microsoftdynamicsax | dynamics | * | |
| microsoftpowerbi | * | * | |
| microsoftsharepoint | microsoftsharepointserver | * | |
| Visualstudio | Visualstudio* |  *-ws2012r2 </br> *-ws2016-ws2019 </br> *-ws2022 | |
| microsoftwindowsserver | windows server | windowsserver 2008 | |
| | windows-cvm | * | |
| | windowsserver-gen2preview | * | |
| | windowsserverdotnet | * | |
| | microsoftwindowsserver | windowsserverupgrade | * |
| | microsoftwindowsserverhpcpack | windowsserverhpcpack | * |
| microsoftwindowsserver | netapp | netapp-oncommand-cloud-manager | occm-byol |

### Supported Linux OS versions

| **Publisher**| **Offer** | **Plan** | **Unsupported image(s)** |
| --- | --- | --- | --- |
| ad-dc | ad-dc-2016 | ad-dc-2016 | |
| | ad-dc-2019 | ad-dc-2019 | |
| | ad-dc-2022 | ad-dc-2022 | |
| almalinux-hpc| almalinux-hpc | 8_6-hpc, 8_6-hpc-gen2 | |
| aviatrix-companion-gateway | aviatrix-companion-gateway-v9 | aviatrix-companion-gateway-v9 | |
| | aviatrix-companion-gateway-v10 | aviatrix-companion-gateway-v10 </br> aviatrix-companion-gateway-v10u | |
| | aviatrix-companion-gateway-v12 | aviatrix-companion-gateway-v12 | |
| | aviatrix-companion-gateway-v13 | aviatrix-companion-gateway-v13 </br> aviatrix-companion-gateway-v13u | |
| | aviatrix-companion-gateway-v14 | aviatrix-companion-gateway-v14 </br> aviatrix-companion-gateway-v14u | |
| | aviatrix-companion-gateway-v16 | aviatrix-companion-gateway-v16 | |
| | aviatrix-copilot | avx-cplt-byol-01, avx-cplt-byol-02 | |
| centos | centos-ci  | 7-ci | |
| | centos-hpc | 7.1, 7.3, 7.4 | |
| | centos-lvm | 7-lvm-gen2   | |
| | centos-lvm | 7-lvm, 8-lvm | |
| center-for-internet-security-inc | cis-oracle-linux-8-l1 | cis-oracle8-l1 | |
| cis-redhat | cis-redhat7-l1-gen1 | | | 
| | cis-redhat8-l1-gen1 | | | 
| | cis-redhat8-l2-gen1 | | | 
| | cis-redhat9-l1-gen1 | | | 
| | cis-redhat9-l1-gen2 | | | 
| cis-rhel | cis-rhel7-l2 | | |
| | cis-rhel8-l2 | | |
| | cis-rhel9-l1 | | |
| | cis-rhel9-l1-gen2 | * | |
| center-for-internet-security-inc | cis-ubuntu | cis-ubuntu1804-l1 </br> cis-ubuntulinux2004-l1-gen1 </br> cis-ubuntulinux2204-l1-gen1 </br> cis-ubuntulinux2204-l1-gen2 | * |
| | cis-ubuntu-linux-1804-l1 | cis-ubuntu1804-l1 | * |
| | cis-ubuntu-linux-2004-l1 | cis-ubuntu2004-l1 </br> cis-ubuntu-linux-2204-l1-gen2 | |
| | cis-ubuntu-linux-2204-l1 | cis-ubuntu-linux-2204-l1 </br> cis-ubuntu-linux-2204-l1-gen2 | |
| | debian-10-daily | 10 </br> 10-gen2 </br> 10-backports </br> 10-backports-gen2 | |
| | debian-11 | 11 </br> 11-gen2 </br> 11-backports </br> 11-backports-gen2 | |
| | debian-11-daily | 11 </br>  11-gen2 </br> 11-backports </br> 11-backports-gen2 | |
| | dns-ubuntu-2004 | dns-ubuntu-2004 | |
| | oracle-database | oracle_db_21 | |
| | oracle-database-19-3 | oracle-database-19-0904 | |
| | rhel-ha | 9_2 </br> 9_2-gen2 | |
| | rhel-sap-apps | 9_0 </br> 90sapapps-gen2 </br> 9_2 </br> 92sapapps-gen2 | |
| | rhel-sap-ha | 9_2 </br> 92sapha-gen2 | |
| | servercore-2019 | servercore-2019 | |
| | sftp-2016 | sftp-2016 | |
| | sle-hpc-15-sp4 | gen1 </br> gen2 | |
| | sle-hpc-15-sp4-byos | gen1 </br> gen2 | |
| | sle-hpc-15-sp5 | gen1 </br> gen 2 | |
| | sle-hpc-15-sp5-byos | gen1 </br> gen 2 | |
| | sles-15-sp1-sapcal | gen1 </br> gen2 | |
| | sles-15-sp2-basic | gen2 | |
| | sles-15-sp2-hpc | gen2 | |
| | sles-15-sp3-sapcal | gen1 </br> gen2 | |
| | sles-15-sp4 | gen1 </br> gen2 | |
| | sles-15-sp4-byos | gen1 </br> gen2 | |
| | sles-15-sp4-chost-byos | gen1 </br> gen 2 | |
| | sles-15-sp4-hardened-byos | gen1 </br> gen2 | |
| | sles-15-sp5 | gen1 </br> gen2 | |
| | sles-15-sp5-basic | gen1 </br> gen2 | |
| | sles-15-sp5-byos | gen1 </br> gen2 | |
| | sles-15-sp5-hardened-byos | gen1 </br> gen2 | |
| | sles-15-sp5-sapcal | gen1 </br> gen2 | |
| | sles-byos | 12-sp4 </br> 12-sp4-gen2 | |
| | sles-sap | 12-sp4 </br> 12-sp4-gen2 | |
| | sles-sap-15-sp4-byos | gen1 </br> gen2 | |
| | sles-sap-15-sp4-hardened-byos | gen1 </br> gen2 | |
| | sles-sap-15-sp5-byos | gen1 </br> gen2 | |
| | sles-sap-15-sp5-hardened-byos| gen1 </br> gen2 | |
| | sles-sap-byos | 12-sp4 </br> 12-sp4-gen2 </br> gen2-12-sp4 | |
| | sles-sapcal | 12-sp3 | |
| | sles-standard | 12-sp4-gen2 | |
| | sles| 12-sp4-gen2 | |
| | squid-ubuntu-2004 | squid-ubuntu-2004 | | 
| | ubuntu-2004 | 2004, 2004-gen2 | |
| | ubuntu-hpc | 1804 </br> 2004-preview-ndv5 </br> 2004 </br> 2204 </br> 2204-preview-ndv5 | |
| sles-15-sp5-chost-byos | gen1 | | |
| | gen2 | | |
| almalinux | almalinux </br> | 8-gen1 </br> 8-gen2 </br> 9-gen1 </br> 9-gen2 | |
| | almalinux-x86_64 | 8-gen1 </br> 8-gen2 </br> 8_7-gen2 </br> 9-gen1 </br> 9-gen2 | |
| aviatrix-systems |aviatrix-bundle-payg | aviatrix-enterprise-bundle-byol | |
| belindaczsro1588885355210 | belvmsrv01 | belvmsrv003 | | 
| canonical | * | | |
| cloud-infrastructure-services | rds-farm-2019 | rds-farm-2019 | |
| cloudera | cloudera-centos-os | 7_5 | |
| cncf-upstream | capi | ubuntu-1804-gen1 </br> ubuntu-2004-gen1 </br> ubuntu-2204-gen1 | |
| credativ | debian | 9 </br> 9-backports | |
| debian | debian-10 | 10 </br> 10-gen2 </br> 10-backports </br> 10-backports-gen2 | |
| esri | arcgis-enterprise-107 | byol-1071 | |
| | pro-byol | pro-byol-29 | |
| | arcgis-enterprise | byol-108 </br> byol-109 </br> byol-111 </br> byol-1081 </br> byol-1091 | |
| | esri | arcgis-enterprise-106 | byol-1061 |
| erockyenterprisesoftwarefoundationinc1653071250513 | rockylinux | free | |
| | rockylinux-9 | rockylinux-9 | |
| microsoft-aks | aks | aks-engine-ubuntu-1804-202112 | |
| microsoft-dsvm | aml-workstation | ubuntu-20 </br> ubuntu-20-gen2 | |
| | aml-workstation | ubuntu | |
| microsoftcblmariner | cbl-mariner | cbl-mariner-1 </br> 1-gen2 </br> cbl-mariner-2 </br> cbl-mariner-2-gen2 | |
| | cbl-mariner-1 | | | 
| | cbl-mariner-1-gen2 | | | 
| | cbl-mariner-2 | | | 
| | cbl-mariner-2-gen2 | | | 
| microsoftsqlserver | * | * | **Offers**: sql2019-sles* </br> sql2019-rhel7 </br> sql2017-rhel 7 </br></br> Example  </br> Publisher: </br> microsoftsqlserver </br> Offer: sql2019-sles12sp5 </br> sku:webARM </br></br> Publisher: microsoftsqlserver </br> Offer: sql2019-rhel7 </br> sku: web-ARM | 
| | * | *| **Offers**:  sql2019-sles*</br> sql2019-rhel7 </br> sql2017-rhel7 |
| nginxinc | nginx-plus-ent-v1 | nginx-plus-ent-centos7 | |
| ntegralinc1586961136942 | ntg_oracle_8_7 | ntg_oracle_8_7 | |
| openlogic | centos | 7.2 </br> 7.3 </br> 7.4 </br> 7.5 </br> 7.6 </br> 7_8 </br> 7_9 </br> 7_9-gen2 | |
| oracle | oracle-linux | 7* </br> ol7* </br> ol8* </br> ol9* </br> ol9-lvm* </br> 8 </br> 8-ci </br> 81 </br> 81-ci </br> 81-gen2 | |
| procomputers | almalinux-8-7 | almalinux-8-7 | |
| | rhel-8-2 | rhel-8-2 | |
| redhat | redhat | rhel-sap | 7* |
| | rhel | 7* </br> 8* </br> 8.1 </br> 89-gen2 </br> 8_9 </br> 9* | |
|| rhel-byos | rhel-lvm79 </br> rhel-lvm79-gen2 </br> rhel-lvm8 </br> rhel-lvm82-gen2 </br> rhel-lvm83 </br> rhel-lvm84 </br> rhel-lvm84-gen2 </br> rhel-lvm85-gen2 </br> rhel-lvm86 </br> rhel-lvm86-gen2 </br> rhel-lvm87-gen2 </br> rhel-raw76 </br> rhel-lvm88 </br> rhel-lvm88-gent2 </br> rhel-lvm92 </br> rhel-lvm92-gen2 | |
| | rhel-ha | 8* | 81_gen2 |
| | rhel-raw | 7* </br> 8* </br> 9* | |
| | rhel-sap | 7.4 </br> 7.7 | |
| | rhel-sap* | 9_0 | |
| | rhel-sap-apps | 90sapapps-gen2 | |
| |rhel-sap-ha | 7* </br> 8* </br> 90sapha-gen2 | |
| | sap-apps | 7* </br> 8* | |
| southrivertech1586314123192 | tn-ent-payg| Tnentpayg | |
| | tn-sftp-payg | Tnsftppayg | |
| suse | opensuse-leap-15-* | gen* | |
| | sles-12-sp5 | gen1 </br> gen2 | |
| | sles-12-sp5-* | gen* | |
| | sles-15-sp2 | gen1 </br> gen2 | |
| | sles-15-sp5 | gen2 | * |
| | sles-sap-12-sp5* | gen* | |
| | sles-sap-15-* | gen* </br> Offer: sles-sap-15-\*-byos  </br></br> **Sku**: gen\* </br> Example </br> Publisher: suse </br> Offer: sles-sap-15-sp3-byos </br> sku: gen1-ARM  | |
| | sles-sap-15-sp2-byos | gen2 | |
| talend | talend_re_image | tlnd_re | |
| thorntechnologiesllc | sftpgateway | Sftpgateway | |
| veeam | office365backup | veeamoffice365backup | |
| | veeam-backup-replication | veeam-backup-replication-v11| |
| zscaler | zscaler-private-access | zpa-con-azure | |

### Custom images

Custom images (including images uploaded to [Azure Compute gallery](/azure/virtual-machines/linux/tutorial-custom-images#overview)) which are created from below listed operating systems are supported for all Azure Update Manager operations except automatic VM guest patching. For instructions on how to use Azure Update Manager to manage updates on VMs created from custom images, see [Manage updates for custom images](manage-updates-customized-images.md). 

#### [Windows operating system](#tab/ci-win)

| Windows operating systems |
| --- |
| Windows Server 2022 |
| Windows Server 2019 |
| Windows Server 2016 |
| Windows Server 2012 R2 |
| Windows Server 2012 |
  
#### [Linux operating system](#tab/ci-lin)

| Linux operating systems |
| --- |
| Oracle Linux 7.x, 8x |
| Red Hat Enterprise 7, 8, 9 |
| SUSE Linux Enterprise Server 12.x, 15.0-15.4 |
| Ubuntu 16.04 LTS, 18.04 LTS, 20.04 LTS, 22.04 LTS |
 
---
:::zone-end

::: zone pivot="azure-arc-enabled-servers"

### Azure Arc-enabled servers

The following table lists the operating systems supported on [Azure Arc-enabled servers](/azure/azure-arc/servers/overview).

| Operating system |
| --- |
| Alma Linux 9 |
| Amazon Linux 2 (x64) |
| Amazon Linux 2023 |
| Debian 10 and 11 |
| Oracle 7.x, 8.x |
| Oracle Linux 9 |
| Red Hat Enterprise Linux (RHEL) 7, 8, 9 (x64) |
| Rocky Linux 8, 9 |
| SUSE Linux Enterprise Server (SLES) 12 and 15 (x64) |
| Ubuntu 16.04, 18.04, 20.04, and 22.04 LTS |
| Windows Server 2012 R2 and higher (including Server Core) |

:::zone-end

::: zone pivot="win-iot-arc-servers"

### Windows IoT Enterprise on Arc enabled servers (preview)

| Windows Enterprise systems |
| --- |
| Windows 10 IoT Enterprise LTSC 2021 |
| Windows 10 IoT Enterprise LTSC 2019  |
| Windows 11 IoT Enterprise, version 23H2 | 
| Windows 11 IoT Enterprise LTSC 2024  |

:::zone-end

## Next steps

- [View updates for a single machine](view-updates.md)
- [Deploy updates now (on-demand) for a single machine](deploy-updates.md)
- [Schedule recurring updates](scheduled-patching.md)
- [Manage update settings via the portal](manage-update-settings.md)
