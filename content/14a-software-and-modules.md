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

(Should this be marked as an info-box? Kind of additional information, but definitelly good to mention shortly somewhere here when we are talking about software on LUMI.)

A sotware stack means a collection of software. On LUMI the main collections are the versions of the `LUMI software stack`, maintained and supported by the LUMI user support team. It is good to know in general what the software environment on LUMI is based on, and what kind of options there are for software stacks on LUMI. Here we give just a very brief description. In case you would like to know more, please see the [LUMI documentation](https://docs.lumi-supercomputer.eu/runjobs/lumi_env/softwarestacks/). 

As LUMI is a HPE Cray machine, the basis of all the software environments on LUMI is a basic Cray environment. This is what you get as default, when you login to LUMI. It is recommended to load one of the offered software stacks on top of this. In the following examples in this guide we will be using versions of the main software stacks on LUMI, i.e. versions of the "LUMI software stack". 

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

`The Cray environment` (CrayEnv) contains some additional tools on top of the default environemnt what you get at login. https://docs.lumi-supercomputer.eu/runjobs/lumi_env/softwarestacks/#crayenv

`The LUMI stacks` are the main software stacks on LUMI that is build on top of the basic Cray environment using EasyBuild. https://docs.lumi-supercomputer.eu/runjobs/lumi_env/softwarestacks/#lumi
This is what we will be using in the following examples. 

`Spack` is available, but it is only recommended if you are already familiar with using Spack. It is offered "as-is". No support with developing or debugging Spack modules is provided by the LUMI user support team. 
https://docs.lumi-supercomputer.eu/software/installing/spack/



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

Basically next step would be to load a version of the available software stacks. But it depends completely on what we want to do on LUMI, which software stack we would like to use. 

...

**The following is a DRAFT. (Still trying to figure out what would be the simplest way to present how to work with modules/software on LUMI. - Hd / 3rd May.)**

Basically first after login we would load a software stacks (e.g. LUMI/23.09). Then we would load a suitable partition for our use case (e.g. partition/C for LUMI-C compute nodes). Then we would load our software. If the software is not yet installed e.g. by one of your group members to your `/project/project_46XXXXXXX/` location, we install the software first. 

A module load example tree?

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
|                        `------ (Load or install your software e.g. w/ EasyBuild) 
|
|-------------- Spack/23.03
|
`-------------- Spack/23.09
                `----- (Do whatever is done with Spack)

```

...


-> Module avail etc, maybe example with Nano and some sw that we need to install w/ EB. 

...

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

At this point we are still with the same environment than which we had right after login to LUMI. If we do the `module list` the listing is exactly the same as earlier. Let's see the currently available versions of `LUMI` software stacks: 

Type `module load LUMI` and press the tabulator key twice. The output you see should be something like:

`LUMI  LUMI/22.08  LUMI/22.12  LUMI/23.03  LUMI/23.09`

The versions of the LUMI software stacks are named based on a year and a month that the corresponding version of a Cray software stack was reliesed.

---
(The tutorial about loading Nano here?)

Now, let's load a version of LUMI software stacks `LUMI/23.09`:

```bash
module load LUMI/23.09
```

...



Because we haven't loaded any of the available versions of software stacks (CrayEnv, LUMI, Spack), we get versions for the Nano editor that are adjusted for several of these. 





(Note that if there are very many versions available, all versions might not be visible in this output. One can always check the [LUMI software library](https://lumi-supercomputer.github.io/LUMI-EasyBuild-docs/, if unsure about availability of  )









If there is no output when searching a specific software, it doesn't yet mean that the software woulnd't be available on LUMI at all. 
It just might mean that one needs to do an extra step to _install_ the software first. In practise this means a couple more commands that one needs to type. Detailed examples how to find the software available to install on LUMI, as well as how to actually do this in practise, follow later in this material.

Why is some software only available after installing it, and not there directly to be loaded? This is because there are in many cases very many different versions and variations of the same software, and different people prefer different qualities for their specific use cases. Having everything already in the system available for everyone would be unpractical (the view when searching for modules would become long and messy, and also all these modules would need to be maintained). The approach with software on LUMI provides more variations for the specific use cases that different users might have.


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

1) Decide where you want to install the software. The default location is your home folder $HOME/EasyBuild. The software installation will be done there, if you dont' change anything. It's in general a better practise to install the software in you /project folder. If the software is istalled there, all of the members of your project can use the installed software, and thus only one of you needed to actually install it. 

- To change the install location to your /project folder:

This change needs to be done before you load a version of LUMI sotware stacks. 

Open a new shell on LUMI (e.g. logout, and login to LUMI again, or open a new LUMI session in another window)

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

(Continuing from here - Hd 19th May 2024)

...

## More module commands with Gromacs as an example

💬 Let's imagine that you want to do some molecular dynamics simulations using the [Gromacs](https://www.gromacs.org/about.html) application.

- It is always a good idea to start by checking [the application list in Docs CSC](https://docs.csc.fi/apps/) to see whether this application is installed in Puhti and how to use it.

1. Check out the [Gromacs page](https://docs.csc.fi/apps/gromacs/).
2. Skim through the documentation and verify that the license allows you to use the software.
3. Check what is the module command that you need to run to be able to load Gromacs in Puhti.
4. Back on the command-line, check which Gromacs versions are available in Puhti.

```bash
module spider gromacs
```

☝🏻 This might take a while as the command searches through all the available modules.

💬 The list can be quite long. You can go to the next line with Enter, or stop viewing by typing `q`).

{:style="counter-reset:step-counter 4"}
5. Check if some versions can be loaded directly, *i.e.* are compatible with your currently loaded module environment:

```bash
module avail gromacs
```

💡 Tip: Another quick way to list the available versions is by typing the load command until the module name and then hit `TAB` twice:

```bash
$ module load gromacs # and here double press TAB
gromacs                gromacs/2022.3         gromacs-env/2021-gpu
gromacs/2021.4-plumed  gromacs/2022.4         gromacs-env/2022
gromacs/2021.5         gromacs-env            gromacs-env/2022-gpu
gromacs/2021.6         gromacs-env/2020       
gromacs/2022.2         gromacs-env/2021
```

{:style="counter-reset:step-counter 5"}
6. Which version is loaded with the default command? Is it the newest version? Try:

```bash
module load gromacs
```

{:style="counter-reset:step-counter 6"}
7. Do you notice any changes in the output of `module list` compared to the first try? Try this again:

```bash
module list
```  

☝🏻 If no version is given in the module command, the default version is loaded.

- The default version is typically the latest **stable** version of the program.
- It is recommended to also provide the version in the module load command, as the default version may change.

{:style="counter-reset:step-counter 7"}
8. Let's try loading the 2021.6 version specifically:

```bash
module load gromacs/2021.6
module list
```

{:style="counter-reset:step-counter 8"}
9. If you want to do something else in the same session, it is usually best to reset the module environment to the default settings. This can be done by first removing all loaded modules and then loading the default environment:

```bash
module purge            # Purge all (non-sticky) modules
module list             # List the loaded modules
module load StdEnv      # Load the default module environment
module list             # List the loaded modules
```

## More information

💭 If actually using Gromacs in Puhti, you would run the application as a batch job through the queueing system, which will be discussed in detail later.

💭 Check out an [example batch job script for Gromacs](https://docs.csc.fi/apps/gromacs/#example-parallel-batch-script-for-puhti) to see how the module is recommended to be loaded (`module load gromacs-env` loads the latest minor release of a specific year).

### Loading an older version of a module

💬 Using an older version of a module is usually possible

💬 As an example, you can try to load an old version of Gromacs from 2020.

1. The `gromacs/2020.5` module cannot be loaded in the default environment since it has different dependencies. Check with the `module spider` command which other modules are needed for the older version:

```bash
module spider gromacs/2020.5
```

{:style="counter-reset:step-counter 1"}
2. Load all of the required modules manually before loading `gromacs/2020.5`

```bash
module purge
module load gcc/9.4.0
module load openmpi/4.1.4
module load gromacs/2020.5
module list
```

☝🏻 It is generally best to use the latest versions since they typically are more performant than old ones and may have useful new features.