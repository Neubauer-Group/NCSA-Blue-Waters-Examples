# NCSA Blue Waters Examples

Examples of running basic jobs on NCSA's Blue Waters

## Login Node Splash Screen

```
   ___  __           _      __     __
  / _ )/ /_ _____   | | /| / /__ _/ /____ ___  ___
 / _  / / // / -_)  | |/ |/ / _ `/ __/ -_) __)(_-<
/____/_/\_,_/\__/   |__/|__/\_,_/\__/\__/_/  /___/
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Batch and Scheduler configuration.
  Queues:  normal (default), high, debug, noalloc
  Features:  "xe" (default), "xk", "x" (xe or xk non-specific)
             "xehimem" (128GB mem), "xkhimem" (64GB mem)
  30 min default wall time, 7 day maximum
  -lnodes=X:ppn=Y syntax supported.

All SSH traffic on this system is monitored.

Questions?  Mail help+bw@ncsa.illinois.edu to create a support ticket.
For known issues:  https://bluewaters.ncsa.illinois.edu/known-issues
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```

## Running a Job with Shifter

Most of this will follow the [NCSA Blue Waters documentation on Shifter](https://bluewaters.ncsa.illinois.edu/shifter), but it will additionally give some further information and asides to get new users up to speed.

> On Blue Waters, Shifter is provided as a module called `shifter`.
> Unlike other modules, it can only be loaded on a [MOM node](https://bluewaters.ncsa.illinois.edu/interactive-jobs) in a Shifter job, which is a job that requests `shifter` generic resource.

Note: A [Machine Oriented Mini-server](https://linux.die.net/man/8/pbs_mom) (MOM) node is a shared service resource that manages job execution, and on Blue Waters is accessed through starting an [interactive session](https://bluewaters.ncsa.illinois.edu/interactive-jobs).

**Example: Running the `pyhf:latest` Docker image**

From the Blue Waters login node start an interactive Shifter job on a single node with 32 processes per node (ppn) on an XE node for a maximum of 1 hour

```
qsub -I -l gres=shifter -l nodes=1:ppn=32:xe -l walltime=01:00:00
```

The job will then be queued up

```
INFO: Job submitted to account: abcd
qsub: waiting for job 12345689.bw to start
```

and when it returns (which can take some time) the user will be on a MOM node.
Once the job starts, load the `shifter` module

```
module load shifter
```

to start using the `shifter` and `shifterimg` commands.

```
aprun -b -N 1 -cc none -- shifter --image=python:3.6.8 -- /bin/bash
```

It seems that getting the correct Docker base images can be tricky given

> when you prepare a Docker image for your application, make sure to use a base image that provides `glibc` that supports the version of Linux kernel installed on Blue Waters, which is version `3.0.101`.
> This requirement is tricky to check automatically because it depends on the version of `glibc` and --enable-kernel flag used at the time `glibc` is compiled.
> If `glibc` provided by the operating system in the image does not support the version of Linux kernel installed on Blue Waters, consider using a different base image.

and it is seen that the official Python Docker images are not compliant

```
$ shifterimg pull python:3.8
$ aprun -b -N 1 -cc none -- shifter --image=python:3.8 --entrypoint=/bin/bash
mount: warning: ufs seems to be mounted read-only.
mount: warning: dsl/opt seems to be mounted read-only.
FATAL: kernel too old
Application 96757362 exit codes: 127
Application 96757362 resources: utime ~0s, stime ~1s, Rss ~22900, inblocks ~40955, outblocks ~38774
```
