# Scheduler and batch jobs

```{questions}
- What is a scheduler and why does a cluster need one?
- How do I capture the output of a program that is run on a node in the cluster?
```

```{objectives}
- Submit a simple script to the cluster.
- Monitor the execution of jobs using command line tools.
- Inspect the output and error files of your jobs.
- Find the right place to put large datasets on the cluster.
```

```{keypoints}
- The scheduler handles how compute resources are shared between users.
- A job is just a shell script.
- Request _slightly_ more resources than you will need.
```

## Job Scheduler

An HPC system might have thousands of nodes and thousands of users. How do we
decide who gets what and when? How do we ensure that a task is run with the
resources it needs? This job is handled by a special piece of software called
the _scheduler_. On an HPC system, the scheduler manages which jobs run where
and when.

The following illustration compares these tasks of a job scheduler to a waiter
in a restaurant. If you can relate to an instance where you had to wait for a
while in a queue to get in to a popular restaurant, then you may now understand
why sometimes your job do not start instantly as in your laptop.

{% include figure.html max-width="75%" caption=""
   file="/fig/restaurant_queue_manager.svg"
   alt="Compare a job scheduler to a waiter in a restaurant" %}

The scheduler used in this lesson is {{ site.sched.name }}. Although
{{ site.sched.name }} is not used everywhere, running jobs is quite similar
regardless of what software is being used. The exact syntax might change, but
the concepts remain the same.

## Running a Batch Job

The most basic use of the scheduler is to run a command non-interactively. Any
command (or series of commands) that you want to run on the cluster is called a
_job_, and the process of using a scheduler to run the job is called _batch job
submission_.

In this case, the job we want to run is a shell script -- essentially a
text file containing a list of UNIX commands to be executed in a sequential
manner. Our shell script will have three parts:

