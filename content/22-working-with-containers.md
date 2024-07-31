# Working with containers

## Singularity tutorial 1

💬 In this tutorial, we will get familiar with the basic usage of Singularity
containers.

### Getting started

1. Download a test container image:

   ```bash
   wget https://a3s.fi/saren-2001659-pub/tutorial.sif
   ls -lh tutorial.sif
   ```

2. The file we downloaded is a container image. It contains all the software
   and data of the container in a single file. In this case, the container is
   very bare-bones and thus quite small, about 40 MB.
   - Actual application containers are typically larger since they also contain
     the software installation and may in some cases include reference data,
     etc.

### Basic usage

💬 There are three basic ways to run software in a Singularity container:

#### `singularity exec`

1. To execute a command inside the container, use `singularity exec`:

   ```bash
   singularity exec tutorial.sif hello_world
   ```

2. Compare the outputs of the following commands:

   ```bash
   grep "^NAME" /etc/os-release
   singularity exec tutorial.sif grep "^NAME" /etc/os-release
   ```

   - The first command is run on the host, the second command is run inside the
     container.

   💭 The tutorial container is based on Ubuntu 18.04. The host and the
   container use the same kernel, but the rest of the system can vary.

   - This means that a container can be based on a different Linux distribution
     than the host (as long as they are kernel-compatible), but it can't run a
     totally different OS, such as Windows or macOS.

##### In batch jobs

💡 `singularity exec` is the run method you would typically use in batch job
scripts.

1. Create a file called `test.sh`:

   ```bash
   nano test.sh
   ```

2. Copy the following contents into the file and replace `<project>` with your
   actual LUMI project, e.g. `project_465001234`:

   ```bash
   #!/bin/bash
   #SBATCH --job-name=test           # Name of the job visible in the queue.
   #SBATCH --account=<project>       # Choose the billing project. Has to be defined!
   #SBATCH --partition=debug         # Job queues: https://docs.lumi-supercomputer.eu/runjobs/scheduled-jobs/partitions/
   #SBATCH --time=00:01:00           # Maximum duration of the job. Max: depends of the partition. 
   #SBATCH --mem=1G                  # How much RAM is reserved for job per node.
   #SBATCH --ntasks=1                # Number of tasks. Max: depends on partition.
   #SBATCH --cpus-per-task=1         # How many processors work on one task. Max: Number of CPUs per node.
   
   singularity exec tutorial.sif hello_world
   ```

3. Submit the job to the queue with:

   ```bash
   sbatch test.sh
   ```

💡 For more information about batch jobs, see the batch jobs section.

#### `singularity run`

💬 When containers are created, a standard action called the `runscript` is
defined. Depending on the container, it may simply print out a message, or it
may launch a program or service inside the container.

💭 If you are using a container created by someone else, you will need to check
the documentation provided by the creator for details.

1. In our test container the `runscript` prints out a simple message:

   ```bash
   singularity run tutorial.sif
   ```

2. Give the container image execution rights so that you can run it directly:

   ```bash
   chmod u+x tutorial.sif
   ./tutorial.sif
   ```

3. You can see the actual script with the command:

   ```bash
   singularity inspect --runscript tutorial.sif
   ```

#### `singularity shell`

1. Open a shell inside the container:

   ```bash
   singularity shell tutorial.sif
   ```

2. Notice that the command prompt changed. You can now run any software inside
   the container interactively:

   ```bash
   hello_world
   ```

1. Exit the container with:

   ```bash
   exit
   ```

### More information

💬 This tutorial is meant as a brief introduction to get you started.

💡 For more detailed instructions, see the official
[Singularity documentation](https://docs.sylabs.io/guides/4.1/user-guide/).
