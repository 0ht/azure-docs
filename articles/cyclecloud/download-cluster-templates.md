---
title: Download Cluster Projects and Templates
description: Azure CycleCloud has built-in templates you can configure and edit to make your own custom templates.
author: adriankjohnson
ms.date: 07/17/2019
ms.author: adjohnso
---

# Cluster Template Downloads

Azure CycleCloud comes with built-in cluster templates that you can use out of the box, or customize to [build a template](~/how-to/cluster-templates.md) for your specific needs. For the full list of available cluster templates, please take a look at the "cyclecloud" repositories in [Microsoft Azure GitHub](https://github.com/Azure?q=cyclecloud).

Customized templates can be imported into CycleCloud using the CycleCloud CLI:

```azurecli-interactive
cyclecloud import_template -f templates/template-name.template.txt
```

## Available Template Types

| Project/Template Type  | CycleCloud Repo | Description  |     |
| --------------------- | ---------------- | ------------ | --- |
| [![Blender](~/media/index/blender.png)](https://blender.org) | [Blender](https://github.com/Azure/cyclecloud-blender) | CycleCloud project. Installs and configures Blender 3D Rendering toolkit for batch rendering; includes example cluster template that installs Blender alongside SGE. |     |
| [![BeeGFS](~/media/index/beegfs.png)](https://www.beegfs.io/content/) | [BeeGFS](https://github.com/Azure/cyclecloud-beegfs) | CycleCloud project to enable configuration, orchestration, and management of BeeGFS file systems in Azure CycleCloud HPC clusters. |     |
| [![Conda](~/media/index/conda.png)](https://anaconda.org/anaconda/conda) | [Conda](https://github.com/Azure/cyclecloud-conda) | CycleCloud project to enable use of conda/bioconda/miniconda on Azure CycleCloud HPC clusters.  |     |
| [![Azure Data Science](~/media/index/data-science.png)](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) | [Azure Data Science VM](https://github.com/Azure/cyclecloud-data-science-vm) | CycleCloud project to enable running the Azure Data Science VM Marketplace offering instance.  |     |
| [![Docker](~/media/index/docker.png)](https://docker.com) | [Docker](https://github.com/Azure/cyclecloud-docker) | CycleCloud project to enable use of Docker containers in HPC clusters. Differs from running CycleCloud in a container instance.                                      |     |
| [![Grid Engine](~/media/index/grid-engine.png)](http://gridscheduler.sourceforge.net/) | [Grid Engine](https://github.com/Azure/cyclecloud-gridengine)    | Azure CycleCloud GridEngine cluster template.  |     |
| [![no logo](~/media/index/default.png)](https://docs.microsoft.com/powershell/high-performance-computing/overview?view=hpc16-ps)  | [HPC Pack](https://github.com/Azure/cyclecloud-hpcpack) | CycleCloud project that enables use of Microsoft HPC Pack job scheduler.  |     |
| [![HTCondor](~/media/index/htcondor.png)](https://research.cs.wisc.edu/htcondor/) | [HTCondor](https://github.com/Azure/cyclecloud-htcondor)  | Azure CycleCloud HTCondor cluster template. |     |
| [![Kafka](~/media/index/kafka.png)](https://kafka.apache.org/)  | [Kafka](https://github.com/Azure/cyclecloud-kafka)  | CycleCloud project to configure and launch a basic Apache Kafka cluster.  |     |
| [![LAMMPS](~/media/index/lammps.png)](https://lammps.sandia.gov/) | [LAMMPS](https://github.com/Azure/cyclecloud-lammps)  | CycleCloud project for LAMMPS cluster type.    |     |
| [![no logo](~/media/index/default.png)](https://www.ibm.com/us-en/marketplace/hpc-workload-management) | [Spectrum LSF](https://github.com/Azure/cyclecloud-lsf) | CycleCloud project to enable use of Spectrum LSF job scheduler in Azure CycleCloud HPC clusters.  |     |
| [![MrBayes](~/media/index/mr-bayes.png)](http://mrbayes.sourceforge.net/)  | [MrBayes](https://github.com/Azure/cyclecloud-mrbayes)  |     |
| ![NFS](~/media/index/nfs.png) | [Network File System](https://github.com/Azure/cyclecloud-nfs) | CycleCloud project to enable use of NFS filers in HPC clusters in Azure.  |     |
| ![PBS Professional OSS](~/media/index/openpbs.png)  | [PBSPro](https://github.com/Azure/cyclecloud-openpbs)  | Azure CycleCloud OpenPBS cluster template.  |     |
| [![Redis](~/media/index/default.png)](https://redis.io/) | [Redis](https://github.com/Azure/cyclecloud-redis)  | CycleCloud project to configure and launch a basic Redis cluster.  |     |
| [![Singularity](~/media/index/singularity.png)](https://www.sylabs.io/)  | [Singularity](https://github.com/Azure/cyclecloud-singularity) | CycleCloud project to enable use of Singularity containers in HPC clusters in Azure. |     |
| [![slurm](~/media/index/slurm.png)](https://slurm.schedmd.com/)  | [Slurm](https://github.com/Azure/cyclecloud-slurm) | CycleCloud project to enable users to create, configure, and use Slurm HPC clusters.  |     |
| [![no logo](~/media/index/default.png)](https://www.ibm.com/ca-en/marketplace/analytics-workload-management)  | [Spectrum Symphony](https://github.com/Azure/cyclecloud-symphony) | CycleCloud project to enable use of Spectrum Symphony job scheduler in Azure CycleCloud HPC clusters.  |     |
| [![ZooKeeper](~/media/index/zookeeper.png)](https://zookeeper.apache.org/)  | [ZooKeeper](https://github.com/Azure/cyclecloud-zookeeper) | CycleCloud project to configure and launch a basic Apache ZooKeeper cluster.   |     |
