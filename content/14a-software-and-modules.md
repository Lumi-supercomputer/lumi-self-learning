# Software and Modules on LUMI

This tutorial helps you to get started with modules on LUMI. For a more comprehensive look to options with modules on LUMI, see the [LUMI documentation](https://docs.lumi-supercomputer.eu/runjobs/lumi_env/Lmod_modules/).

## What software there are available on LUMI?

To see the available software on LUMI, one doesn't need to login to LUMI. To see the software available in the central LUMI software collection, just open the [LUMI software library](https://lumi-supercomputer.github.io/LUMI-EasyBuild-docs/) page, which lists all the software that are either _pre-installed_ to the system, or easily _user installable_.

Let's take a few examples. 

First, let's search Gnuplot, and see the page about it.
When we open the page about Gnuplot, we see that there is a green box that reads `pre-installed`. This means that the software is already available in the system, and one can directly use the typical LMOD commands (e.g. `module load`) with it. (We'll see a bit later how the LMOD commands work in practise, and what the term _pre-installed_ means.)

As a second example, let's take a look at the page of the software Gromacs. On this page we have a blue box that reads `user installable`. This means that LMOD can't directly find the software on LUMI, but user needs to _install_ it first. This installation is not a difficult process, and only requires typing few commands. We'll come back to how to do this with specific examples later in this guide. 

Besides the software available in the central LUMI software collection, there are software collections available by local LUMI organizations. Currently (April 2024) there is available an additional software stack by CSC, Finland. To see these available software via this collection, visit the [page about local software collections](https://docs.lumi-supercomputer.eu/software/local/csc/) in the LUMI documentation. 

## Modular structure with software

Maybe here a chapter from 14-modules-and-software.md that helps understanding a bit, why software available via modules and so. 

?

...

## Modules on LUMI

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

The modules you see listed, are both _installed_ and currently _loaded_ to use. 

To see other modules that are currently installed to the system, but not yet loaded, one can use the command `module avail`. The output lists everything that is currently available to load, so it's quite a long list. To search if a specific software is directly availabe to load, one can use the command `module spider <software_name>` . E.g. to see, if we have the editor Nano available to load:

```bash
module spider nano
```

<!-- To be checked through again when LUMI is back in use. Maybe we could also take another editor/example than Nano, what do you think? -->

The output gives the available versions of Nano, of which one can choose. (We'll come back to the meaning of the different versions a bit later.) 

If there is no output when searching a specific software, it doesn't yet mean that the software woulnd't be available on LUMI at all. 
It just might mean that one needs to do an extra step to _install_ the software first. In practise this means a couple more commands that one needs to type. Detailed examples how to find the software available to install on LUMI, as well as how to actually do this in practise, follow later in this material.

Why is some software only available after installing it, and not there directly to be loaded? This is because there are in many cases very many different versions and variations of the same software, and different people prefer different qualities for their specific use cases. Having everything already in the system available for everyone would be just heavy for the system. The approach with software on LUMI provides more variations for the specific use cases that different users might have.


## Tutorial: Load the Nano editor and write a simple script


(I'll continue from here later. The rest of the page is still directly from CSC environment guide. -Hd)

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