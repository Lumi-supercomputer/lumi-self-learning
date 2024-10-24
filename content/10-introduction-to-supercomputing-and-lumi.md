# Introduction to supercomputing and LUMI

Frequently, research problems that use computing can outgrow the capabilities
of the desktop or laptop computer where they started:

* A statistics student wants to cross-validate a model. This involves running
  the model 1000 times -- but each run takes an hour. Running the model on
  a laptop will take over a month! In this research problem, final results are
  calculated after all 1000 models have run, but typically only one model is
  run at a time (in **serial**) on the laptop. Since each of the 1000 runs is
  independent of all others, and given enough computers, it's theoretically
  possible to run them all at once (in **parallel**).
* A genomics researcher has been using small datasets of sequence data, but
  soon will be receiving a new type of sequencing data that is 10 times as
  large. It's already challenging to open the datasets on a computer --
  analyzing these larger datasets will probably crash it. In this research
  problem, the calculations required might be impossible to parallelize, but a
  computer with **more memory** would be required to analyze the much larger
  future data set.
* An engineer is using a fluid dynamics package that has an option to run in
  parallel. So far, this option was not used on a desktop. In going from 2D
  to 3D simulations, the simulation time has more than tripled. It might be
  useful to take advantage of that option or feature. In this research problem,
  the calculations in each region of the simulation are largely independent of
  calculations in other regions of the simulation. It's possible to run each
  region's calculations simultaneously (in **parallel**), communicate selected
  results to adjacent regions as needed, and repeat the calculations to
  converge on a final set of results. In moving from a 2D to a 3D model, **both
  the amount of data and the amount of calculations increases greatly**, and
  it's theoretically possible to distribute the calculations across multiple
  computers communicating over a shared network.

In all these cases, access to more (and larger) computers is needed. Those
computers should be usable at the same time, **solving many researchers'
problems in parallel**.

## Notes on vocabulary

### CPU

A central processing unit, or **CPU**, is perhaps one of the most important
parts of a computer. It is a processor that executes the instructions of a
computer program, i.e. performs the actual computing task. Nowadays, modern
CPUs are multi-core processors, i.e. they contain multiple subunits called
CPU **cores** that can run instructions at the same time, increasing the speed
of programs that support parallel computing.

### GPU

Graphics processing units, or **GPUs**, are massively parallel processors that
were originally developed for running computer graphics. Since computer
graphics are essentially about performing highly parallelizable linear algebra
operations that are encountered also in many other domains of computational
science, GPUs have evolved to become more widely used also for non-graphic
computing tasks. Nowadays GPUs are widely used, for example, for training AI
models and running molecular dynamics simulations.

Note, however, that not all algorithms and computational problems can make
efficient use of GPUs due to their highly parallel nature. Programming software
that can run on GPUs also requires using special programming languages or
frameworks like **CUDA** for Nvidia GPUs and **HIP** for AMD GPUs.

### Memory

When talking about memory, we usually refer to random-access memory, or
**RAM**. You can think of RAM as the short-term memory of a computer. It is
very fast compared to regular disk space (hard drive), and processing data in
memory is generally the most efficient option when running your computer
programs. Sometimes the data can, however, be so large that it does not fit
into memory, in which case some temporary files may need to be written to disk.
This can be a significant performance bottleneck. A modern consumer workstation
often has on the order of 8–64 GiB of RAM, while supercomputers may have
several TiB per node.

### Storage

With storage we refer to all disk space that can be accessed like a file
system. In broad terms, this is storage that can hold data "permanently", i.e.
data is still there even if the computer is rebooted. Computer storage come
both in local (hard drive installed on the computer) and shared flavors. A
shared file system is basically a remote server to which many computers may be
connected, and is thus commonly used in supercomputers to enable accessing data
from all parts of the system. Local disks tend to be, however, faster
than shared file systems.

### Node

You can roughly think that one **node** of a supercomputer is a single
computer. Each node in an HPC system has in principle the same components as
your own laptop or desktop workstation. Typically it contains one or more
multi-core CPUs, memory, and in some cases a fast local disk as well as one or
more GPUs.

### Cluster

When multiple nodes are connected together with a fast interconnect, we get a
**computer cluster**. Although there is no exact definition, the term
**supercomputer** is often used to refer to a very large cluster system.
The nodes of a cluster are often also connected to a shared file system to
enable easy access of data from all nodes. Without a shared file system, data
would always have to be manually copied back and forth between the node local
disks depending on which node(s) a computing task is being carried out on.

### Partition

The nodes of a cluster system are often grouped into special **partitions**
depending on their intended use or capabilities. At the coarsest level, nodes
are divided into **user access nodes** (UAN, also called **login nodes**) and
**compute nodes**. UANs are intended for light data processing and file
management, and are typically shared by hundreds of simultaneous users. It is
therefore important not to run any heavy computing tasks on the UANs. Such
workloads should instead be submitted to the compute nodes to be run as
**batch jobs**.

Compute nodes are typically further divided into different partitions depending
on the type of hardware they provide (CPU, GPU, available memory, local disk),
or what kind of restrictions have been set on their use (short/long computing
tasks, minimum/maximum number of CPU cores/GPUs allowed per job).

## LUMI

LUMI is a **pre-exascale** supercomputer, meaning that it has the capability to
perform almost 10<sup>18</sup> floating point operations per second. This is
mainly thanks to its large amount of GPUs. In addition to the GPU partition
**LUMI-G**, LUMI has several other types of partitions, such as a sizeable CPU
partition, **LUMI-C**. LUMI also has a
[**web interface**](https://www.lumi.csc.fi) that allows users to connect to
and use the supercomputer simply from a browser as an alternative to the more
traditional command-line interface.

An overview of the LUMI architecture is summarized in the table below.

| Partition | Description              | Specifications |
|-----------|--------------------------|----------------|
| LUMI-G    | GPU partition            | &bullet; 2978 nodes<br>&bullet; 4 AMD MI250X GPUs per node<br>&bullet; 64 AMD CPU cores per node<br>&bullet; 512 GiB memory per node |
| LUMI-C    | CPU partition            | &bullet; 2048 nodes<br>&bullet; 128 AMD CPU cores per node<br>&bullet; 256–1024 GiB memory per node |
| LUMI-D    | Data analytics partition | &bullet; 16 nodes<br>&bullet; 128 AMD CPU cores per node<br>&bullet; 2048–4096 GiB memory per node<br>&bullet; 312 TiB fast local disk<br>&bullet; 8 Nvidia A40 GPUs per node on 8 nodes |
| LUMI-P    | Parallel file system     | &bullet; 80 PiB Lustre storage space |
| LUMI-F    | Flash-based file system  | &bullet; 8 PiB fast Flash-based Lustre storage space |
| LUMI-O    | Object storage           | &bullet; 30 PiB object storage space |

For more details,
[see the LUMI user guide](https://docs.lumi-supercomputer.eu/hardware/).

```{keypoints}
- High Performance Computing (HPC), typically involves connecting to very large
  computing systems elsewhere in the world.
- These other systems can be used to do work that would either be impossible
  or much slower on smaller systems.
- HPC resources are shared by multiple users.
- The standard method of interacting with such systems is via a command-line
  interface. LUMI, however, also has a web interface for running applications
  with a graphical user interface on the supercomputer.
- LUMI is a powerful supercomputer equipped with a hefty GPU partition with AMD
  MI250X GPUs. LUMI also has a sizeable CPU partition, various storage
  solutions and a limited number of huge memory nodes and Nvidia GPUs for data
  analysis and visualization.
```
