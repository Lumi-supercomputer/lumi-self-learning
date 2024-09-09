# Using LUMI-O object storage 

All your data doesn't fit to the filesystem on LUMI? You don't actively need it, but would like to keep it somewhere close and easy to access? Or you would like to have a backup of the data that you have in the file system of LUMI in your /project or /scratch folders that would be also accessible during (most of) the LUMI maintenance breaks? Are you looking for a way to share data from your project to others, either to a specific audience via a link or managing the access rights, or publicly to anyone who knows where to look for it? The answer to all these questions/needs is LUMI-O! 

LUMI-O is a S3 compatible object storage, based on Ceph Reef (v18.2.2). 

Some benefits of LUMI-O:
- LUMI-O can fit up to X? of data per project (30 PB all together)
- Data lifetime on LUMI-O is the same as your project lifetime
- Can be used as a backup of your data that you have on your /project and /scratch locations
- Can be used to share data outside of project members (privately, or making the content public)

Guide for LUMI-O in the LUMI documentation: https://docs.lumi-supercomputer.eu/storage/lumio/

One can access LUMI-O via terminal from LUMI, using the LUMI web interface, or also without connecting to LUMI at all, since LUMI-O is a separate instance from LUMI. 

## Tutorial: Using LUMI-O from the LUMI web interface

1. Go to the LUMI web interface: <https://www.lumi.csc.fi>
2. Login with using your identity provider
3. Select *Cloud storage configuration* from the *Pinned Apps* view on the dashboard.
4. From "Configure new remotes", choose LUMI-O.
5. From the drop-down menu, select the project you will use for this exercise.
6. Tick both of the selections to configure both public and private remotes (this means that you will be able to add/modify both kinds of data, public and private(=only for your project members))
   - You may skip the previous step if you've already configured a connection.

💡 Now you'll be able to browse your _buckets_ and _objects_ in LUMI-O using the *Files* app!

{:style="counter-reset:step-counter 4"}
1. From the *Files* dropdown menu in the top navigation bar, select `lumi-<id>-public` where `<id>` is the number of your project (e.g. 465000000).
2. Create a new bucket by pressing the *New Directory* button.
   - Name it as `<id>_<username>`, in which `<id>` is again the number of your project and `<username>` is your LUMI username. Note that you cannot use a bucket name that already exists!
3. Open the created bucket by clicking it.
4. Upload one file from your computer into the bucket (any file should do, but prefer a file that you can open in LUMI, e.g. a text file).

💭 During the exercises, you can use this web interface to get another view to your buckets and objects in LUMI-O.

## Tutorial: Accessing LUMI-O from a LUMI login node (via terminal)

### Preparations (if not done already)

1. Login to `lumi.csc.fi` via terminal (or open a login node shell, if using the LUMI web interface)
2. Once you have a terminal open on a LUMI login node, check your environment with the command

```bash
lumi-workspaces
```

{:style="counter-reset:step-counter 2"}
3. Move to the scratch directory of your project:

```bash
cd /scratch/<project>   # replace <project> with your LUMI project, e.g. project_465000000
```

{:style="counter-reset:step-counter 3"}
4. Create your own subdirectory named as your username (tip! your username is automatically stored in the environment variable `$USER`):

```bash
mkdir -p $USER
cd $USER
```

### Connecting to LUMI-O

1. Open a connection to LUMI-O with these commands:

```bash
module load lumio
lumio-conf 
```

After this you are asked to login to: https://auth.lumidata.eu/
Please follow the guide here in the LUMI documentation: https://docs.lumi-supercomputer.eu/storage/lumio/auth-lumidata-eu/

{:style="counter-reset:step-counter 1"}
2. If you have several projects with access to LUMI-O available, select the one where you just created a bucket using the LUMI web interface

After creating the credentials for your chosen project, give the LUMI project number, e.g.

`465000000`

in the terminal. Next you need to give the access key you just generated, i.e. something like:

`IXZLOSAF2E3MD42T840B`

Finally, you should give the secret key, i.e. something like: 
`rwfkhpNtUSvVZ2R2Pw4qsLkWetTKsd7dC5DBxS98`


