---
title: Release Notes
description: Product release notes for current Azure CycleCloud major release
author: adriankjohnson
ms.date: 03/12/2020
ms.author: adjohnso
---

# Azure CycleCloud 7.9

The current release is 7.9.3.

## Azure CycleCloud 7.9 Release Highlights

|  |  |
| --- | --- |
| [**IBM Spectrum LSF Support**](lsf.md)<br/>The official LSF resource connector plugin for CycleCloud is included in IBM LSF version 10.1 fp 9, and this release includes a LSF cluster template.  | ![LSF sample](./images/release-notes/lsf.png)  |
| [**Node Management**](node-configuration-reference.md)<br/>The user interface now has explicit control over operating nodes.<br/><br/>  - It is now possible to add nodes to a cluster and specify the placement groups that the nodes should be in. This gives a user a measure of control over node proximity, which is invaluable for creating an MPI ring for building or debugging MPI code.<br/><br/>  - A keep-alive toggle on each node also gives users control over a node's lifespan independently from a scheduler's autoscaling policies. This feature is useful for users who need to access a node locally for troubleshooting applications or jobs.  | [ ![Node management sample](./images/release-notes/node_management_small.png) ](./images/release-notes/node_management_large.png#lightbox) |
| [**Issue Reporting**](error_messages.md)<br/>A new issue reporting UI has been added that simplifies the diagnosis of issues that may occur in a running cluster.<br/><br/>  - Errors in nodes are now reported back into CycleCloud and displayed to a user without requiring remote access into the virtual machine, making trouble shooting easier.<br/><br/>  - The issues interface also provides links to documentation that improves self-supportability.| [ ![Issue Reporting sample](./images/release-notes/issue_reporting_small.png) ](./images/release-notes/issue_reporting_large.png#lightbox) |
| [**Ephemeral OS disks**](https://docs.microsoft.com/azure/virtual-machines/windows/ephemeral-os-disks)<br/>Ephemeral OS disks can now be used to improve virtual machine and scale sets start-up performance and cost.<br/><br/>  To use an ephemeral OS disk, add `EphemeralOSDisk = true` to a node in your cluster template.| |

## Release Notes

Comprehensive release notes for the individual 7.9.x releases are listed below

* [**7.9.3 Release Notes**](release-notes/7-9-3.md) - released on 03/12/20
* [**7.9.2 Release Notes**](release-notes/7-9-2.md) - released on 01/13/20
* [**7.9.1 Release Notes**](release-notes/7-9-1.md) - released on 12/09/19
* [**7.9.0 Release Notes**](release-notes/7-9-0.md) - released on 11/15/19

Release notes from the [previous major releases](release-notes-previous.md) and [older versions](release-notes-archive.md) are also available.
