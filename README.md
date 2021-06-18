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

**Example: Running a Docker image**

From the Blue Waters login node start an interactive Shifter job on a single node with 32 processes per node (ppn) on an XE node (**N.B.** XK is required for GPUs) for a maximum of 1 hour

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
aprun -b -N 1 -- shifter --image=centos:7 -- /bin/bash -i
```

**N.B.:** If you don't include the interactive flag `-i` `shifter` will run the image with no prompt, so you will have to guess when execution finishes in the interactive session.

Finding Docker images that can work with Blue Waters can be a bit tricky given

> when you prepare a Docker image for your application, make sure to use a base image that provides `glibc` that supports the version of Linux kernel installed on Blue Waters, which is version `3.0.101`.
> This requirement is tricky to check automatically because it depends on the version of `glibc` and --enable-kernel flag used at the time `glibc` is compiled.
> If `glibc` provided by the operating system in the image does not support the version of Linux kernel installed on Blue Waters, consider using a different base image.

From a NCSA [help ticket](https://jira.ncsa.illinois.edu/browse/BWAPPS-6807), it was revealed that distributions with **`glibc` `v2.23` or older can be made compatible** with the Linux kernel on Blue Waters (`v3.0.101`), whereas `glibc` `v2.24` or newer **can not**.
So before you try to use a Docker image on Blue Waters check it locally.

---

**Examples:**

The `python:3.8` image can NOT be used

```
$ docker run --rm python:3.8 /bin/bash -c "ldd --version"
ldd (Debian GLIBC 2.28-10) 2.28
Copyright (C) 2018 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
Written by Roland McGrath and Ulrich Drepper.
```

but the `centos:7` image CAN be used

```
$ shifterimg pull centos:7
$ docker run --rm centos:7 /bin/bash -c "ldd --version"
ldd (GNU libc) 2.17
Copyright (C) 2012 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
Written by Roland McGrath and Ulrich Drepper.
```

If you tried to run an incompatible Docker image on Blue Waters you'd get a kernel fatal error like the following

```
$ shifterimg pull python:3.8
$ aprun -b -N 1 -cc none -- shifter --image=python:3.8 --entrypoint=/bin/bash
mount: warning: ufs seems to be mounted read-only.
mount: warning: dsl/opt seems to be mounted read-only.
FATAL: kernel too old
Application 96757362 exit codes: 127
Application 96757362 resources: utime ~0s, stime ~1s, Rss ~22900, inblocks ~40955, outblocks ~38774
```

---

### Running a shifter job with GPUs

Running on a GPU with shifter requires using the XK nodes

```
qsub -I -l gres=shifter -l nodes=1:ppn=8:xk -l walltime=01:00:00
```

and then loading both the `shifter` and `nvidia` modules

```
module load shifter
module load craype-accel-nvidia35
```

then the NVIDIA drivers and CUDA libraries can be picked up

```
aprun -b -N 1 -- shifter --image=centos:7 -- /bin/bash -c "nvidia-smi && nvcc --version"
mount: warning: ufs seems to be mounted read-only.
mount: warning: dsl/opt seems to be mounted read-only.
Thu Jan 14 23:28:33 2021
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 390.46                 Driver Version: 390.46                    |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla K20X          On   | 00000000:02:00.0 Off |                    0 |
| N/A   32C    P8    17W / 225W |      0MiB /  5700MiB |      0%   E. Process |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2017 NVIDIA Corporation
Built on Fri_Nov__3_21:07:56_CDT_2017
Cuda compilation tools, release 9.1, V9.1.85
```

Note that given that Blue Waters has CUDA 9 installed, and CUDA 10 will not be installed given that Blue Waters is entering end-of-life operations, modern machine learning frameworks like JAX, PyTorch (`v1.X`), and TensorFlow (`v2.X`) can not use the GPUs.

Blue Waters does have the [Blue Waters Python software stack (`bwpy`)](https://bluewaters.ncsa.illinois.edu/python), which can be loaded with

```
module load bwpy/<version you want here> # example bwpy/2.0.4
```

which includes PyTorch and TensorFlow, but quite old releases (`torch` `v0.X` and `tensorflow` `v1.X`) that are compatible with CUDA v9 only.


## Running Batch Jobs

As noted in the Blue Waters [Getting Started Guide section on batch jobs](https://bluewaters.ncsa.illinois.edu/getting-started/#Running), the Blue Waters batch environment is Torque/MOAB and the `aprun` utility is used to start jobs on compute nodes.

### Hello World example

The following is a "Hello World" example of executing a job with `aprun` in a [Portable Batch System](https://en.wikipedia.org/wiki/Portable_Batch_System) (PBS) batch script submitted to run with `qsub`.