3. Study what you have in LUMI-O with [rclone](https://docs.lumi-supercomputer.eu/storage/lumio/#__tabbed_1_1) or with [s3cmd](https://docs.lumi-supercomputer.eu/storage/lumio/#__tabbed_1_2)

- With `rclone`:

```bash
rclone lsd lumi-o:
rclone ls lumi-o:<id>_$USER
rclone lsl lumi-o:<id>_$USER
rclone lsf lumi-o:<id>_$USER
rclone cat lumi-o:<id>_$USER/<filename>
```

The bucket `<id>_$USER` is the one we just created in the first exercise using the LUMI web interface.

- With `s3cmd`:

```bash
s3cmd ls s3:
s3cmd ls s3://<id>_$USER
s3cmd lsl s3://<id>_$USER
s3cmd lsf s3://<id>_$USER
s3cmd cat s3://<id>_$USER/<filename>
```

{:style="counter-reset:step-counter 3"}
4. Download to LUMI the file that you just uploaded from your local computer to LUMI-O. This can be done e.g. with rclone or s3cmd:

- With `rclone`:

```bash
rclone copy lumi-o:<id>_$USER/<filename> ./    # replace <id> with your LUMI project number, e.g. 465000000, and <filename> with the file you uploaded
```

- With `s3cmd`:

```bash
...
```


{:style="counter-reset:step-counter 4"}
5. Open, edit and rename the file so that you can distinguish it from the original one
6. Upload the new file to LUMI-O:

- With `rclone`:

```bash
rclone copy <newfilename> lumi-o:<id>_$USER/   # replace <newfilename> and <id> accordingly
```

- With `s3cmd`:

```bash
...
```

{:style="counter-reset:step-counter 6"}
7. Check that the file in LUMI indeed has a counterpart in LUMI-O:

- With `rclone`:

```bash
rclone ...
```

- With `s3cmd`:

```bash
...
```

{:style="counter-reset:step-counter 7"}
8. Check the privacy settings of your file in LUMI-O:

- With `rclone`:

```bash
rclone ...
```

- With `s3cmd`:

```bash
...
```

9. Check the privacy settings of your bucket ... in LUMI-O:

- With `rclone`:

```bash
rclone ...
```

- With `s3cmd`:

```bash
...
```

Which is as it should be, since we created the bucket to the public end point. The objects in the buckets automatically inherit the privacy settings from the bucket where they are in. But you can also change the privacy settings of the buckets or objects in them. This goes to advanced use, but you can see more from the [LUMI documentation](https://docs.lumi-supercomputer.eu/storage/lumio/advanced/) in which especially the part about [Granular Access management](https://docs.lumi-supercomputer.eu/storage/lumio/advanced/#granular-access-management )


{:style="counter-reset:step-counter 8"}
10. Locate the files you just uploaded to LUMI-O in the LUMI web interface (search for the bucket name)

### Clean up

1. Delete the local file from LUMI:

```bash
rm <filename>             # replace <filename>
```

{:style="counter-reset:step-counter 1"}
2. Whenever you need your data again, you can download it from LUMI-O

💭 If you can't find your file but remember the name, try 

- With `rclone`:

```bash
rclone ...
```

- With `s3cmd`:

```bash
...
```

## Advanced: Using LUMI-O without connecting to LUMI

(Comment: This might belong to LUMI docs instead of here.)

Useful for example during a LUMI servcie break, when LUMI-O might be normally available.

One can use LUMI-O also without connecting to LUMI. This is an example with rclone.

1) If you don't have rclone installed already, follow directions at https://rclone.org/install/ to install rclone on your local machine.

2) Set LUMI-O access credentials at https://auth.lumidata.eu/ for the project you want to use.

3) Create an `rclone.conf` file (to the path ~/.config/rclone/rclone.conf) on your local machine:

```bash
# An example of an rclone config file rclone.conf
# Objects will inherit the default properties from buckets. 

[lumi-project-private]
# Buckets created with this endpoint configuration are private by default.
type = s3
acl = private
env_auth = false
provider = Ceph
endpoint = https://lumidata.eu/
access_key_id = XXX  # Replace with your created LUMI-O access key
secret_access_key = YYY  # Replace with your created LUMI-O secret access key

[lumi-project-public]
# Buckets created with this endpoint configuration are visible for all.
type = s3
acl = public-read
env_auth = false
provider = Ceph
endpoint = https://lumidata.eu/
access_key_id = XXX  # Replace with your created LUMI-O access key
secret_access_key = YYY # Replace with your created LUMI-O secret access key

```

4) Run `rclone config` # Not maybe necessary?

5) Use LUMI-O with rclone commands from your local machine, e.g. to list all the buckets in your project:

```bash
rclone lsd lumi-project-private:
```






