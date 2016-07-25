% Savio introductory training: Basic usage of the Berkeley Savio high-performance computing cluster
% Chris Paciorek
% August 2, 2016


# Introduction

This training session will cover the following topics:

 - System capabilities and hardware
     - Getting access to the system - FCA and condo
     - Describe various partitions and SUs
     - Disk space options (home, scratch, condo storage)
 - Logging in, data transfer, and software
     - Login nodes, compute nodes, and DTN nodes (with diagram of Savio system architecture)
     - Data transfer with examples to/from laptop, Box, Google Drive, AWS
       - SCP/SFTP
       - Globus
       - Box transfer
     - Software modules
 - Submitting and monitoring jobs
     - Sacctmgr and accounts and partitions
     - Parallelization flags
     - Low-priority queue
     - Interactive jobs
     - HTC jobs
     - Squeue to monitor jobs
 - Basic use of standard software: Python, R, MATLAB, etc., depending on participant requests
 - More information
     - How to get additional help
     - Upcoming events

We'll do this mostly as a demonstration. I encourage you to login to your account and try out the various examples yourself as we go through them.

Much of this material is based on the extensive Savio documention we have prepared and continue to prepare, available at (http://research-it.berkeley.edu/services/high-performance-computing/user-guide).

# System capabilities and hardware

- Savio is a 323-node, 6696 core Linux cluster rated at 200 peak teraFLOPS. 
   - 162 compute nodes provided by the institution for general access
   - 161 nodes contributed by researchers in the Condo program



## Getting access to the system - FCA and condo

- All regular Berkeley faculty can request 200,000 service units (roughly core-hours) per year through the [Faculty Computing Allowance (FCA)](http://research-it.berkeley.edu/services/high-performance-computing/faculty-computing-allowance)
- Researchers can also purchase nodes for their own priority access and gain access to the shared Savio infrastructure and to the ability to *burst* to additional nodes through the [condo cluster program](http://research-it.berkeley.edu/services/high-performance-computing/condo-cluster-program)

Faculty/principle investigators can allow researchers working with them to get user accounts with access to the FCA or condo resources available to the faculty member.

## Describe various partitions and SUs

Let's take a look at the hardware specifications of the computing nodes on the cluster: [see the *Hardware Configuration* section](http://research-it.berkeley.edu/services/high-performance-computing/user-guide)

The nodes are divided into several pools, called [partitions -- see the *configuration details* section](http://research-it.berkeley.edu/services/high-performance-computing/user-guide). Any job you submit must be submitted to a partition to which you have access.

## Disk space options (home, scratch, project, condo storage)

You have access to the following disk space as described [here in the *Storage and Backup section](http://research-it.berkeley.edu/services/high-performance-computing/user-guide).

When reading/writing data to/from disk, unless the amount of data is small, please put the data in your scratch space at /global/scratch/YOUR_USERNAME. 

We are also making available disk space for purchase via a new *condo storage* offering. The minimum purchase is $7000, which will provide roughly 20-25 TB for five years. 

# Logging in, data transfer, and software

## Login nodes, compute nodes, and DTN nodes (with diagram of Savio system architecture)

Savio has a few different kinds of nodes:

 - login nodes: for login and non-intensive interactive work such as job submission and monitoring, basic compilation, managing your disk space
 - data transfer nodes: for transferring data to/from Savio
 - compute nodes: for computational tasks

[show architecture diagram]

## Logging in

To login, you need to have software on your own machine that gives you access to a UNIX terminal (command-line) session. These come built-in with Mac (see Applications -> Utilities -> Terminal). For Windows, some options include Putty 

Here are instructions for [logging in](http://research-it.berkeley.edu/services/high-performance-computing/logging-savio).

You need to set up your smartphone or tablet with Google authenticator to generate one-time passwords for you.

Then to login:
```
ssh YOUR_USERNAME@hpc.brc.berkeley.edu
```

Then enter XXXXXYYYYYY where XXXXXX is your PIN and YYYYYY is the one-time password. 

If you want to be able to open programs with graphical user interfaces:
```
ssh -X YOUR_USERNAME@hpc.brc.berkeley.edu
```

You'll need X server software on your own machine (which manages the graphical windows) in this latter case, such as *eXceed* or *Xming*.

## Data transfer with examples to/from laptop, Box, Google Drive, AWS

Let's see how we would transfer files/data to/from Savio using a few different approaches. 

### SCP/SFTP

We can use the scp and sftp protocols to transfer files.

You need to use the Savio data transfer node, dtn.brc.berkeley.edu.

Linux/Mac:

```
# to Savio, while on your local machine
scp bayArea.csv paciorek@dtn.brc.berkeley.edu:~/.
scp bayArea.csv paciorek@dtn.brc.berkeley.edu:~/data/newName.csv
scp bayArea.csv paciorek@dtn.brc.berkeley.edu:/global/scratch/paciorek/.
# from Savio, while on your local machine
scp paciorek@dtn.brc.berkeley.edu:~/data/newName.csv ~/Desktop/.
```

If you can ssh to your local machine or want to transfer files to other systems on to which you can ssh, you can use the above syntax with the system you are copying from first and the system you are copying to second.

```
scp YOUR_USERNAME_SAVIO@dtn.brc.berkeley.edu:~/file.csv YOUR_USERNAME_OTHER@other.domain.edu:~/data/.
scp YOUR_USERNAME_OTHER@other.domain.edu:~/data/. YOUR_USERNAME_SAVIO@dtn.brc.berkeley.edu:~/file.csv 
```

One program you can use with Windows is WinSCP. After logging in, you'll see windows for the Savio filesystem and your local filesystem on your machine. You can drag files back and forth.

You can package multiple files (including directory structure) together using tar:
```
tar -cvzf files.tgz dir_to_zip 
# to untar later:
tar -xvzf files.tgz
```

### Globus

You can use Globus Connect to transfer data data to/from Savio (and between other resources) quickly and unattended. This is a better choice for large transfers.

For larger transfers and for making unattended transfers that will continue in the background, Globus Connect is a good option. Here are some [instructions](http://research-it.berkeley.edu/services/high-performance-computing/using-globus-connect-savio).

Globus transfers data between *endpoints*. Possible endpoints include: Savio, your laptop or desktop, NERSC, and XSEDE, among others.

Savio's endpoint is named `ucb#brc`.

If you are transferring to/from your laptop, you'll need Globus Connect Personal set up and running on your machine and you'll need to establish your machine as an endpoint. At that point you can proceed as below.

To transfer files, you open Globus at globus.org and authenticate to the endpoints you want to transfer between. You can then start a transfer and it will proceed in the background, including restarting if interrupted. 

Globus also provides a [command line interface](https://docs.globus.org/cli/using-the-cli) that will allow you to do transfers programmatically, such that a transfer could be embedded in a workflow script.


### Box transfer

Box provides **unlimited**, free, secured, and encrypted content storage of files with a maximum file size of 15 Gb to Berkeley affiliates. So it's a good option for backup and long-term storage. 

You can move files between Box and your laptop using the Box Sync app. And you can interact with Box via a web browser at http://box.berkeley.edu.

The best way to move files between Box and Savio is [via lftp as discussed here](http://research-it.berkeley.edu/services/high-performance-computing/transferring-data-between-savio-and-your-uc-berkeley-box-account). 

Here's how you logon to box via *lftp* on Savio (assuming you've set up an external password already as described in the link above):

```
ssh USERNAME@dtn.brc.berkeley.edu
lftp ftp.box.com
set ssl-allow true
user CAMPUS_USERNAME@berkeley.edu
```

```
lpwd # on Savio
ls # on box
!ls # on Savio
mkdir workshops
cd workshops # on box
lcd savio-training-intro-2016 # on savio
put foreach-doMPI.R # savio to box
get AirlineDataAll.ffData  # box to savio; 1.4 Gb in ~ 1 minute
```

One additional command that can be quite useful is *mirror*, which lets you copy an entire directory to/from Box.

```
# to upload a directory from Savio to Box 
mirror -R mydir
# to download a directory from Box to Savio
mirror mydir .
```

Be careful, because it's fairly easy to wipe out files or directories on Box.

Finally you can set up *special purpose accounts* (Berkeley SPA) so files are owned at a project level rather than by individuals.

BRC is working (long-term) on making Globus available for transfer to/from Box, but it's not available yet.

### Transfer to/from bDrive (Google Drive)

bDrive provides **unlimited**, free, secured, and encrypted content storage of files with a maximum file size of 5 Tb to Berkeley affiliates.

You can move files to and from your laptop using the Google Drive app. 

There are also some third-party tools for copying files to/from Google Drive, though I've found them to be a bit klunky. This is why I'd recommend using Box for workflows at this point. However, BRC is also working (short-term) on making Globus available for transfer to/from bDrive, though it's not available yet.

## Software modules

A lot of software is available on Savio but needs to be loaded from the relevant software module before you can use it.

```{r, modules, engine='bash', eval=FALSE}
module list  # what's loaded?
module avail  # what's available
```

One thing that tricks people is that the modules are arranged in a hierarchical (nested) fashion, so you only see some of the modules as being available *after* you load the parent module. Here's how we see the Python packages that are available.

```
which python
python

module avail
module load python/2.7.8
which python
module avail
module load numpy
python 
# import numpy as np
```

Similarly, we can see that linear algebra, FFT, and HDF5/NetCDF software is available only after loading either the intel or gcc modules.

```
module load intel
module avail
```

# Submitting and monitoring jobs

All computations are done by submitting jobs to the scheduling software that manages jobs on the cluster, called SLURM.

## Sacctmgr and accounts and partitions

When submitting a job, the main things you need to indicate are the project account you are using (in some cases you might have access to multiple accounts such as an FCA and a condo) and the partition.

You can see what accounts you have access to and which partitions within those accounts as follows:

```
sacctmgr -p show associations user=USERNAME
```

If you are part of a condo, you'll notice that you have *low-priority* access to certain partitions. For example I am part of the statistics cluster *co_stat*, which owns some Savio2 nodes and therefore I have normal access to those, but I can also burst beyond the condo and use other partitions at low-priority (see below).

In contrast, through my FCA, I have access to the savio, savio2, and big memory partitions.

## Submitting a batch job

## Parallelization flags

## Low-priority queue

## Interactive jobs

You can also do work interactively.

For this, you may want to have used the -X flag to ssh if you are running software with a GUI such as MATLAB. 

```
srun -A fc_paciorek -p savio2  -N 1 -t 10:0 --pty bash
matlab
```

## HTC jobs

## Squeue to monitor jobs

# Basic use of standard software: Python, R, MATLAB, etc., depending on participant requests

# More information

## How to get additional help

 - For technical issues and questions about using Savio: email brc-hpc-help@berkeley.edu.
 - For questions about computing resources in general, including cloud computing: email brc@berkeley.edu.
 - For questions about data management (including HIPAA-protected data): email researchdata@berkeley.edu.


## Upcoming events

 - August 9: [XSEDE Big Data training for Hadoop and Spark](http://www.psc.edu/index.php/training-category-list/2350-xsede-hpc-workshop-august-9-2016-big-data), here in this AIS room.
 - September 13: Advanced Savio training
