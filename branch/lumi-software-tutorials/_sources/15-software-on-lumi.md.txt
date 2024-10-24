# Using software on LUMI

- How do I know if my software is available on LUMI?
- How do I use the software on LUMI?

This chapter introduces to key points of how to identify the available software on LUMI, and how to get these software in use. We also give examples with selected software how this works in practise. In the chapter (installing own applications) we give instruction how to install other software that is not available via the supported EasyBuild recipes by the LUMI user support team, or by LUMI local organizations. 


For a more comprehensive look to modules and software on LUMI (for people who already understand something about these concepts) see the LUMI documentation of [Lmod modules](https://docs.lumi-supercomputer.eu/runjobs/lumi_env/Lmod_modules/), [software stacks](https://docs.lumi-supercomputer.eu/runjobs/lumi_env/softwarestacks/) and [using software on LUMI](https://docs.lumi-supercomputer.eu/software/).


## What software there are available on LUMI?

To see the available software on LUMI, one doesn't need to login to the LUMI supercomputer. To see the software available in the central LUMI software collection, just open the [LUMI software library](https://lumi-supercomputer.github.io/LUMI-EasyBuild-docs/) page, which lists all the software that are either _pre-installed_ to the system, or easily _user installable_.

Let's take a few examples. 

First, let's search Gnuplot, which is a simple graphics software, and see the page about it.
When we open the page about Gnuplot, we see that there is a green box that reads `pre-installed`. This means that the software is already available in the system, and one can directly use the typical Lmod commands (e.g. `module load`) with it. (We'll see a bit later how the Lmod commands work in practise, and what the term _pre-installed_ means.)

As a second example, let's take a look at the page of Gromacs, a software for molecular dynamics. On this page we have a blue box that reads `user installable`. This means that Lmod can't directly find the software on LUMI, but user needs to _install_ it first. This installation is not a difficult process, and only requires typing few commands. We'll come back to how to do this with specific examples later in this guide. 

Besides the software available in the central LUMI software collection, there are software collections available by local LUMI organizations. Currently (April 2024) there is available an additional software stack by CSC, Finland. To see these available software via this collection, visit the [page about local software collections](https://docs.lumi-supercomputer.eu/software/local/csc/) in the LUMI documentation. 

## Good to know: Software stacks on LUMI

A software stack is a predetermined set of modules, libraries and compilers, and everything that is needed to run applications on the system. On LUMI there are a couple of different software stacks available. These can be considered as different approaches with a little bit different features. Also there can be different versions of these software stacks available at the same time. It depends on your use case, and perhaps your previous experience with these approaches, which software stacks you wish to use. 

As LUMI is a HPE Cray machine, the basis of all the software stacks and environments on LUMI is a Cray environment. This is what you get as a default, once you login to LUMI. It is recommended to load one of the available software stacks on top of this. 

The main software stack on LUMI is the `LUMI software stack`, which is maintained and supported by the LUMI user support team. There are several versions of it available at the same time, as the components of the software stacks regularily get updates. Old software stack versions always get disabled at some point, and new ones are added.

In the following examples in this guide we will be using a version of the main software stack on LUMI, i.e. version of the "LUMI software stack". 

The figure below drafts the available software stacks, when one logins to LUMI. At first there is the Basic Cray Environment. On top of that one is recommended to load either the CrayEnv, a version of the LUMI software stacks, or a version of the Spack package manager. 

```
Login
|
Basic Cray Environment
|
|------ CrayEnv
|      
|------ LUMI stacks (LUMI/22.12, LUMI/23.09, ...)
|      
`------ Spack (Spack/23.03, Spack/23.09, ...)

```

[`The Cray environment`](https://docs.lumi-supercomputer.eu/runjobs/lumi_env/softwarestacks/#crayenv) (CrayEnv) contains some additional tools on top of the default environemnt what you get at login.

[`The LUMI stacks`](https://docs.lumi-supercomputer.eu/runjobs/lumi_env/softwarestacks/#lumi) are the main software stacks on LUMI that is build on top of the basic Cray environment using EasyBuild.
This is what we will be using in the following examples. 

[`Spack`](https://docs.lumi-supercomputer.eu/software/installing/spack/) is available, but it is only recommended if you are already familiar with using Spack. It is offered "as-is". No support with developing or debugging Spack modules is provided by the LUMI user support team. 

This is just a very brief description. In case you would like to know more, please see the [LUMI documentation](https://docs.lumi-supercomputer.eu/runjobs/lumi_env/softwarestacks/). 


## Modules on LUMI

Let's now login to LUMI and start experimenting what we see and what's available.

1. Log in to LUMI with your user credentials (SSH or [LUMI web interface](https://www.lumi.csc.fi)):

```bash
ssh -i <path-to-private-key> <username>@lumi.csc.fi    # replace <username> with your LUMI username, e.g. myname@lumi.csc.fi
                                                       # replace <path-to-private-key> with the path to your private key file, e.g. 
                                                       # ./.ssh/id_ed25519 or /home/user/.ssh/id_ed25519
```


2. Try a `module` command! Check out which modules are loaded as default as you login to LUMI:

```bash
module list
```

The modules you see listed are both _installed_ to the system, and currently _loaded_, which means that they are ready for use. This is the default environment when you open a new session on LUMI, before you do, or load, anything else, and contains some initial/default settings for libraries, compiling environment, etc.

In your regular use of LUMI, the next step after login would usually be loading one version of the available software stacks. Which one, depends on what we would like to do on LUMI. 

One example workflow would be that as a first after login we would load a version of LUMI software stacks (e.g. `LUMI/23.09`). Then we would load a suitable partition for our use case (e.g. `partition/C` that is suitable for the LUMI-C compute nodes). (The partitions are there to add some settings on top of the software stack, depending on which types of nodes we are using: login nodes, LUMI-C nodes, or LUMI-G nodes.) Then we would load our software. If the software is not yet installed (usually by you or by one of your group members to your `/project/project_46XXXXXXX/` location on the LUMI file system), we install the software first. 


```bash

Login (Basic Cray environment loaded as default)
|
|-------------- CrayEnv
|               `----- (Load or install your environments/packages)
|      
|-------------- LUMI/22.12
|
|-------------- LUMI/23.03
|           
|-------------- LUMI/23.09
|                  |
|                  |---- partition/L
|                  |---- partition/C
|                  `---- partition/G
|                        `------ Load or install your software (e.g. w/ EasyBuild) 
|
|-------------- Spack/23.03
|
`-------------- Spack/23.09
                

```

To see other modules that are currently installed and available to be loaded (but not yet loaded to use), one can use the command `module avail`. The output lists everything that is currently available to be loaded, so it's quite a long list. (Typing this opens an output with the `less` tool, which you can scroll e.g. with the keyboard arrow kyes, and exit with pressing `q` from the keyboard.)

To search if a specific software is directly availabe to load, one can use the command `module spider <software_name>` . E.g. to see, if we have the simple text editor `Nano` available to load:

```bash
module spider nano
```
The output is an overview of versions of Nano that are available to be loaded. In general it's good to use the latest versions of a software available, unless one has a reason to use an older version. Let's see more information of some of the Nano versions:

```bash
module spider nano/7.2
```

We get an output that gives us some information about this software or the version of it, and what we need to do first to load this version of Nano, if anything. 
We see that this version of Nano is available in the `CrayEnv` software stack, and also in several versions of the main `LUMI software stacks`. 

What it comes to the Nano editor itself, it doesn't really matter which version of software stacks and which _partition_ we choose. This is relevant what it comes to other things we are doing on LUMI besides using the Nano editor (since probably you didn't apply compute time on LUMI to use the Nano editor, but rather are using Nano in addition to some other workflow). In some cases it matters how a software is optimized or compiled, and all the software one is using in the same workflow should be optimized or compiled the way that it is compatible. This is the reason why there are so many different versions for such a simple tool as Nano available, for example. 

From the output of available prerequisites that we just got with `module spider nano/7.2` we choose **one line**. Let's now choose the latest (on May 2024) of the available LUMI software stacks `LUMI/23.09`. Besides this let's choose to use the `partition/L` that just means a set of settings optimized for the _login nodes_ on LUMI. Because we are just going to test Nano on a login node where we are now, and not submitting jobs e.g. to LUMI-C or LUMI-G and using Nano interactively from a compute node for example, we are happy with settings optimized for the login nodes. 

At this point we are still with the same environment than which we had right after login to LUMI. If we do the `module list` the listing is exactly the same as earlier. 

---

Let's see the currently available versions of `LUMI` software stacks: 

Type `module load LUMI` and press the tabulator key twice. The output you see should be something like:

`LUMI  LUMI/22.08  LUMI/22.12  LUMI/23.03  LUMI/23.09`

The versions of the LUMI software stacks are named based on a year and a month that the corresponding version of a Cray software stack was reliesed.

Now let's not load any version of the LUMI software stacks just yet. Usually it's good to load a version of the software stacks after one knows which version we need, and we'll get to that point soon. 

When loading e.g. a LUMI software stak version, if you don't specify the version in the module load command (i.e. you would do only `module load LUMI` and press enter), a default version is loaded. But it is recommended to also provide the version of the software stacks in the module load command, as we are doing above, as the default version may change. Also when specifying the version, you always know what you are actually loading, and what other modules or EasyBuild installations are compatible with it. 

---






## Tutorial: Load the Nano editor and write a simple script


Work in progress

Maybe a simple example like this would be good here.
It could be some other than Nano, but should be something that is there that can be just directly loaded without the installation step. 


## Tutorial: Install a software with EasyBuild

Let's use here an example software called 'eb-tutorial'. This is an example software that has no real practical use, but can be used in exercises as an example of a software, when getting to know EasyBuild. 
First let's check the available versions of the software from the [LUMI software library](https://lumi-supercomputer.github.io/LUMI-EasyBuild-docs/). 
At `e` we find the [eb-tutorial](https://lumi-supercomputer.github.io/LUMI-EasyBuild-docs/e/eb-tutorial/). We see from the blue box on top that it is `user-installable`, which means that we can install it with EasyBuild. 

Let's scroll down a bit on the page, to the list of available EasyConfigs. It is in general a good idea to use the latest available installation, unless there are other restrictions (e.g. one needs a certain version of a software, or because of compatibility reasons). 
We see that there is available one version of this software `1.0.1`. It is available as six different toolchains (May 19th 2024). The version of the compiler (GNU, Cray, AOCC) could have a meaning for us, but because we don't now care how the software is compiled, we choose as an example the one that is compiled with the GNU compiler (cpeGNU). Because we don't have other restrictions, we choose the installation made to the latest LUMI sotware stack that is available, the 22.12. 

(A picture here about the text `eb-tutorial-1.0.1-cpeGNU-22.12.eb` where the software name, version, and eb-toolchain are marked?)

=> We will install the `eb-tutorial-1.0.1-cpeGNU-22.12.eb` version of the eb-tutorial software. 

(Some boxes with different colours next? To separate the blocks that one needs to do only once, and the ones that needs to be done every time on LUMI?)

### Installation

1) Decide where you want to install the software. The default location is your home folder $HOME/EasyBuild. The software will be automatically installed there, if you don't change anything. However, it's in general a better practise to install the software in your /project folder, which is shared by everyone in your project. If the software is istalled there, all of the members of your project can use the installed software that one of the project members has installed. 

Now let's change the location for EasyBuild installations to the /project folder of your project. 

Note, that this change needs to be done before you load any version of the LUMI sotware stacks. 

Open a new shell on LUMI (e.g. logout, and login to LUMI again, or open a new LUMI session in another shell window)

Go to your home folder:

`cd`

List the content of your home folder with a command that also shows the hidden files:

`ls -a`

Open the `.bashrc` file e.g. with

`nano .bashrc`

Add a following line somewhere in the file

```bash
export EBU_USER_PREFIX=/project/project_465000000/EasyBuild # Replace the project number with your project number
```

Save the changes to the `.bashrc` file with `Ctrl + s` and exit nano with `Ctrl + x` .

2) Next let's load the version of LUMI software stack that corresponds to our software version:

`module load LUMI/22.12`

3) Next we will decide which `partition` we will use the software with. If we don't choose anything here, the default is `partition/L`, which means a set of settings that are optimized for the login nodes of LUMI. If we would be using this software on the compute nodes of LUMI-C, we would choose the `partition/C`. If the softare would be used on the LUMI-G nodes, we would load the `partition/G` . For the example let's say that our software 'eb-tutorial' is something that we want to run on the LUMI-C compute nodes. In that case we install the software with settings that are optimized for the LUMI-C compute nodes:

`module load partition/C`

4) To install a software with EasyBuild, we will need to load a module that does the installation:

`module load EasyBuild-user`

This is loaded only when we are installing the software, not anymore later when we are using the software. 

5) Now the software is installed with a command:

```bash
eb eb-tutorial-1.0.1-cpeGNU-22.12.eb -r #The -r tells to also install possible dependencies
```

Depending on the software, and if it needs to first install several dependencies, the installation can take a while. 

6) Once the installation is ready, the software has appeared to us on LUMI as it would be installed in the central software stack. We can take it into use with:

`module load eb-tutorial`

### Usage of the software

When you later login to LUMI again and are using the software next times, you (or your group members) only need to do the steps:

```bash
module load LUMI/22.12
module load partition/C
module load eb-tutorial
```

## Tutorial: PyTorch with DDP

### Setup

Run through the following three steps to initialize a PyTorch environment on LUMI. This only needs to be done once.

#### 1st Step

Install ROCm Communication Collectives Library (RCCL) if you want to leverage multiple GPUs:

```console
$ module load LUMI/22.08 partition/G
$ module load EasyBuild-user
$ eb aws-ofi-rccl-66b3b31-cpeGNU-22.08.eb -r
```

#### 2nd Step

Get the ROCm PyTorch Docker container:

```console
$ SINGULARITY_TMPDIR=/tmp/tmp-singularity SINGULARITY_CACHEDIR=/tmp/tmp-singularity singularity pull docker://rocm/pytorch:rocm5.7_ubuntu22.04_py3.10_pytorch_2.0.1
```

#### 3rd Step

Create a virtual environment (venv) for each of your projects and add additional modules you might need:

```console
$ singularity exec pytorch_rocm5.7_ubuntu22.04_py3.10_pytorch_2.0.1.sif bash
Singularity> python -m venv my_project_env --system-site-packages
Singularity> . my_project_env/bin/activate
(my_project_env) Singularity> Install what else you'd need: pip install ...
```

```{callout} Create more virtual environments
You might want to create more virtual environments, one for each of your projects, or for experimenting with different module versions. Simply repeat the commands with providing individual virtual environment names.
```

### Use it

We use the following job script (`run_sbatch.sh`):
```
#!/bin/bash -l
#SBATCH --job-name="PyTorch MNIST"
#SBATCH --output=output.txt
#SBATCH --error=error.txt
#SBATCH --partition=standard-g
#SBATCH --nodes=2
#SBATCH --ntasks=16
#SBATCH --ntasks-per-node=8
#SBATCH --time=1:00:00
#SBATCH --account=project_<YOURID>
#SBATCH --gpus-per-node=8

module load LUMI/22.08 partition/G
module load singularity-bindings
module load aws-ofi-rccl

export SCRATCH=$PWD
export NCCL_SOCKET_IFNAME=hsn
export NCCL_NET_GDR_LEVEL=3
export MIOPEN_USER_DB_PATH=/tmp/${USER}-miopen-cache-${SLURM_JOB_ID}
export MIOPEN_CUSTOM_CACHE_DIR=${MIOPEN_USER_DB_PATH}
export CXI_FORK_SAFE=1
export CXI_FORK_SAFE_HP=1
export FI_CXI_DISABLE_CQ_HUGETLB=1
export SINGULARITYENV_LD_LIBRARY_PATH=${EBROOTAWSMINOFIMINRCCL}/lib:/opt/cray/xpmem/2.5.2-2.4_3.50__gd0f7936.shasta/lib64:${SINGULARITYENV_LD_LIBRARY_PATH}

master_addr=$(scontrol show hostnames "$SLURM_JOB_NODELIST" | head -n 1)
export SINGULARITYENV_MASTER_ADDR="$master_addr"
export SINGULARITYENV_MASTER_PORT=6200
echo "MASTER_ADDR="$SINGULARITYENV_MASTER_ADDR "MASTER_PORT="$SINGULARITYENV_MASTER_PORT

srun --mpi=cray_shasta singularity exec --pwd /work --bind $SCRATCH:/work pytorch_rocm5.7_ubuntu22.04_py3.10_pytorch_2.0.1.sif /work/my_project_env/bin/python PyTorch_MNIST-DDP.py
```

This job script executes 16 processes over two GPU nodes. That is, one process for each of the eight GPUs in a single node. The first node is used as master node for NCCL/RCCL communication management with port 6200.

```{callout} Using the project scratch volume
Replace `project_<YOURID>` with your project ID. Consider to use the working directory `/scratch/project_<YOURID>` for running your experiemnts. Your virtual environment should also be located there.
```

Place the following `PyTorch_MNIST-DDP.py` script into your working directory:
```
import os
from tqdm import tqdm

import torch
from torch import nn, optim
from torch.nn.parallel.distributed import DistributedDataParallel
from torch.utils.data import DataLoader, DistributedSampler
from torchvision import datasets, transforms

batch_size = 128
epochs = 5

use_gpu = True
dataset_loc = './mnist_data'

#rank = int(os.environ['OMPI_COMM_WORLD_RANK'])
#local_rank = int(os.environ['OMPI_COMM_WORLD_LOCAL_RANK'])
#world_size = int(os.environ['OMPI_COMM_WORLD_SIZE'])
rank = int(os.environ['SLURM_PROCID'])
local_rank = int(os.environ['SLURM_LOCALID'])
world_size = int(os.environ['SLURM_NTASKS'])

#os.environ["MASTER_ADDR"] = "127.0.0.1"
#os.environ["MASTER_PORT"] = "6108"
print(os.environ["MASTER_ADDR"], os.environ["MASTER_PORT"])

if rank == 0:
    print(torch.__version__)

print(local_rank, rank, world_size)
torch.cuda.set_device(local_rank)
torch.distributed.init_process_group(backend="nccl", rank=rank, world_size=world_size)

transform = transforms.Compose([
    transforms.ToTensor() # convert and scale
])

if rank == 0:
    train_dataset = datasets.MNIST(dataset_loc,
                                   download=True,
                                   train=True,
                                   transform=transform)
    test_dataset = datasets.MNIST(dataset_loc,
                                  download=True,
                                  train=False,
                                  transform=transform)
torch.distributed.barrier()
if rank != 0:
    train_dataset = datasets.MNIST(dataset_loc,
                                   download=False,
                                   train=True,
                                   transform=transform)
    test_dataset = datasets.MNIST(dataset_loc,
                                  download=False,
                                  train=False,
                                  transform=transform)

train_sampler = DistributedSampler(train_dataset,
                                   num_replicas=world_size, # number of all GPUs
                                   rank=rank,               # (global) ID of GPU
                                   shuffle=True,
                                   seed=42)
test_sampler = DistributedSampler(test_dataset,
                                 num_replicas=world_size, # number of all GPUs
                                 rank=rank,               # (global) ID of GPU
                                 shuffle=False,
                                 seed=42)

train_loader = DataLoader(train_dataset,
                          batch_size=batch_size,
                          shuffle=False, # sampler does it
                          num_workers=4,
                          sampler=train_sampler,
                          pin_memory=True)
val_loader =   DataLoader(test_dataset,
                          batch_size=batch_size,
                          shuffle=False,
                          num_workers=4,
                          sampler=test_sampler,
                          pin_memory=True)

def create_model():
    model = nn.Sequential(
        nn.Linear(28*28, 128),  # Input: 28x28(x1) pixels
        nn.ReLU(),
        nn.Dropout(0.2),
        nn.Linear(128, 128),
        nn.ReLU(),
        nn.Linear(128, 10, bias=False)  # Output: 10 classes
    )
    return model

model = create_model()

if use_gpu:
    device = torch.device("cuda:{}".format(local_rank))
    model = model.to(device)

model = DistributedDataParallel(model, device_ids=[local_rank], output_device=local_rank)

optimizer = optim.SGD(model.parameters(), lr=0.01)
loss = nn.CrossEntropyLoss()

# Main training loop
for i in range(epochs):
    model.train()
    #train_loader.sampler.set_epoch(i)

    # Training steps per epoch
    epoch_loss = 0
    pbar = tqdm(train_loader)
    for x, y in pbar:
        if use_gpu:
            x = x.to(device, non_blocking=True)
            y = y.to(device, non_blocking=True)

        x = x.view(x.shape[0], -1) # flatten
        optimizer.zero_grad()
        y_hat = model(x)
        batch_loss = loss(y_hat, y)
        batch_loss.backward()
        optimizer.step()
        batch_loss_scalar = batch_loss.item()
        epoch_loss += batch_loss_scalar / x.shape[0]
        pbar.set_description(f'training batch_loss={batch_loss_scalar:.4f}')

    # Run validation at the end of each epoch
    with torch.no_grad():
        model.eval()
        val_loss = 0
        pbar = tqdm(val_loader)
        for x, y in pbar:
            if use_gpu:
                x = x.to(device, non_blocking=True)
                y = y.to(device, non_blocking=True)

            x = x.view(x.shape[0], -1) # flatten
            y_hat = model(x)
            batch_loss = loss(y_hat, y)
            batch_loss_scalar = batch_loss.item()

            val_loss += batch_loss_scalar / x.shape[0]
            pbar.set_description(f'validation batch_loss={batch_loss_scalar:.4f}')

    if rank == 0:
        print(f"Epoch={i}, train_loss={epoch_loss:.4f}, val_loss={val_loss:.4f}")

if rank == 0:
    torch.save(model.state_dict(), 'model.pt')
```

Execute the job script in the directory of your project which contains the virtual environment:

```console
$ sbatch run_sbatch.sh
```

Refer to the `output.txt` and `error.txt` files for the results.

## Tutorial: Tensorflow with Horovod

### Setup

Run through the following three steps to initialize a Tensorflow environment on LUMI. This only needs to be done once.

#### 1st Step

Install ROCm Communication Collectives Library (RCCL) and OpenMPI if you want to leverage multiple GPUs:

```console
$ module load LUMI/22.08 partition/G
$ module load EasyBuild-user
$ eb aws-ofi-rccl-66b3b31-cpeGNU-22.08.eb -r
$ eb OpenMPI-4.1.3-cpeGNU-22.08.eb -r
```

#### 2nd Step

Get the ROCm Tensorflow Docker container:

```console
$ eb singularity-bindings-system-cpeGNU-22.08.eb -r
$ SINGULARITY_TMPDIR=/tmp/tmp-singularity SINGULARITY_CACHEDIR=/tmp/tmp-singularity singularity pull docker://rocm/tensorflow:rocm5.5-tf2.11-dev
```

#### 3rd Step

Install `ensurepip` first by executing the following script (`download_ensurepip.sh`)
```
base_url=https://raw.githubusercontent.com/python/cpython/3.9/Lib
files=("__init__.py" \
  "__main__.py" \
  "_uninstall.py" \
  "_bundled/__init__.py" \
  "_bundled/pip-23.0.1-py3-none-any.whl" \
  "_bundled/setuptools-58.1.0-py3-none-any.whl")
for _f in ${files[@]}; do
  f=ensurepip/${_f}
  if test ! -f "$f"; then
    wget -q --show-progress ${base_url}/${f} -P $(dirname $f);
  else
    echo -e "?${f}? already exists. Nothing to do."
  fi
done
```

with:
```console
$ cd $HOME
$ bash download_ensurepip.sh
```

```{callout} Adjust for your versions
Depending on the Python environment you chose to use (as part of the Tensorflow Docker container), you might need to adjust the file names. E.g., the version of `pip` can be different. Please look at `https://github.com/python/cpython` in the `Lib/ensurepip` directory for the Python version (branch) used in the Docker conatiner.
```

Create a virtual environment (venv) for each of your projects and add additional modules you might need:

```console
$ singularity exec -B $HOME/tensorflow/ensurepip:/usr/lib/python3.9/ensurepip tensorflow_rocm5.5-tf2.11-dev.sif  bash
Singularity> python -m venv my_project_env --system-site-packages
Singularity> . my_project_env/bin/activate
(my_project_env) Singularity> pip install tensorflow-datasets # Needed for later
(my_project_env) Singularity> Install what else you'd need: pip install ...
```

```{callout} Create more virtual environments
You might want to create more virtual environments, one for each of your projects, or for experimenting with different module versions. Simply repeat the commands with providing individual virtual environment names.
```

#### 4th Step

Build and install Horovod. Use a clean environment for this!

```{callout} Use a clean environment
Do not load `aws-ofi-rccl` or `singularity-bindings` modules before building Horovod as they interfere with the build process!
```

```console
$ module load LUMI/22.08 partition/G
$ module load EasyBuild-user
$ singularity exec tensorflow_rocm5.5-tf2.11-dev.sif  bash
Singularity> . ./my_project_env/bin/activate
(my_project_env) Singularity> export HOROVOD_WITHOUT_MXNET=1
(my_project_env) Singularity> export HOROVOD_WITHOUT_PYTORCH=1
(my_project_env) Singularity> export HOROVOD_WITH_TENSORFLOW=1
(my_project_env) Singularity> export HOROVOD_GPU=ROCM
(my_project_env) Singularity> export HOROVOD_GPU_OPERATIONS=NCCL
(my_project_env) Singularity> export HOROVOD_ROCM_PATH=/opt/rocm
(my_project_env) Singularity> export HOROVOD_RCCL_HOME=/opt/rocm/rccl
(my_project_env) Singularity> export RCCL_INCLUDE_DIRS=/opt/rocm/rccl/include
(my_project_env) Singularity> export HOROVOD_RCCL_LIB=/opt/rocm/rccl/lib
(my_project_env) Singularity> export HCC_AMDGPU_TARGET=gfx90a
(my_project_env) Singularity> export HIP_PATH=/opt/rocm

(my_project_env) pip install --no-cache-dir --force-reinstall horovod==0.28.1
```

### Use it

We use the following job script (`run_sbatch.sh`):
```
#!/bin/bash -l
#SBATCH --job-name="PyTorch MNIST"
#SBATCH --output=output.txt
#SBATCH --error=error.txt
#SBATCH --partition=standard-g
#SBATCH --nodes=2
#SBATCH --ntasks=16
#SBATCH --ntasks-per-node=8
#SBATCH --time=1:00:00
#SBATCH --account=project_<YOURID>
#SBATCH --gpus-per-node=8

module load LUMI/22.08 partition/G
module load singularity-bindings
module load aws-ofi-rccl
module load OpenMPI/4.1.3-cpeGNU-22.08

export SCRATCH=$PWD
export NCCL_SOCKET_IFNAME=hsn
export MIOPEN_USER_DB_PATH=/tmp/${USER}-miopen-cache-${SLURM_JOB_ID}
export MIOPEN_CUSTOM_CACHE_DIR=${MIOPEN_USER_DB_PATH}
export CXI_FORK_SAFE=1
export CXI_FORK_SAFE_HP=1
export FI_CXI_DISABLE_CQ_HUGETLB=1
export SINGULARITYENV_LD_LIBRARY_PATH=/openmpi/lib:/opt/rocm/lib:${EBROOTAWSMINOFIMINRCCL}/lib:/opt/cray/xpmem/2.5.2-2.4_3.50__gd0f7936.shasta/lib64:$SINGULARITYENV_LD_LIBRARY_PATH

scontrol show hostname $SLURM_JOB_NODELIST > hostfile
mpirun -np 16 -hostfile hostfile singularity exec --pwd /work --bind $SCRATCH:/work tensorflow_rocm5.5-tf2.11-dev.sif /work/my_project_env/bin/python keras_horovod_example.py
```

This job script executes 16 processes over two GPU nodes. That is, one process for each of the eight GPUs in a single node. This uses MPI for management/communication across the processes/nodes.

```{callout} Using the project scratch volume
Replace `project_<YOURID>` with your project ID. Consider to use the working directory `/scratch/project_<YOURID>` for running your experiemnts. Your virtual environment should also be located there.
```

Place the following `keras_horovod_example.py` script into your working directory:
```
import os
import time
import datetime
import tensorflow as tf
import tensorflow.compat.v2 as tf
import tensorflow_datasets as tfds
import horovod.tensorflow.keras as hvd

batch_size = 32
epochs = 4

# Initialize Horovod
hvd.init() # HVD

physical_devices = tf.config.list_physical_devices()
print(physical_devices)

tfds.disable_progress_bar()
tf.enable_v2_behavior()

gpus = tf.config.experimental.list_physical_devices('GPU')
print("Num GPUs available: ", len(gpus))
local_gpu =  hvd.local_rank() #HVD
if gpus:
    try:
        tf.config.set_visible_devices(gpus[local_gpu], 'GPU')
        for gpu in gpus:
            tf.config.experimental.set_memory_growth(gpu, True)
    except RuntimeError as e:
        print(e)

import urllib3
urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

(ds_train, ds_test), ds_info = tfds.load(
    'mnist',
    split=['train', 'test'],
    shuffle_files=True,
    as_supervised=True,
    with_info=True,
)

ds_train.apply(tf.data.experimental.assert_cardinality(ds_info.splits['train'].num_examples))
ds_test.apply(tf.data.experimental.assert_cardinality(ds_info.splits['test'].num_examples))

num_steps_train = ds_info.splits['train'].num_examples // batch_size
num_steps_test = ds_info.splits['test'].num_examples // batch_size
print("Total number of training steps for training/testing: {}/{}".format(num_steps_train, num_steps_test))

def normalize_img(image, label):
  """Normalizes images: `uint8` -> `float32`."""
  return tf.cast(image, tf.float32) / 255., label

ds_train = ds_train.map(
    normalize_img, num_parallel_calls=tf.data.experimental.AUTOTUNE)
ds_train = ds_train.shuffle(ds_info.splits['train'].num_examples)
ds_train = ds_train.batch(batch_size)
ds_train = ds_train.shard(hvd.size(), hvd.rank()) # HVD
ds_train = ds_train.cache()
ds_train = ds_train.prefetch(tf.data.experimental.AUTOTUNE).repeat()

ds_test = ds_test.map(
    normalize_img, num_parallel_calls=tf.data.experimental.AUTOTUNE)
ds_test = ds_test.batch(batch_size)
ds_test = ds_test.shard(hvd.size(), hvd.rank()) # HVD
ds_test = ds_test.cache()
ds_test = ds_test.prefetch(tf.data.experimental.AUTOTUNE).repeat()


def create_model():
    inp = tf.keras.Input(shape=[28, 28, 1])
    flat = tf.keras.layers.Flatten(input_shape=(28, 28, 1))(inp)
    dense1 = tf.keras.layers.Dense(128,activation='relu')(flat)
    dense2 = tf.keras.layers.Dense(10, activation='softmax')(dense1)
    return tf.keras.Model(inp, dense2)

fmodel = create_model()

callbacks = [
    # Horovod: broadcast initial variable states from rank 0 to all other processes.
    # This is necessary to ensure consistent initialization of all workers when
    # training is started with random weights or restored from a checkpoint.
    hvd.callbacks.BroadcastGlobalVariablesCallback(0), # HVD
]

opt = tf.optimizers.Adam(0.001 * hvd.size()) # HVD
opt = hvd.DistributedOptimizer(opt) # HVD

# Horovod: Specify `experimental_run_tf_function=False` to ensure TensorFlow
# uses hvd.DistributedOptimizer() to compute gradients.
fmodel.compile(
    loss='sparse_categorical_crossentropy',
    optimizer=opt,
    metrics=['accuracy'],
    experimental_run_tf_function=False # HVD
)

# Horovod: save checkpoints only on worker 0 to prevent other workers from corrupting them.
if hvd.rank() == 0: # HVD
    callbacks.append(tf.keras.callbacks.ModelCheckpoint('./checkpoint-{epoch}.h5'))

if hvd.rank() == 0:
    print("Number of processes: {}".format(hvd.size()))

time_s = time.time()
fmodel.fit(
    ds_train,
    steps_per_epoch=num_steps_train // hvd.size(), # HVD
    epochs=epochs,
    validation_data=ds_test,
    validation_steps=num_steps_test // hvd.size(), # HVD
    callbacks=callbacks,
    verbose=1 if hvd.rank() == 0 else 0)

if hvd.rank() == 0:
    print("Runtime: {}".format(time.time() - time_s))
```

Execute the job script in the directory of your project which contains the virtual environment:

```console
$ sbatch run_sbatch.sh
```

Refer to the `output.txt` and `error.txt` files for the results.


## Tutorial: Adding your own EasyConfig to LUMI

If you have an EasyConfig that you have written yourself, or modified from an existing one, and you would like to bring it to LUMI, here's an example of how it's done. This is briefly described in the [LUMI documentation](https://docs.lumi-supercomputer.eu/software/installing/easybuild/#building-your-own-easybuild-repository)


First let's check what is the value of `EBU_USER_PREFIX` 

```bash
echo $EBU_USER_PREFIX
```

The output should be something as:

```bash
/project/project_465000001/EasyBuild
```

or

```bash
$HOME/EasyBuild
```

if we haven't set the `EBU_USER_PREFIX` to point to a location under our project directories. 

The location where `EBU_USER_PREFIX` points is not only where EasyBuild places the EasyBuild installations, but where it goes looking for existing recipes. If we want to add an EasyConfig ourselves, this is where to place it.

Let's assume we have our `EBU_USER_PREFIX` variable pointing to `/project/project_465000001/EasyBuild`. Let's go to this location

```bash
cd /project/project_465000001/EasyBuild
```

With `ls` we see that the location contains something as the following, depening a bit what kind of installations one has already made:

`ebrepo_files  mgmt  modules  sources  SW  UserRepo`

Let's move to the location where the new recipe should be placed:

```bash
cd UserRepo/easybuild/easyconfigs
```

This location should be empty, if you haven't added any EasyConfigs yourself before. 

You should name your EasyConfig using a similar naming scheme as described in the LUMI documentation: 
https://docs.lumi-supercomputer.eu/software/installing/easybuild/#easybuild-recipes

with 

`software_name`-`version`-`compiler`-`LUMI_software_stack_version`-`possible_additional_information`.eb

Create a new file in this location e.g. with using `nano`

```bash
nano <your-new-easyconfig-name>.eb
```

and add the content of the recipe there. Save and exit the document with `Ctrl+x` and `y` and Enter. 

We can check with `ls` that our EasyConfig file is there now. 

It doesn't matter in which location you are next for the installation, but you can move e.g. to your home directory

```bash
cd
```

To make sure you have a clean shell environment, it is recommended e.g. to log out from LUMI and back in again at this point. 

When back at LUMI with a clean shell, we can now install the software module (for us and our project members) with using the EasyConfig we just added. This works the same way as with any other EasyConfigs that we have described earlier on this page, in the previous examples.

Let's assume our EasyConfig is for LUMI/23.09, and we would like to use it with LUMI-C. 

```bash
module load LUMI/23.09
module load partition/C
module load EasyBuild-user
```

now, if we want to, we can check with `eb -S <our-software-name>` what recipes are available. If our software would be called e.g. MySoftware, we could do:

```bash
eb -S MySoftware
```

Let's now install our EasyConfig:

```bash
eb <full-name-of-our-easyconfig>.eb -r
```
This may take a while, depending on the software.

Now with 

```bash
module avail <our-software-name> 
```

we should find out that our software is available to be loaded with LUMI/23.09 and with partition/C, and we can load it with

```bash
module load MySoftware
```

In the future, when logging in to LUMI, we can only load the same LUMI software stack and partition, and then our software. In this case it would be:

```bash
module load LUMI/23.09
module load partition/C
module load MySoftware


