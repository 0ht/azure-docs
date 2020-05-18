---
title: PBS Professional OSS Integration
description: PBS Professional OSS scheduler configuration in Azure CycleCloud.
author: KimliW
ms.date: 08/01/2018
ms.author: adjohnso
---

# PBS Professional OSS

[//]: # (Need to link to the scheduler README on Github)

[PBS Professional OSS (PBS Pro)](http://pbspro.org/) can easily be enabled on a CycleCloud cluster by modifying the "run_list" in the configuration section of your cluster definition. The two basic components of a PBS Professional cluster are the 'master' node which provides a shared filesystem on which the PBS Professional software runs, and the 'execute' nodes which are the hosts that mount the shared filesystem and execute the jobs submitted. For example, a simple cluster template snippet may look like:

``` ini
[cluster my-pbspro]

[[node master]]
    ImageName = cycle.image.centos7
    MachineType = Standard_A4 # 8 cores

    [[[configuration]]]
    run_list = role[pbspro_master_role]

[[nodearray execute]]
    ImageName = cycle.image.centos7
    MachineType = Standard_A1  # 1 core

    [[[configuration]]]
    run_list = role[pbspro_execute_role]
```

Importing and starting a cluster with definition in CycleCloud will yield a single 'master' node. Execute nodes can be added to the cluster via the `cyclecloud add_node` command. For example, to add 10 more execute nodes:

```azurecli-interactive
cyclecloud add_node my-pbspro -t execute -c 10
```

## PBS Resource-based Autoscaling

Cyclecloud maintains two resources to expand the dynamic provisioning capability. These resources are *nodearray* and *machinetype*. 

If you submit a job and specify a nodearray resource by `qsub -l nodearray=highmem -- /bin/hostname ` 
then CycleCloud will add nodes to the nodearray named 'highmem'. If there is  no such nodearray then the job will remain idle.

Similarly if a machinetype resource is specified which a job submission, e.g. `qsub -l machinetype:Standard_L32s_v2 my-job.sh`, then CycleCloud autoscales the 'Standard_L32s_v2' in the 'execute' (default) nodearray. If that machine type is not available in the 'execute' node array then the job will remain idle.

These resources can be used in combination as:

```bash
qsub -l nodes=8:ppn=16:nodearray=hpc:machinetype=Standard_HB60rs my-simulation.sh
```

which will autoscale only if the 'Standard_HB60rs' machines are specified an the 'hpc' node array.

## Adding additional queues assigned to nodearrays

On clusters with multiple nodearrays, it's common to create separate queues to automatically route jobs to the appropriate VM type. In this example, we'll assume the following "gpu" nodearray has been defined in your cluster template:

```bash
    [[nodearray gpu]]
    Extends = execute
    MachineType = Standard_NC24rs

        [[[configuration]]]
        pbspro.slot_type = gpu
```

After importing the cluster template and starting the cluster, the following commands can be ran on the master node to create the "gpu" queue:

```bash
/opt/pbs/bin/qmgr -c "create queue gpu"
/opt/pbs/bin/qmgr -c "set queue gpu queue_type = Execution"
/opt/pbs/bin/qmgr -c "set queue gpu resources_default.ungrouped = false"
/opt/pbs/bin/qmgr -c "set queue gpu resources_default.place = scatter"
/opt/pbs/bin/qmgr -c "set queue gpu resources_default.slot_type = gpu"
/opt/pbs/bin/qmgr -c "set queue gpu default_chunk.ungrouped = false"
/opt/pbs/bin/qmgr -c "set queue gpu default_chunk.slot_type = gpu"
/opt/pbs/bin/qmgr -c "set queue gpu enabled = true"
/opt/pbs/bin/qmgr -c "set queue gpu started = true"
```

> [!NOTE]
> The above queue definition will pack all VMs in the queue into a single VM scale set to support MPI jobs. To define the queue for serial jobs and allow multiple VM Scalesets, set `ungrouped = true` for both `resources_default` and `default_chunk`. You can also set `resources_default.place = pack` if you want the scheduler to pack jobs onto VMs instead of round-robin allocation of jobs. For more information on PBS job packing, see the official [PBS Professional OSS documentation](https://www.altair.com/pbs-works-documentation/).

## PBS Professional Configuration Reference

The following are the PBS Professional specific configuration options you can toggle to customize functionality:

| PBS Pro Options | Description |
| --------------- | ----------- |
| pbspro.slots                           | The number of slots for a given node to report to PBS Pro. The number of slots is the number of concurrent jobs a node can execute, this value defaults to the number of CPUs on a given machine. You can override this value in cases where you don't run jobs based on CPU but on memory, GPUs, etc.                                                               |
| pbspro.slot_type                       | The name of type of 'slot' a node provides. The default is 'execute'. When a job is tagged with the hard resource 'slot_type=<type>', that job will *only* run on a machine of the same slot type. This allows you to create different software and hardware configurations per node and ensure an appropriate job is always scheduled on the correct type of node.  |
| pbspro.version                         | Default: '18.1.3-0'. This is the PBS Professional version to install and run. This is currently the default and *only* option. In the future additional versions of the PBS Professional software may be supported. |

[!INCLUDE [scheduler-integration](~/includes/scheduler-integration.md)]

> [!NOTE]
> Even though Windows is an officially supported PBS Professional platform, CycleCloud does not support running PBS Professional on Windows at this time.