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

The cluster is named ![alt text](images/server.png "server name") and can be logged onto through a secure shell (ssh) interface using a command such as ![alt text](images/ssh.png "ssh command"). Note however that the cluster is only accessible from within Trinity's firewall.

The logon credentials are not the same as those for other Linux systems on campus. To have an account made on the cluster please contact the ITS helpdesk or the cluster administrators.

## Storage on the Cluster

Several storage systems are mounted to the cluster, however not all storage available on the head/logon node is available on the remote nodes. In general, *all working files on the cluster should be kept in your user home directory or in a portion of the data directory that you have access to*.

### Home directories

The home directories on the cluster are located at `/home/username`. Note that this is different than on the CS departmental Linux systems, which are located at `/users/username`. However, the CS home directories are also mounted on the cluster to the **head node only** at `/users/username` to enable copying files back and forth between the systems.

**IMPORTANT:** *All jobs submitted to the cluster should typically be run from your cluster home directory, e.g. `/home/username`. Jobs create files containing logs and error messages into the folder that the command was run from, meaning that you must have read/write access and the folder must be mounted to the remote nodes. Even if you have write access to a folder in the data directories and you launch the job from there, the queue system will generate an error when it tries to write the log files there. So, __submit all of your jobs from within your home directory__.*

**IMPORTANT:** *The amount of space available in the home directories is limited. __No large files should be kept in the home directories.__ Instead, they should be kept in an appropriate folder in the data directories.*

### Data directories

The large RAID6 shared storage for the cluster is mounted to both the head and remote nodes at `/data`. Sub-folders of `/data` will be created for each faculty member or lab head using the cluster, such as `/data/namelab` for which the lab head will have ownership and privileges. Lab heads may then create further subfolders and assign permissions as appropriate.

**IMPORTANT:** *All large data files should be maintained within the `/data` directory.*

### Additional directories (only mounted to the head node)

Some additional file systems are also mounted to the head node, but **not to the remote nodes**, meaning that submitted jobs cannot refer to files in these directories. These filesystems are mounted to the head node to enable files to be transferred easily to or from the cluster and other systems on campus. These additional mounts include:
- `/users` corresponding to the CS Linux home directories
- `/disk1 /disk2 /disk3` corresponding to mounts in the Hibbs lab storage
- several other miscellaneous directories

**IMPORTANT:** *Files in these folders are unavailable to the remote nodes, and so cannot be referred to by any jobs submitted to the cluster.*

## Installing software on the Cluster

If administrative privileges are not required for a piece of software to be installed and if the software is unlikely to be used by multiple users, then we encourage you to build or install that software within your home directory or a `/data` directory that you have access to.

If software requires administrative privileges to be installed, or if the software is likely to be useful to a broad number of users, contact a system administrator (currently npape and mhibbs) for installation


## Submitting jobs on the Cluster

We use the [TORQUE](http://www.adaptivecomputing.com/products/open-source/torque/) resource management software to submit, schedule, and monitor jobs submitted to the cluster. This software is open source, and has several online tutorials and instruction manuals. TORQUE is built upon the [OpenPBS](https://en.wikipedia.org/wiki/Portable_Batch_System) project, which is no longer in development, but much of the syntax and tools of TORQUE derive from this origin.

In general, to run a job on the cluster, you will need to create a bash shell script file containing configuration information for your job, including how many resources your job will consume, and the command line statements that will run your job. Once this script file is created, the job can be submitted using the `qsub` command as described below.

### Creating a job submission bash shell script

Shell scripts are plain text files that contain Linux bash commands. These commands are executed from top to bottom in order. Modifiers such as I/O redirection (e.g. `> outfile`), piping, and running in the background with `&` behave as normal. Though not required, by convention these files often end in a `.sh` extension. These files should begin with the line `#!/usr/bin/sh`.

For cluster submission scripts, you should include a series of comments, called PBS directives, at the beginning of the file (before the normal Linux commands). All PBS directives begin with `#PBS`, and they are used to indicate how many resources a job requires, gives the job a name, and can optionally set up e-mail notifications for the job.

* `#PBS -N job_name` is used to specify a name for the job, which is used in several other places
* `#PBS -l nodes...` is used with additional options to specify the number and types of nodes requested, some examples are:
  * `#PBS -l nodes=1:ppn=1` requests a single core on a single node
  * `#PBS -l nodes=2:ppn=8` requests 2 nodes, and 8 cores on each node
  * `#PBS -l nodes=1:gpus=2` requests a single node with 2 GPUs
  * For the above examples, always begin with the number of nodes requested, optionally followed by the number of cores on each node (`ppn`) and the number of GPUs (`gpus`). If ppn is not specified, it defaults to 1. If gpus is not specified it defaults to 0.
* `#PBS -l walltime=72:00:00` is used to request a maximum length of time to allow the job to run, called the "wall clock time" or just "walltime". This example requests up to 72 hours of runtime. If no walltime is specified, the default walltime is 1 hour. So without specifying a walltime, if your job takes longer than 1 hour to run, it will be terminated after that hour. The maximum possible walltime that can be requested is currently limited to 168 hours (1 week). If you have a job that needs more than 1 week to run, please contact the administrators.
* `#PBS -M email@address.com` is used to specify an email address to send status updates for this job.
* `#PBS -m abe` is used to specify when to send emails related to this job. If `a` is included after the -m option, email is sent if the job is aborted, with `b` email is sent when a job begins, and with `e` email is sent when a job ends. Any combination of `a`, `b`, and `e` may be specified.
* There are many additional PBS directives which you can read about in the [documentation](http://docs.adaptivecomputing.com/torque/4-0-2/Content/topics/commands/qsub.htm).
* It is possible to specify these options (e.g. `-N job_name`) from the command line when you submit your job, but I recommend keeping them in your script files to document your job submission history and minimize errors.

An example of a job submission script that runs a tool named bowtie2 on a single node using 8 cores, with a maximum runtime of 4 hours is below. This file also gives the job the name `bowtie_job` and requests emails to be sent to somebody@gmail.com when the job ends normally or abnormally terminates.
```
#!/bin/sh

##Place PBS directives here
#PBS -N bowtie_job
#PBS -l nodes=1:ppn=8
#PBS -l walltime=4:00:00
#PBS -M somebody@gmail.com
#PBS -m ae

##Place Linux commands to run on the remote node here
bowtie2 -p 8 /data/path/to/file/dna.fq -S /data/path/to/file/output.sam
```

### Using `qsub` to submit a shell script to the job queue system

Once you have created a shell script containing your

## Maintenance of jobs on the Cluster