* On the very first line, add `{{ site.remote.bash_shebang }}`. The `#!`
  (pronounced "hash-bang" or "shebang") tells the computer what program is
  meant to process the contents of this file. In this case, we are telling it
  that the commands that follow are written for the command-line shell (what
  we've been doing everything in so far).
* Anywhere below the first line, we'll add an `echo` command with a friendly
  greeting. When run, the shell script will print whatever comes after `echo`
  in the terminal.
  * `echo -n` will print everything that follows, _without_ ending
    the line by printing the new-line character.
* On the last line, we'll invoke the `hostname` command, which will print the
  name of the machine the script is run on.

```
{{ site.remote.prompt }} nano example-job.sh
```

```
{{ site.remote.bash_shebang }}

echo -n "This script is running on "
hostname
```


>  Creating Our Test Job
>
> Run the script. Does it execute on the cluster or just our login node?
>
>  Solution
>
> ```
> {{ site.remote.prompt }} bash example-job.sh
> ```
> 
> ```
> This script is running on {{ site.remote.host }}
> ```
> 
> {: .solution}
{: .challenge}

This script ran on the login node, but we want to take advantage of
the compute nodes: we need the scheduler to queue up `example-job.sh`
to run on a compute node.

To submit this task to the scheduler, we use the
`{{ site.sched.submit.name }}` command.
This creates a _job_ which will run the _script_ when _dispatched_ to
a compute node which the queuing system has identified as being
available to perform the work.

```
{{ site.remote.prompt }} {{ site.sched.submit.name }} {% if site.sched.submit.options != '' %}{{ site.sched.submit.options }} {% endif %}example-job.sh
```


{% include {{ site.snippets }}/scheduler/basic-job-script.snip %}

And that's all we need to do to submit a job. Our work is done -- now the
scheduler takes over and tries to run the job for us. While the job is waiting
to run, it goes into a list of jobs called the _queue_. To check on our job's
status, we check the queue using the command
`{{ site.sched.status }} {{ site.sched.flag.user }}`.

```
{{ site.remote.prompt }} {{ site.sched.status }} {{ site.sched.flag.user }}
```


{% include {{ site.snippets }}/scheduler/basic-job-status.snip %}

>  Where's the Output?
>
> On the login node, this script printed output to the terminal -- but
> now, when `{{ site.sched.status }}` shows the job has finished,
> nothing was printed to the terminal.
>
> Cluster job output is typically redirected to a file in the directory you
> launched it from. Use `ls` to find and `cat` to read the file.
{: .discussion}

## Customising a Job

The job we just ran used all of the scheduler's default options. In a
real-world scenario, that's probably not what we want. The default options
represent a reasonable minimum. Chances are, we will need more cores, more
memory, more time, among other special considerations. To get access to these
resources we must customize our job script.

Comments in UNIX shell scripts (denoted by `#`) are typically ignored, but
there are exceptions. For instance the special `#!` comment at the beginning of
scripts specifies what program should be used to run it (you'll typically see
`{{ site.local.bash_shebang }}`). Schedulers like {{ site.sched.name }} also
have a special comment used to denote special scheduler-specific options.
Though these comments differ from scheduler to scheduler,
{{ site.sched.name }}'s special comment is `{{ site.sched.comment }}`. Anything
following the `{{ site.sched.comment }}` comment is interpreted as an
instruction to the scheduler.

Let's illustrate this by example. By default, a job's name is the name of the
script, but the `{{ site.sched.flag.name }}` option can be used to change the
name of a job. Add an option to the script:

```
{{ site.remote.prompt }} cat example-job.sh
```


```
{{ site.remote.bash_shebang }}
{{ site.sched.comment }} {{ site.sched.flag.name }} hello-world

echo -n "This script is running on "
hostname
```


Submit the job and monitor its status:

```
{{ site.remote.prompt }} {{ site.sched.submit.name }} {% if site.sched.submit.options != '' %}{{ site.sched.submit.options }} {% endif %}example-job.sh
{{ site.remote.prompt }} {{ site.sched.status }} {{ site.sched.flag.user }}
```


{% include {{ site.snippets }}/scheduler/job-with-name-status.snip %}

Fantastic, we've successfully changed the name of our job!


### Resource Requests

What about more important changes, such as the number of cores and memory for
our jobs? One thing that is absolutely critical when working on an HPC system
is specifying the resources required to run a job. This allows the scheduler to
find the right time and place to schedule our job. If you do not specify
requirements (such as the amount of time you need), you will likely be stuck
with your site's default resources, which is probably not what you want.

The following are several key resource requests:

{% include {{ site.snippets }}/scheduler/option-flags-list.snip %}

Note that just _requesting_ these resources does not make your job run faster,
nor does it necessarily mean that you will consume all of these resources. It
only means that these are made available to you. Your job may end up using less
memory, or less time, or fewer nodes than you have requested, and it will still
run.

It's best if your requests accurately reflect your job's requirements. We'll
talk more about how to make sure that you're using resources effectively in a
later episode of this lesson.

>  Submitting Resource Requests
>
> Modify our `hostname` script so that it runs for a minute, then submit a job
> for it on the cluster.
>
>  Solution
>
> ```
> {{ site.remote.prompt }} cat example-job.sh
> ```
> 
>
> ```
> {{ site.remote.bash_shebang }}
> {{ site.sched.comment }} {{ site.sched.flag.time }} 00:01 # timeout in HH:MM
>
> echo -n "This script is running on "
> sleep 20 # time in seconds
> hostname
> ```
> 
>
> ```
> {{ site.remote.prompt }} {{ site.sched.submit.name }} {% if site.sched.submit.options != '' %}{{ site.sched.submit.options }} {% endif %}example-job.sh
> ```
> 
>
> Why are the {{ site.sched.name }} runtime and `sleep` time not identical?
> {: .solution}
{: .challenge}

{% include {{ site.snippets }}/scheduler/print-sched-variables.snip %}

Resource requests are typically binding. If you exceed them, your job will be
killed. Let's use wall time as an example. We will request 1 minute of
wall time, and attempt to run a job for two minutes.

```
{{ site.remote.prompt }} cat example-job.sh
```


```
{{ site.remote.bash_shebang }}
{{ site.sched.comment }} {{ site.sched.flag.name }} long_job
{{ site.sched.comment }} {{ site.sched.flag.time }} 00:01 # timeout in HH:MM

echo "This script is running on ... "
sleep 240 # time in seconds
hostname
```


Submit the job and wait for it to finish. Once it is has finished, check the
log file.

```
{{ site.remote.prompt }} {{ site.sched.submit.name }} {% if site.sched.submit.options != '' %}{{ site.sched.submit.options }} {% endif %}example-job.sh
{{ site.remote.prompt }} {{ site.sched.status }} {{ site.sched.flag.user }}
```


{% include {{ site.snippets }}/scheduler/runtime-exceeded-job.snip %}

{% include {{ site.snippets }}/scheduler/runtime-exceeded-output.snip %}

Our job was killed for exceeding the amount of resources it requested. Although
this appears harsh, this is actually a feature. Strict adherence to resource
requests allows the scheduler to find the best possible place for your jobs.
Even more importantly, it ensures that another user cannot use more resources
than they've been given. If another user messes up and accidentally attempts to
use all of the cores or memory on a node, {{ site.sched.name }} will either
restrain their job to the requested resources or kill the job outright. Other
jobs on the node will be unaffected. This means that one user cannot mess up
the experience of others, the only jobs affected by a mistake in scheduling
will be their own.

## Cancelling a Job

Sometimes we'll make a mistake and need to cancel a job. This can be done with
the `{{ site.sched.del }}` command. Let's submit a job and then cancel it using
its job number (remember to change the walltime so that it runs long enough for
you to cancel it before it is killed!).

```
{{ site.remote.prompt }} {{ site.sched.submit.name }} {% if site.sched.submit.options != '' %}{{ site.sched.submit.options }} {% endif %}example-job.sh
{{ site.remote.prompt }} {{ site.sched.status }} {{ site.sched.flag.user }}
```


{% include {{ site.snippets }}/scheduler/terminate-job-begin.snip %}

Now cancel the job with its job number (printed in your terminal). A clean
return of your command prompt indicates that the request to cancel the job was
successful.

```
{{ site.remote.prompt }} {{site.sched.del }} 38759
# It might take a minute for the job to disappear from the queue...
{{ site.remote.prompt }} {{ site.sched.status }} {{ site.sched.flag.user }}
```


{% include {{ site.snippets }}/scheduler/terminate-job-cancel.snip %}

{% include {{ site.snippets }}/scheduler/terminate-multiple-jobs.snip %}

## Other Types of Jobs

Up to this point, we've focused on running jobs in batch mode.
{{ site.sched.name }} also provides the ability to start an interactive session.

There are very frequently tasks that need to be done interactively. Creating an
entire job script might be overkill, but the amount of resources required is
too much for a login node to handle. A good example of this might be building a
genome index for alignment with a tool like [HISAT2][hisat]. Fortunately, we
can run these types of tasks as a one-off with `{{ site.sched.interactive }}`.

{% include {{ site.snippets }}/scheduler/using-nodes-interactively.snip %}


## Tutorial: Serial jobs

> In this tutorial we'll get familiar with the basic usage of the Slurm batch queue system on LUMI. 
- The goal is to learn how to request resources that **match** the needs of a job

💬 A batch job consists of two parts: resource requests and the job step(s)

☝🏻 Examples are done on LUMI. If using the web interface, open a login node shell.

## Serial jobs

💬 A serial program can only use one core (CPU)

- One should request only a single core from Slurm
- The job does not benefit from additional cores
- Excess cores are wasted since they will not be available to other users

💬 Within the job (or allocation), the actual program is launched using the command `srun`

☝🏻 If you use a software that is available in the LUMI software stack, please check the [software library page](https://lumi-supercomputer.github.io/LUMI-EasyBuild-docs/); it might have a batch job example with useful default settings.
In case you are using an installation by a local organization (e.g. CSC), [check the documentation pages](https://docs.lumi-supercomputer.eu/software/local/csc/) there. 

### Launching a serial job

1. Go to the `/scratch` directory of your project:

```bash
cd /scratch/<project>      # replace <project> with your LUMI project, e.g. project_465000000
```

- Now your input (and output) will be on a shared disk that is accessible to the compute nodes.

💡 You can list your projects with `lumi-workspaces`

💡 Note! If you're using a project with other members (like the course project), first make a subdirectory for yourself (e.g. `mkdir $USER` and then move there (`cd $USER`) to not clutter the `/scratch` root of your project)

{:style="counter-reset:step-counter 1"}
2. Create a file called `my_serial.bash` e.g. with the `nano` text editor:

```bash
nano my_serial.bash
```

{:style="counter-reset:step-counter 2"}
3. Copy the following **batch script** there and change `<project>` to the LUMI project you actually want to use:

```bash
#!/bin/bash
#SBATCH --account=<project>      # Choose the billing project. Has to be defined!
#SBATCH --time=00:02:00          # Maximum duration of the job. Upper limit depends on the partition. 
#SBATCH --partition=debug        # Job queues: debug, interactive, small, standard, small-g, standard-g, largemem, lumid, ..
#SBATCH --ntasks=1               # Number of tasks. Upper limit depends on partition. For a serial job this should be set 1!

srun hostname                    # Run hostname-command
srun sleep 60                    # Run sleep-command
```

{:style="counter-reset:step-counter 3"}
4. Submit the job to the batch queue and check its status with the commands:

```bash
sbatch my_serial.bash
squeue -u $USER
```

💬 In the batch job example above we are requesting

- one core (`--ntasks=1`)
- for two minutes (`--time=00:02:00`)
- from the test queue (`--partition=debug`)  

💬 We want to run the program `hostname` that will print the name of the LUMI compute node that has been allocated for this particular job

💬 In addition, we are running the `sleep` program to keep the job running for an additional 60 seconds, in order to have time to monitor the job

#### Checking the output

- By default, the output is written to a file named `slurm-<jobid>.out` where `<jobid>` is a unique job ID assigned to the job by Slurm

💭 You can get a list of all your jobs that are running or queuing with the command `squeue -u $USER`

🗯 A submitted job can be cancelled using the command `scancel <jobid>`

## More information

💡 [Batch jobs](https://docs.lumi-supercomputer.eu/runjobs/scheduled-jobs/slurm-quickstart/) in LUMI documentation





{% include links.md %}

[fshs]: https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard
[hisat]: https://daehwankimlab.github.io/hisat2/
