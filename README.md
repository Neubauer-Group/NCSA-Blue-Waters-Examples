# NCSA Blue Waters Examples

Examples of running basic jobs on NCSA's Blue Waters

## Running a Job with Shifter

Most of this will follow the [NCSA Blue Waters docuementation on Shifter](https://bluewaters.ncsa.illinois.edu/shifter), but it will addiitonally give some further information and asides to get new users up to speed.

> On Blue Waters, Shifter is provided as a module called `shifter`.
> Unlike other modules, it can only be loaded on a [MOM node](https://bluewaters.ncsa.illinois.edu/interactive-jobs) in a Shifter job, which is a job that requests `shifter` generic resource.

Note: A [Machine Oriented Mini-server](https://linux.die.net/man/8/pbs_mom) (MOM) node is a shared service resource that manages job execution, and on Blue Waters is accessed through starting an [interactive session](https://bluewaters.ncsa.illinois.edu/interactive-jobs).
