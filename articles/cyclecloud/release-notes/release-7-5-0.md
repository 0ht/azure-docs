---
title: Azure CycleCloud Release Notes v7.5.0 | Microsoft Docs
description: Product release notes for Azure CycleCloud v7.5.0
services: azure cyclecloud
author: KimliW
ms.prod: cyclecloud
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: a-kiwels
---

# Release Notes

Azure CycleCloud v7.5.0 was released as a preview.

## New Features

* Added a more powerful machine-type selector that supports a prioritized list of sizes
* Improved user experience regarding SSH keys
* [Let's Encrypt](https://letsencrypt.org/) is now supported for getting SSL certificates
* Automatically add ssh/rdp endpoints for public nodes and nodearrays
* Account form includes inline help and validation of entered credentials
* Can now specify `AuthorizedKeyPath` on Linux virtual machines
* Cluster scaling now takes advantage of quota information to find capacity quickly
* Added tool downloads and useful resources to About page
* Now supports Azure Marketplace images that require acceptance of license terms
* Can now override accelerated networking option
* Election of Windows Azure Hybrid Licensing Benefit now supported

## Resolved Issues

* CycleCloud could not listen on port 443 for SSL
* Jetpack would leave temporary files on the system after use
* The endpoint browser couldn't list remote hosts with hostnames of `localhost`
* Creating multiple account credentials caused a UI error when editing clusters
* pogo wouldn't recognize sas tokens while listing containers
* Deployment operation messages of type string were not handled correctly when displaying errors
* PBS execute nodes were auto-stopping too quickly under some conditions
* Too many grouped nodes were allocated by autoscale
* Environments and references name validation was too restrictive
* The SSH port was not always open if Ganglia was enabled
* Data disks embedded in custom and platform images are not deleted on termination
* Storage Account creation would occacionally fail when using the `cyclecloud account create` command
* Some dropdowns on the cluster page did not update when the region was changed
* Subscription-level resources were not deleted with provider account
* HTCondor clusters did not use the autostop settings for idle termination
* Instance prices are now set for all families
* Volumes can now be used with nodearrays
* Exporting of parameters via the cli failed when using environments
* "Back to Profile" button was disabled after user settings were changed
* Cluster edit UI would only display a maximum of two specs per node
* Lockers could not be deleted
