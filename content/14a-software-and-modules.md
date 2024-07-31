# Software and Modules on LUMI

This tutorial helps you to get started with modules on LUMI. For a more comprehensive look to options with modules on LUMI, see the [LUMI documentation](https://docs.lumi-supercomputer.eu/runjobs/lumi_env/Lmod_modules/).

## What software there are available on LUMI?

To see the available software on LUMI, one doesn't need to login to LUMI. To see the software available in the central LUMI software collection, just open the [LUMI software library](https://lumi-supercomputer.github.io/LUMI-EasyBuild-docs/) page, which lists all the software that are either _pre-installed_ to the system, or easily _user installable_.

Let's take a few examples. 

First, let's search Gnuplot, and see the page about it.
When we open the page about Gnuplot, we see that there is a green box that reads `pre-installed`. This means that the software is already available in the system, and one can directly use the typical Lmod commands (e.g. `module load`) with it. (We'll see a bit later how the Lmod commands work in practise, and what the term _pre-installed_ means.)

As a second example, let's take a look at the page of the software Gromacs. On this page we have a blue box that reads `user installable`. This means that Lmod can't directly find the software on LUMI, but user needs to _install_ it first. This installation is not a difficult process, and only requires typing few commands. We'll come back to how to do this with specific examples later in this guide. 

Besides the software available in the central LUMI software collection, there are software collections available by local LUMI organizations. Currently (April 2024) there is available an additional software stack by CSC, Finland. To see these available software via this collection, visit the [page about local software collections](https://docs.lumi-supercomputer.eu/software/local/csc/) in the LUMI documentation. 

## Good to know: Software stacks on LUMI

A software stack is a set of modules, libraries and compilers, and everything there is needed to run the applications. On LUMI there are several software stacks available. 
The main software stack on LUMI is the `LUMI software stack`, which is maintained and supported by the LUMI user support team. There are several versions of it available at the same time, as the components of the software stacks regularily get updates. Old software stack versions get disabled at some point. 

It is good to know the principles of what the software environment on LUMI is based on, and what kind of options there are for software stacks on LUMI. Here we give just a very brief description. In case you would like to know more, please see the [LUMI documentation](https://docs.lumi-supercomputer.eu/runjobs/lumi_env/softwarestacks/). 

As LUMI is a HPE Cray machine, the basis of all the software environments on LUMI is a Cray environment. This is what you get as a default, once you login to LUMI. It is recommended to load one of the offered software stacks on top of this. In the following examples in this guide we will be using versions of the main software stacks on LUMI, i.e. versions of the "LUMI software stack". 

(A figure here, what sw stack options there are?)

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




## Modular structure with software

Maybe here a chapter from 14-modules-and-software.md that helps understanding a bit, why software available via modules and so. 

?

...

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

The modules you see listed are both _installed_ and currently _loaded_ to use. This is the default environment when you open a new session on LUMI, before you do or load anything else. (It contains e.g. some initial/default settings for libraries, compiling environment, etc.)

Next step after login would usually be loading one version of the available software stacks. Which one, depends on what we would like to do on LUMI. 

One example workflow would be that first after login we would load a LUMI software stacks (e.g. LUMI/23.09). Then we would load a suitable partition for our use case (e.g. partition/C for LUMI-C compute nodes). Then we would load our software. If the software is not yet installed by you or by one of your group members to your `/project/project_46XXXXXXX/` location, we install the software first. 

(A some sort of module load example tree here?)

```

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

...


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



## Advanced: Adding your own EasyConfig to LUMI

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
```