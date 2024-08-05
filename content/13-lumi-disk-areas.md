
# Using the LUMI filesystem

> In this tutorial you will
   - Familiarize yourself with personal and project-specific disk areas and their quotas on the LUMI supercomputer.
   - Learn how to share your files, such as software installations and data, to other members of your LUMI project.
   - Get to try out the disk area for I/O intensive workflows, i.e. frequent read and write operations

💬 Each user of LUMI supercomputer has access to different disk areas (or directories) for managing their data. Each disk area has its own specific purpose.

💬 Active data files needed for computational simulations and analyses should be stored and shared in directories under `/scratch`. For I/O heavy operations use `/flash` instead, which is a high performance variant of /scratch. Any software installations and binaries should be shared under the `/project` directory.

## Identify your personal and project-specific directories on LUMI supercomputer

1. First login to LUMI using SSH (or by opening a login node shell in the [LUMI web interface](https://www.lumi.csc.fi)):
  
```bash
ssh -i <path-to-private-key> <username>@lumi.csc.fi    # replace <username> with your LUMI username
                                                       # replace <path-to-private-key> with the path to your private key file, e.g. 
                                                       # ./.ssh/id_ed25519 or /home/user/.ssh/id_ed25519
```

2. Get an overview of your projects and directories by running the following command on the login node:

```bash
lumi-workspaces
```

3. Inspect the output information summarizing your directories and their current quotas.
4. Visit your project's `/scratch` directory and list its contents:

```bash
cd /scratch/<project>/   # replace <project> with your LUMI project, e.g. project_465000123
ls
```

5. Visit your project's `/project` directory and list its contents:

```bash
cd /project/<project>/   # replace <project> with your LUMI project, e.g. project_465000123
ls
```

💬 These directories can be briefly summarized as follows:

- User-specific directory (i.e. your personal home folder)
   - Your home directory (path stored in environment variable `$HOME`)
   - The default directory when you login to LUMI
   - You can store configuration files and other minor data for personal use
- Project-specific directories:
   - The project's `/scratch`, `/flash` and `/project` directories
   - Each project has its own `/scratch` disk space. This is the main storage space one should use for the active input and output data of their application. The `/scratch` area is a temporary space not intended for long-term data storage! Please move inactive data to e.g. [LUMI-O](https://docs.lumi-supercomputer.eu/storage/lumio/).
   - The `/flash` area is like /scratch, but meant to only be used shortly for I/O heavy tasks. Using /flash consumes 10x the billing units compared to using /scratch.
   - `/project` directory on the other hand is mainly for storing and sharing compiled applications and libraries etc. with other members of the project.
- Read more of the file system locations on LUMI from [LUMI user documentation](https://docs.lumi-supercomputer.eu/storage/)


> ## There are no backups for data on LUMI
> Notice that there are no backups for any of the file system locations on LUMI. 
> Make sure to move your important data or back it up e.g. to your local machine.
{: .callout}

## Sharing binaries and data files

💬 Data transfer between a supercomputer and your local machine, or between two supercomputers, can be done e.g. with `rsync`.

### Download the example files

☝🏻 In this example you will *download* data from [Allas](https://docs.csc.fi/data/Allas/) object storage. # Change the example from Allas to LUMI-O ?

1. Move to your home folder:

```bash
cd
```

💡 If you know the files are large, you should consider downloading them directly to `/scratch`.

2. Download an example program package (`ggplot2_3.3.3_Rprogramme.tar.gz`) and a data file (`Merged.fasta`) from the Allas object storage
  
```bash
wget https://a3s.fi/CSC_training/shared_files.tar.gz
tar -xavf shared_files.tar.gz
cd shared_files
```

Let's assume that

- `Merged.fasta` is a data file intended for computational use
- `ggplot2_3.3.3_Rprogramme.tar.gz` is a software tool needed for the analysis.

### Move the files to LUMI `/scratch` and `/project`

1. Create folders with your username (using environment variable `$USER`) in your project directories under `/scratch` and `/project` on LUMI.

```bash
mkdir -p /project/<project>/$USER   # replace <project> with your LUMI project, e.g. project_465000123
mkdir -p /scratch/<project>/$USER    # replace <project> with your LUMI project, e.g. project_465000123
```

2. Copy your `ggplot2_3.3.3_Rprogramme.tar.gz` file to the `/project` directory

```bash
cp ggplot2_3.3.3_Rprogramme.tar.gz  /project/<project>/$USER/   # replace <project> with your LUMI project, e.g. project_465000123
```

3. Copy the `Merged.fasta` file to the `/scratch` directory

```bash
cp Merged.fasta /scratch/<project>/$USER/    # replace <project> with your LUMI project, e.g. project_465000123
```

- Note that all new files and directories are also fully accessible to other members of the project (including read and write permissions).

4. Set read-only permissions for your project members for the file `Merged.fasta`:

```bash
cd /scratch/<project>/$USER/    # replace <project> with your LUMI project, e.g. project_465000123
chmod g-w Merged.fasta          # g-w means that we "subtract" write permissions for users belong to our group (g), i.e. our project
```

### Copying files from LUMI to your local machine and from your local machine to LUMI

1. Change the path to the folder where you have the example files on LUMI e.g. with `pwd` command 
2. Open a new terminal window that is not connected to LUMI. Create a suitable location where you want to copy your data. To copy `Merged.fasta` file from LUMI to your current location ` . ` on your local machine:

```bash
rsync -P <username>@lumi.csc.fi:/scratch/<project>/<username>/Merged.fasta .  # replace <username> with your LUMI username and <project> with your LUMI project, e.g. project_465000123
```

3. For the exercise, copy the `Merged.fasta` file now from your local machine to your home directory on LUMI:

```bash
rsync -P Merged.fasta <username>@lumi.csc.fi:   # replace <username> with your LUMI username and <project> with your LUMI project, e.g. project_465000123
```

- Learn more about data transfer options from the [LUMI user documentation](https://docs.lumi-supercomputer.eu/firststeps/movingdata/)

## Light pre-processing of data files using project `/flash`

You may sometimes come across situations where you have to process a large number of smaller files, which can cause heavy input/output load on the shared file system. In order to facilitate such heavy I/O operations, there is a specific disk space for that. It can be used for:
  - pre-processing of data and I/O heavy tasks such as software compilation
  - submitting computations for jobs that need very fast disk I/O operations

### Download a tar archive containing thousands of small files and merge the files into one large file using the fast disk area /flash

1. Move to the /flash disk space and download a tar file from the **Allas** object storage:
  
```bash
cd /flash/<project>/$USER/   # replace <username> with your LUMI username and <project> with your LUMI project, e.g. project_465000123     
wget https://a3s.fi/CSC_training/Individual_files.tar.gz
```

2. Unpack the downloaded tar file:

```bash
tar -xavf Individual_files.tar.gz
cd Individual_files
```

3. Merge each small file into a larger one and remove all small files

```bash
find . -name 'individual.fasta*' | xargs cat >> Merged.fasta
find . -name 'individual.fasta*' | xargs rm
```

In general it is a good idea to move any data away from the /flash disk space when it's not used in that location anymore. Now we used the /flash disk space only for data pre-processing, in which case we will move it right away to other location. If you would need to submit an I/O heavy computation with your data, then do such way that the data is in the /flash location. 

### Move your pre-processed data to the project-specific `/scratch` area

1. Move your pre-processed data from the previous step (i.e., the `Merged.fasta` file) from `/flash` to `/scratch`:

```bash
mv Merged.fasta /scratch/<project>/$USER
```

3. You have now successfully moved the data to the `/scratch` area and can start performing an analysis using batch job scripts.

- More about batch jobs in later tutorials.


## More information

💡 Hint: You can use your folder under `/scratch` for the rest of the tutorials. You can save the path using an [alias](https://www.shell-tips.com/bash/alias/) (with `cd` or `echo`) or somewhere in your notes.

💡 It is sometimes required to export the paths of the `/scratch` or `/project` directories in environmental variables (until logout). This can be done with the following commands:

```bash
export PROJECT=/project/<project>/   # replace <project> with your LUMI project, e.g. project_465000123
export SCRATCH=/scratch/<project>/   # replace <project> with your LUMI project, e.g. project_465000123
```
