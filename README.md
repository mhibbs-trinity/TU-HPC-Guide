# Trinity University HPC cluster Guide

## Description of the Cluster

Trinity maintains a 42 node HPC (high performance computing) cluster available for use by the general university community. The cluster was obtained through the support of the National Science Foundation. Any work published or presented with the aid of the cluster should acknowledge grant award **NSF MRI-ACI-1531594** as well as Trinity University for their support of the cluster.

### Technical specification of the Cluster

The cluster consists of a "head" node which is used to log onto the cluster and is where all jobs are submitted. There are also several "worker" or "remote" nodes where the bulk of computation should occur. This includes regular CPU-focused nodes, and specialized nodes with GPUs and a node with a large amount of RAM.

* The single head/login node contains:
  * Dual Intel Xeon ES-2695 v4
    * 36 cores total (18 per CPU)
    * 2.1 GHz clock speed
  * 64 GB of DDR4-2133 ECC RAM

* 36 CPU-focused nodes each contain:
  * Dual Intel Xeon ES-2695 v4
    * 36 cores total (18 per CPU)
    * 2.1 GHz clock speed
  * 64 GB of DDR4-2133 ECC RAM

* 1 high memory node contains:
  * Dual Intel Xeon ES-2695 v4
    * 36 cores total (18 per CPU)
    * 2.1 GHz clock speed
  * 768 GB of DDR4-2133 ECC RAM

* 5 GPU-focused nodes each contain:
  * Dual Intel Xeon ES-2695 v4
    * 36 cores total (18 per CPU)
    * 2.1 GHz clock speed
  * 64 GB of DDR4-2133 ECC RAM
  * 2x NVIDIA Tesla K80m GPUs
    * 9984 CUDA cores (4992 per GPU)
    * 48 GB of GDDR5 ECC memory (24 GB per GPU)

* 1 Storage server contains:
  * Dual Intel Xeon ES-2620 v4
    * 16 cores total (8 per CPU)
    * 2.1 GHz clock speed
  * 64 GB of DDR4-2133 ECC RAM
  * 216 TB of raw HDD Storage
    * Configured as 100 TB usable RAID 6 Storage

## Logging on to the Cluster

The cluster is named !(images/server.png) and can be logged onto through a secure shell (ssh) interface using a command such as !(images/ssh.png). Note however that the cluster is only accessible from within Trinity's firewall.

The logon credentials are not the same as those for other Linux systems on campus. To have an account made on the cluster please contact the ITS helpdesk or the cluster administrators.

## Storage on the Cluster


## Submitting jobs on the Cluster


## Maintenance of jobs on the Cluster
