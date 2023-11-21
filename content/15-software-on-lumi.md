# Software on LUMI

How to find the available software, how to install, and specific software tutorials. 


## Tutorial: Finding available & installable software

How to find available software from LUMI software collection, and how to see what is pre-installed and what is user-installable. what other things to pay attention to (referring to previous chapter).


## Tutorial: Installing user-installable software with EasyBuild

An easy example, a general hand-hold tutorial how to do this


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
Singularity> pip install XXX
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

srun --mpi=cray_shasta singularity exec --pwd /work --bind $SCRATCH:/work pytorch_rocm5.7_ubuntu22.04_py3.10_pytorch_2.0.1.sif /work/pt_rocm5.7_env/bin/python PyTorch_MNIST-DDP.py
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
$ SINGULARITY_TMPDIR=/tmp/tmp-singularity SINGULARITY_CACHEDIR=/tmp/tmp-singularity singularity pull docker://rocm/tensorflow:rocm5.7-tf2.13-dev
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
$ singularity exec -B $HOME/tensorflow/ensurepip:/usr/lib/python3.9/ensurepip tensorflow_rocm5.7-tf2.13-dev.sif  bash
Singularity> python -m venv my_project_env --system-site-packages
Singularity> . my_project_env/bin/activate
Singularity> pip install XXX
```

```{callout} Create more virtual environments
You might want to create more virtual environments, one for each of your projects, or for experimenting with different module versions. Simply repeat the commands with providing individual virtual environment names.
```

#### 4th Step

Build and install Horovod.

```console
$ module load LUMI/22.08 partition/G
$ module load EasyBuild-user
$ singularity exec tensorflow_rocm5.7-tf2.13-dev.sif  bash
Singularity> . ./my_project_env/bin/activate
(my_project_env) Singularity> export HOROVOD_WITHOUT_MXNET=1
(my_project_env) Singularity> export HOROVOD_WITHOUT_PYTORCH=1
(my_project_env) Singularity> export HOROVOD_GPU=ROCM
(my_project_env) Singularity> export HOROVOD_GPU_OPERATIONS=NCCL
(my_project_env) Singularity> export HOROVOD_WITHOUT_GLOO=1
(my_project_env) Singularity> export HOROVOD_WITH_TENSORFLOW=1
(my_project_env) Singularity> export HOROVOD_ROCM_PATH=/opt/rocm
(my_project_env) Singularity> export HOROVOD_RCCL_HOME=/opt/rocm/rccl
(my_project_env) Singularity> export RCCL_INCLUDE_DIRS=/opt/rocm/rccl/include
(my_project_env) Singularity> export HOROVOD_RCCL_LIB=/opt/rocm/rccl/lib
(my_project_env) Singularity> export HOROVOD_MPICXX_SHOW="CC --cray-print-opts=all"
(my_project_env) Singularity> export HCC_AMDGPU_TARGET=gfx90a

```

### Use it


## Tutorial: ...

A software tutorial

## Tutorial: ...

A software tutorial

