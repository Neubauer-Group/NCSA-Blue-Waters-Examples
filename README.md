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
