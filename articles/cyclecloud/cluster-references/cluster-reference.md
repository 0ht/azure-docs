---
title: Azure CycleCloud Cluster Template Reference | Microsoft Docs
description: Parameter reference for cluster templates for use with Azure CycleCloud
services: azure cyclecloud
author: KimliW
ms.prod: cyclecloud
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: a-kiwels
---

# Cluster Reference

## Creating Clusters

Clusters can be defined in a template file and created using the command line interface. Another option is to create a new cluster using the web interface. To create a new cluster, click the large **+** in the bottom left corner of the page. You will be prompted to select your cluster type from the list of Cycle-supported clusters. Once selected, you will need to enter or select a number of variables for your cluster, starting with your Cloud Provider, credentials, and region. Click **Next** to continue, or save/cancel your cluster.

On the Cluster Software screen, you will select the OS of the manager and execute region for your cluster. Use the file picker to select the Cluster-Init specification file(s) to be used. You can have more than one spec file. Enter the path of the local keypair to allow CycleCloud the access required to run your job(s).

The Compute Backend parameters allow you to select the instance types needed for your application. Use the dropdown menu to select a CM Type and Execute Type. Your cluster can also be autoscaled to add hosts as needed. Check the box to autoscale, and enter an initial and maximum core count for the cluster.

Check the box for Return Proxy on the Networking screen to have the node act as a proxy for communication from the cluster to CycleCloud. Lastly, for Azure users, select a Subnet ID for your Virtual Network Configuration. Click Save to finish the cluster creation.

## Starting Clusters

Clusters are started through the web interface by clicking the “Start” button, or from the
command line with `cyclecloud start_cluster <clustername>`. By default, all nodes defined in the
cluster template will be started, but nodearrays will only start instances if the InitialCoreCount
setting is non­-zero.

## Adding and Removing Nodes

Using autoscale will cause the cluster to add or remove nodes from a nodearray based on the
current workload. You can also add or remove nodes manually when desired. To add
nodes to a cluster from the web interface, select the desired cluster and click the “Add” button.
Enter the desired number of nodes and the template (nodearray) and click “Add”. From the
command line, use `cyclecloud add_nodes <clustername> -c <count> ­-t <template>` .

To remove nodes from the web interface, select the node(s) to terminate in the Nodes tab and
click the “remove” button. To remove nodes from the command line, use `cyclecloud
remove_node <clustername nodename>`. To remove multiple nodes, filter expressions can be
provided.

## Terminating Clusters

Terminating a cluster will terminate all instances running in that cluster. Terminated
clusters through the web interface by clicking the “Terminate" link or from the
command line with `cyclecloud terminate_cluster <clustername>`.

## Reimporting Clusters

An existing cluster can be reimported by appending the `--force` argument to the command
used to import the cluster. Any configuration changes will be applied to new instances, but not
to running instances. Attributes that were previously specified in the file and are now missing will
be removed from the node definitions. Attributes that were added to nodes after the cluster was
imported will be unaffected.
