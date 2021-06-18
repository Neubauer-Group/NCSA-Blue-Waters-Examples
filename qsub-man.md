qsub(1B)                              PBS                             qsub(1B)



NAME
       qsub - submit pbs job

SYNOPSIS
       qsub   [-a   date_time]   [-A  account_string]  [-b  secs]  [-c  check-
       point_options] [-C directive_prefix] [-d path] [-D path] [-e path] [-f]
       [-h]  [-I]  [-j  join] [-k keep] [-K kill_delay] [-l resource_list] [-m
       mail_options] [-M user_list] [-n node_exclusive] [-N  name]  [-o  path]
       [-p  priority] [-P proxy_username[:group]]  [-q destination] [-r c] [-S
       path_list] [-t array_request] [-T  prologue/epilogue  script_name]  [-u
       user_list] [-v variable_list] [-V] [-w path] [-W additional_attributes]
       [-x] [-X] [-z] [script]

DESCRIPTION
       To create a job is to submit an executable script to  a  batch  server.
       The  batch  server  will  be the default server unless the -q option is
       specified.  See discussion of PBS_DEFAULT under  Environment  Variables
       below.   Typically, the script is a shell script which will be executed
       by a command shell such as sh or csh.

       Options on the qsub command allow the specification of attributes which
       affect the behavior of the job.

       The  qsub  command will pass certain environment variables in the Vari-
       able_List attribute of the job.  These variables will be  available  to
       the  job.  The value for the following variables will be taken from the
       environment of the qsub  command:  HOME,  LANG,  LOGNAME,  PATH,  MAIL,
       SHELL,  and  TZ.   These values will be assigned to a new name which is
       the current name prefixed with the string "PBS_O_".  For  example,  the
       job  will have access to an environment variable named PBS_O_HOME which
       have the value of the variable HOME in the qsub command environment.

       In addition to the above, the following environment variables  will  be
       available to the batch job.

       PBS_O_HOST
              the name of the host upon which the qsub command is running.

       PBS_SERVER
              the hostname of the pbs_server which qsub submits the job to.

       PBS_O_QUEUE
              the name of the original queue to which the job was submitted.

       PBS_O_WORKDIR
              the  absolute  path of the current working directory of the qsub
              command.

       PBS_ARRAYID
              each member of a job array is assigned a unique identifier  (see
              -t)

       PBS_ENVIRONMENT
              set  to  PBS_BATCH  to  indicate  the  job is a batch job, or to
              PBS_INTERACTIVE to indicate the job is a  PBS  interactive  job,
              see -I option.

       PBS_JOBID
              the job identifier assigned to the job by the batch system.

       PBS_JOBNAME
              the job name supplied by the user.

       PBS_NODEFILE
              the  name  of the file contain the list of nodes assigned to the
              job (for parallel and cluster systems).

       PBS_QUEUE
              the name of the queue from which the job is executed.

OPTIONS
       -a date_time
               Declares the time after which the job is  eligible  for  execu-
               tion.

               The      date_time     argument     is     in     the     form:
               [[[[CC]YY]MM]DD]hhmm[.SS]

               Where CC is the first two digits of the year (the century),  YY
               is  the second two digits of the year, MM is the two digits for
               the month, DD is the day of the month, hh is the  hour,  mm  is
               the minute, and the optional SS is the seconds.

               If the month, MM, is not specified, it will default to the cur-
               rent month if the specified day DD, is in the  future.   Other-
               wise,  the  month  will be set to next month.  Likewise, if the
               day, DD, is not specified, it will default to today if the time
               hhmm  is  in  the  future.   Otherwise,  the day will be set to
               tomorrow.  For example, if you submit a job at 11:15am  with  a
               time  of  -a  1110,  the job will be eligible to run at 11:10am
               tomorrow.

       -A account_string
               Defines the  account  string  associated  with  the  job.   The
               account_string  is  an  undefined  string  of characters and is
               interpreted by the server which executes the job.  See  section
               2.7.1 of the PBS ERS.

       -b seconds
               Defines  the maximum number of seconds qsub will block attempt-
               ing to contact pbs_server.  If pbs_server is  down,  or  for  a
               variety  of communication failures, qsub will continually retry
               connecting to pbs_server for job submission.  This value  over-
               rides  the CLIENTRETRY parameter in torque.cfg.  This is a non-
               portable TORQUE extension.  Portability-minded  users  can  use
               the  PBS_CLIENTRETRY  environmental variable.  A negative value
               is interpreted as infinity.  The default is 0.

       -c checkpoint_options
               Defines the options that will apply to the  job.   If  the  job
               executes  upon  a host which does not support checkpoint, these
               options will be ignored.

               Valid checkpoint options are:

               none
                  No checkpointing is to be performed.

               enabled
                  Specify that checkpointing is allowed but must be explicitly
                  invoked by either the qhold or qchkpt commands.

               shutdown
                  Specify that checkpointing is to be done on a job at pbs_mom
                  shutdown.

               periodic
                  Specify that periodic checkpointing is enabled. The  default
                  interval  is  10  minutes  and can be changed by the $check-
                  point_interval  option  in  the  mom  config  file   or   by
                  specifying an interval when the job is submitted

               interval=minutes
                  Checkpointing  is to be performed at an interval of minutes,
                  which is the integer number of minutes of wall time used  by
                  the job.  This value must be greater than zero.

               depth=number
                  Specify  a number (depth) of checkpoint images to be kept in
                  the checkpoint directory.

               dir=path
                  Specify    a    checkpoint     directory     (default     is
                  /var/spool/torque/checkpoint).

       -C directive_prefix
               Defines  the  prefix that declares a directive to the qsub com-
               mand within the script  file.   See  the  paragraph  on  script
               directives in the Extended Description section.

               If  the -C option is presented with a directive_prefix argument
               that is the null string, qsub will not scan the script file for
               directives.

       -d path Defines  the working directory path to be used for the job.  If
               the -d option is not specified, the default  working  directory
               is  the home directory.  This option sets the environment vari-
               able PBS_O_INITDIR.

       -D path Defines the root directory to be used for the job.  This option
               sets the environment variable PBS_O_ROOTDIR.

       -e path Defines  the  path  to be used for the standard error stream of
               the batch job.  The path argument is of the form:
                   [hostname:][path_name]
               where hostname is the name of a host to which the file will  be
               returned  and  path_name  is  the path name on that host in the
               syntax recognized by POSIX.  The argument will  be  interpreted
               as follows:

               path_name
                      Where  path_name  is not an absolute path name, then the
                      qsub command will expand the path name relative  to  the
                      current  working  directory of the command.  The command
                      will supply the name of the host upon which it  is  exe-
                      cuting for the hostname component.

               hostname:path_name
                      Where  path_name  is not an absolute path name, then the
                      qsub command will not expand the path name  relative  to
                      the current working directory of the command.  On deliv-
                      ery of  the  standard  error,  the  path  name  will  be
                      expanded  relative  to  the user's home directory on the
                      hostname system.

               path_name
                      Where path_name specifies an absolute  path  name,  then
                      the qsub will supply the name of the host on which it is
                      executing for the hostname

               hostname:path_name
                      Where path_name specifies an  absolute  path  name,  the
                      path will be used as specified.  hostname.

               hostname:
                      Where  hostname  specifies the name of the host that the
                      file should be returned to. The path will be the default
                      file name.

               If the -e option is not specified or the path_name is not spec-
               ified or is specified and is a directory, the default file name
               for  the  standard error stream will be used.  The default name
               has the following form:
                   job_name.esequence_number
               where job_name is the name of  the  job,  see  -N  option,  and
               sequence_number is the job number assigned when the job is sub-
               mitted.

       -f      Specifies that the job is fault  tolerant.  The  fault_tolerant
               attribute will be set to true, which indicates that the job can
               survive the loss of a mom other than the "mother superior"  mom
               (the first node in the exec hosts )

       -h      Specifies  that a user hold be applied to the job at submission
               time.

       -I      Declares that the job is to be run  "interactively".   The  job
               will  be  queued  and  scheduled as any PBS batch job, but when
               executed, the standard input, output, and error streams of  the
               job are connected through qsub to the terminal session in which
               qsub is running.  Interactive jobs are forced to not rerunable.
               See  the "Extended Description" paragraph for addition informa-
               tion of interactive jobs.

       -j join Declares if the standard error stream of the job will be merged
               with the standard output stream of the job.

               An  option  argument  value  of oe directs that the two streams
               will be merged, intermixed,  as  standard  output.   An  option
               argument  value  of  eo  directs  that  the two streams will be
               merged, intermixed, as standard error.

               If the join argument is n or the option is not  specified,  the
               two streams will be two separate files.

       -k keep Defines  which (if either) of standard output or standard error
               will be retained on the execution host.  If set for  a  stream,
               this  option  overrides  the path name for that stream.  If not
               set, neither stream is retained on the execution host.

               The argument is either the single letter "e"  or  "o",  or  the
               letters  "e" and "o" combined in either order.  Or the argument
               is the letter "n".

               e  The standard error stream is to retained  on  the  execution
                  host.   The   stream will be placed in the home directory of
                  the user under whose user id the  job  executed.   The  file
                  name  will  be the default file name given by: job_name.ese-
                  quence where job_name is the name specified for the job, and
                  sequence is the sequence number component of the job identi-
                  fier.

               o  The standard output stream is to retained on  the  execution
                  host.   The   stream will be placed in the home directory of
                  the user under whose user id the  job  executed.   The  file
                  name  will  be the default file name given by: job_name.ose-
                  quence where job_name is the name specified for the job, and
                  sequence is the sequence number component of the job identi-
                  fier.

               eo Both the standard output and standard error streams will  be
                  retained.

               oe Both  the standard output and standard error streams will be
                  retained.

               n  Neither stream is retained.

       -K kill_delay
               Specifies a job specific kill delay in seconds. When specified,
               this  job  will  be  sent  a SIGTERM, followed by a SIGKILL the
               specified number of seconds later.

       -l resource_list
               Defines the resources that are required by the job  and  estab-
               lishes  a limit to the amount of resource that can be consumed.
               If not set for a generally  available  resource,  such  as  CPU
               time,  the limit is infinite.  The resource_list argument is of
               the form:
                   resource_name[=[value]][,resource_name[=[value]],...]

       -m mail_options
               Defines the set of conditions under which the execution  server
               will send a mail message about the job.  The mail_options argu-
               ment is a string which consists of either the single  character
               "n" or "p", or one or more of the characters "a", "b", "e", and
               "f".

               If the character "n" is specified, no normal mail is sent. Mail
               for job cancels and other events outside of normal job process-
               ing are still sent.  If the character "p"  is  specified,  mail
               will never be sent for the job.

               For the characters "a", "b", "e", and "f":

               a  Mail is sent when the job is aborted by the batch system.

               b  Mail is sent when the job begins execution.

               e  Mail is sent when the job terminates.

               f  Mail  is  sent  when the job terminates with a non-zero exit
                  code.

               If the -m option is not specified, mail will be sent if the job
               is aborted.


       -M user_list
               Declares  the  list of users to whom mail is sent by the execu-
               tion server when it sends mail about the job.

               The user_list argument is of the form:
                   user[@host][,user[@host],...]
               If unset, the list defaults to the submitting user at the  qsub
               host, i.e. the job owner.

       -n node_exclusive

               Allows  a  user  to specify an exclusive-node access/allocation
               request for the job. This will set node_exclusive = True in the
               output  of qstat -f <job ID>. By default, this only applies for
               cpusets, and only for compatible schedulers.

               For systems that use Moab and have cgroups enabled, the  recom-
               mended  manner  for  assigning all cores is to use NUMA syntax:
               "-L tasks=<count>:lprocs=all:place=node".

               With cgroups, the ("-l") syntax (lowercase L) will, by default,
               restrict   to   the  number  of  cores  requested,  or  to  the
               resources_default.procs value (i.e.,  1  core,  typically).  In
               order  to  override  this behavior and have Moab assign all the
               cores on a node while using "-l...singlejob"  and/or  "-n"  (in
               other  words, without "-L ...lprocs=all..."), you must also set
               RMCFG[<torque>] FLAGS=MigrateAllJobAttributes in moab.cfg.



       -N name Declares a name for the job.  The name specified may be  up  to
               and  including  15  characters  in  length.  It must consist of
               printable, non white space characters with the first  character
               alphabetic.

               If  the  -N  option  is not specified, the job name will be the
               base name of the job script file specified on the command line.
               If  no  script  file name was specified and the script was read
               from the standard input, then the  job  name  will  be  set  to
               STDIN.

       -o path Defines  the  path to be used for the standard output stream of
               the batch job.  The path argument is of the form:
                   [hostname:][path_name]
               where hostname is the name of a host to which the file will  be
               returned  and  path_name  is  the path name on that host in the
               syntax recognized by POSIX.  The argument will  be  interpreted
               as follows:

               path_name
                      Where  path_name  is not an absolute path name, then the
                      qsub command will expand the path name relative  to  the
                      current  working  directory of the command.  The command
                      will supply the name of the host upon which it  is  exe-
                      cuting for the hostname component.

               hostname:path_name
                      Where  path_name  is not an absolute path name, then the
                      qsub command will not expand the path name  relative  to
                      the current working directory of the command.  On deliv-
                      ery of the  standard  output,  the  path  name  will  be
                      expanded  relative  to  the user's home directory on the
                      hostname system.

               path_name
                      Where path_name specifies an absolute  path  name,  then
                      the qsub will supply the name of the host on which it is
                      executing for the hostname

               hostname:path_name
                      Where path_name specifies an  absolute  path  name,  the
                      path will be used as specified.  hostname.

               hostname:
                      Where  hostname  specifies the name of the host that the
                      file should be returned to. The path will be the default
                      file name.

               If the -o option is not specified or the path_name is not spec-
               ified or is specified and is a directory, the default file name
               for  the standard output stream will be used.  The default name
               has the following form:
                   job_name.osequence_number
               where job_name is the name of  the  job,  see  -N  option,  and
               sequence_number is the job number assigned when the job is sub-
               mitted.

       -p priority
               Defines the priority of the job.  The priority argument must be
               a integer between -1024 and +1023 inclusive.  The default is no
               priority which is equivalent to a priority of zero.

       -P proxy_user[:group]
               Proxy user for whom the job should be submitted.   This  option
               is only available for the super user.

       -q destination
               Defines  the  destination  of the job.  The destination names a
               queue, a server, or a queue at a server.

               The qsub command will submit the script to the  server  defined
               by  the  destination argument.  If the destination is a routing
               queue, the job may be routed by the server to  a  new  destina-
               tion.

               If the -q option is not specified, the qsub command will submit
               the script to the default server.  See  PBS_DEFAULT  under  the
               Environment  Variables section on this man page and the PBS ERS
               section 2.7.4, "Default Server".

               If the -q option is specified, it is in one  of  the  following
               three forms:
                   queue
                   @server
                   queue@server

               If  the  destination argument names a queue and does not name a
               server, the job will be submitted to the  named  queue  at  the
               default server.

               If  the destination argument names a server and does not name a
               queue, the job will be submitted to the default  queue  at  the
               named server.

               If  the  destination  argument names both a queue and a server,
               the job will be submitted to  the  named  queue  at  the  named
               server.

       -r y|n  Declares whether the job is rerunable.  See the qrerun command.
               The option argument is a single character, either y or n.

               If the argument is "y", the job is rerunable.  If the  argument
               is  "n",  the  job is not rerunable.  The default value is 'y',
               rerunable.

       -S path_list
               Declares the shell that interprets the job script.

               The option argument path_list is in the form:
                   path[@host][,path[@host],...]
               Only one path may be specified for any host  named.   Only  one
               path may be specified without the corresponding host name.  The
               path selected will be the one with the host name  that  matched
               the  name of the execution host.  If no matching host is found,
               then the path specified without a host  will  be  selected,  if
               present.

               If  the  -S option is not specified, the option argument is the
               null string, or no entry from the path_list  is  selected,  the
               execution  will  use  the  user's  login shell on the execution
               host.

       -t array_request
               Specifies the task ids of a job array.  Single task arrays  are
               allowed.

               The array_request argument is an integer id or a range of inte-
               gers. Multiple ids or id ranges can  be  combined  in  a  comma
               delimted list. Examples : -t 1-100 or -t 1,10,50-100

               An  optional slot limit can be specified to limit the amount of
               jobs that can run concurrently in the job  array.  The  default
               value is unlimited. The slot limit must be the last thing spec-
               ified in the array_request and is delimited from the array by a
               percent sign (%).

               qsub script.sh -t 0-299%5

               This  sets the slot limit to 5. Only 5 jobs from this array can
               run at the same time.

               Note: You can use qalter to modify slot limits on an array. The
               server  parameter  max_slot_limit  can  be used to set a global
               slot limit policy.

       -T script_name
               Allows for per job prologue  and  epilogue  scripts.  The  full
               script name will be prologue.[name] or epilogue.[name]. For the
               job submission, only request the name of the prologue  or  epi-
               logue script.

               Example: qsub -T prescript
               Specifies to use the script prologue.prescript

       -u user_list
               Defines the user name under which the job is to run on the exe-
               cution system.

               The user_list argument is of the form:
                   user[@host][,user[@host],...]
               Only one user name may be given per specified host.   Only  one
               of  the  user specifications may be supplied without the corre-
               sponding host specification.  That user name will used for exe-
               cution  on  any host not named in the argument list.  If unset,
               the user list defaults to the user who is running qsub.

       -v variable_list
               Expands the list of environment variables that are exported  to
               the job.

               In  addition  to  the  variables described in the "Description"
               section above, variable_list names environment  variables  from
               the  qsub  command  environment which are made available to the
               job when it executes.  The variable_list is a  comma  separated
               list  of strings of the form variable or variable=value.  These
               variables and their values are passed to the job.

       -V      Declares that all environment variables in the  qsub  command's
               environment are to be exported to the batch job.

       -w path Defines  the working directory path to be used for the job.  If
               the -w option is not specified, the default  working  directory
               is  the  current  directory.   This option sets the environment
               variable PBS_O_WORKDIR.

       -W additional_attributes
               The -W option allows for the specification  of  additional  job
               attributes.  The general syntax of the -W is in the form:
                   -W attr_name=attr_value[,attr_name=attr_value...]
               Note  if white space occurs anywhere within the option argument
               string or the equal sign, "=", occurs within an attribute_value
               string,  then the string must be enclosed with either single or
               double quote marks.

               PBS currently supports the following attributes within  the  -W
               option.

               depend=dependency_list
               Defines the dependency between this and other jobs.  The depen-
               dency_list is in the form:
               type[:argument[:argument...][,type:argument...].
               The argument is either a numeric count or a PBS job id  accord-
               ing  to type .  If argument is a count, it must be greater than
               0.  If it is a job id and  not  fully  specified  in  the  form
               seq_number.server.name,  it  will  be expanded according to the
               default server rules which apply to job IDs on  most  commands.
               If  argument  is  null  (the preceding colon need not be speci-
               fied), the dependency of  the  corresponding  type  is  cleared
               (unset).

                   synccount:count
                       This  job  is the first in a set of jobs to be executed
                       at the same time.  Count is the  number  of  additional
                       jobs in the set.

                   syncwith:jobid
                       This job is an additional member of a set of jobs to be
                       executed at the same time.  In the above and  following
                       dependency  types,  jobid  is the job identifier of the
                       first job in the set.

                   after:jobid[:jobid...]
                       This job may be scheduled for execution  at  any  point
                       after jobs jobid have started execution.

                   afterok:jobid[:jobid...]
                       This job may be scheduled for execution only after jobs
                       jobid have terminated with  no  errors.   See  the  csh
                       warning under "Extended Description".

                   afternotok:jobid[:jobid...]
                       This job may be scheduled for execution only after jobs
                       jobid have terminated with errors.  See the csh warning
                       under "Extended Description".

                   afterany:jobid[:jobid...]
                       This  job  may  be  scheduled  for execution after jobs
                       jobid have terminated, with or without errors.

                   on:count
                       This job may be scheduled  for  execution  after  count
                       dependencies  on  other jobs have been satisfied.  This
                       form is used in conjunction  with  one  of  the  before
                       forms, see below.

                   before:jobid[:jobid...]
                       When  this  job has begun execution, then jobs jobid...
                       may begin.

                   beforeok:jobid[:jobid...]
                       If this job terminates execution without  errors,  then
                       jobs  jobid...  may  begin.   See the csh warning under
                       "Extended Description".

                   beforenotok:jobid[:jobid...]
                       If this job terminates execution with errors, then jobs
                       jobid...   may   begin.   See  the  csh  warning  under
                       "Extended Description".

                   beforeany:jobid[:jobid...]
                       When this job terminates execution, jobs  jobid...  may
                       begin.

                       If  any  of  the before forms are used, the jobs refer-
                       enced by jobid must have been submitted with  a  depen-
                       dency type of on.

                   Array Dependencies
                       It  is  now  possible to have a job depend on an array.
                       These  dependencies  are  in  the  form   depend=array-
                       dep:arrayid[num].  If  [num]  is  not present, then the
                       dependencies applies to the entire array. If  [num]  is
                       present,  then  num  means the number of jobs that must
                       meet the condition for the dependency to be  satisfied.

                   afterstartarray:arrayid[count]
                       This job may be scheduled for execution only after jobs
                       in arrayid have started execution.

                   afterokarray:arrayid[count]
                       This job may be scheduled for execution only after jobs
                       in arrayid have terminated with no errors.

                   afternotok:arrayid[count]
                       This job may be scheduled for execution only after jobs
                       in arrayid have terminated with errors.

                   afteranyarray:arrayid[count]
                       This job may be scheduled for execution after  jobs  in
                       array id have terminated, with or without errors.

                   beforestartarray:arrayid[count]
                       This  job  may  be  scheduled for execution only before
                       jobs in arrayid have started execution.

                   beforeokarray:arrayid[count]
                       This job may be scheduled  for  execution  only  before
                       jobs in arrayid have terminated with no errors.

                   beforenotok:arrayid[count]
                       This  job  may  be  scheduled for execution only before
                       jobs in arrayid have terminated with errors.

                   beforeanyarray:arrayid[count]
                       This job may be scheduled for execution before jobs  in
                       array id have terminated, with or without errors.

                       If  any  of  the before forms are used, the jobs refer-
                       enced by jobid must have the  same  owner  as  the  job
                       being submitted.  Otherwise, the dependency is ignored.

                   Error processing of the existence, state, or  condition  of
                   the job on which the newly submitted job is a deferred ser-
                   vice, i.e. the check is performed after the job is  queued.
                   If an error is detected, the new job will be deleted by the
                   server.  Mail will be sent to the job submitter stating the
                   error.

                   Dependency examples:
                   qsub -W depend=afterok:123.big.iron.com /tmp/script
                   qsub      -W      depend=before:234.hunk1.com:235.hunk1.com
                   /tmp/script
                   qsub -W depend=afterokarray:21.tom.com[] /tmp/script
                   qsub -W depend=beforenotokarray:22.tom.com[][5] /tmp/script

               group_list=g_list
               Defines  the  group  name  under which the job is to run on the
               execution system.  The g_list argument is of the form:
               group[@host][,group[@host],...]
               Only one group name may be given per specified host.  Only  one
               of  the group specifications may be supplied without the corre-
               sponding host specification.  That group  name  will  used  for
               execution  on  any host not named in the argument list.  If not
               set, the group_list defaults to the primary group of  the  user
               under which the job will be run.

               interactive=true
               If the interactive attribute is specified, the job is an inter-
               active job.  The -I option is a alternative method of  specify-
               ing this attribute.

               stagein=file_list
               stageout=file_list
               Specifies  which  files are staged (copied) in before job start
               or staged out after the job completes execution.  On completion
               of the job, all staged-in and staged-out files are removed from
               the execution system.  The file_list is in the form
               local_file@hostname:remote_file[,...]
               regardless of the direction of the copy.  The  name  local_file
               is  the  name of the file on the system where the job executed.
               It may be an absolute path or relative to the home directory of
               the  user.  The name remote_file is the destination name on the
               host specified by hostname.  The name may be absolute or  rela-
               tive to the user's home directory on the destination host.  The
               use of wildcards in the file name is not recommended.  The file
               names  map to a remote copy program (rcp) call on the execution
               system in the follow manner:
               For stagein:   rcp hostname:remote_file local_file
               For stageout:  rcp local_file hostname:remote_file
               Data staging examples:
               -W stagein=/tmp/input.txt@headnode:/home/user/input.txt
               -W stageout=/tmp/output.txt@headnode:/home/user/output.txt
               If TORQUE has been compiled with wordexp  support,  then  vari-
               ables  can  be  used  in  the  specified paths.  Currently only
               $PBS_JOBID, $HOME, and $TMPDIR are supported for stagein.

               umask=XXX
               Sets umask used to create stdout  and  stderr  spool  files  in
               pbs_mom  spool directory. Values starting with 0 are treated as
               octal values, otherwise the value is treated as a decimal umask
               value.

       -x      When  running  an interactive job, the -x flag makes it so that
               the script won't be parsed for PBS directives, but instead will
               be  a  command  that  is  launched once the interactive job has
               started. The job will terminate at the completion of this  com-
               mand.

       -X      Enables  X11 forwarding.  The DISPLAY environment variable must
               be set.

       -z      Directs that the qsub command is not to write the  job  identi-
               fier assigned to the job to the command's standard output.


OPERANDS
       The  qsub  command  accepts  a  script  operand that is the path to the
       script of the job.  If the path is relative, it will be expanded  rela-
       tive to the working directory of the qsub command.

       If  the  script  operand  is  not provided or the operand is the single
       character "-", the qsub command reads the script from  standard  input.
       When  the  script is being read from Standard Input, qsub will copy the
       file to a temporary file.  This temporary file is passed to the library
       interface  routine  pbs_submit.   The temporary file is removed by qsub
       after pbs_submit returns or upon the receipt of a  signal  which  would
       cause qsub to terminate.

STANDARD INPUT
       The  qsub  command  reads the script for the job from standard input if
       the script operand is missing or is the single character "-".

INPUT FILES
       The script file is read by the qsub command.  Qsub acts upon any direc-
       tives found in the script.

       When  the  job  is  created, a copy of the script file is made and that
       copy cannot be modified.

STANDARD OUTPUT
       Unless the -z option is set, the job identifier  assigned  to  the  job
       will  be written to standard output if the job is successfully created.

STANDARD ERROR
       The qsub command will write a diagnostic message to standard error  for
       each error occurrence.

ENVIRONMENT VARIABLES
       The  values of some or all of the variables in the qsub command's envi-
       ronment are exported with the job, see the -v and -V options.

       The environment variable PBS_DEFAULT defines the name  of  the  default
       server.    Typically,  it corresponds to the system name of the host on
       which the server is running.  If PBS_DEFAULT is not set, the default is
       defined by an administrator established file.

       The environment variable PBS_DPREFIX determines the prefix string which
       identifies directives in the script.

       The environment variable PBS_CLIENTRETRY defines the maximum number  of
       seconds  qsub  will block.  See the -b option above.  Despite the name,
       currently qsub is the only client that supports this option.

TORQUE.CFG
       The torque.cfg file, located in PBS_SERVER_HOME  (/var/spool/torque  by
       default)  controls the behavior of the qsub command. This file contains
       a list of parameters and values separated by whitespace

       QSUBSLEEP takes an integer operand which specifies time to  sleep  when
       running  qsub  command.   Used  to  prevent users from overwhelming the
       scheduler.

       SUBMITFILTER specifies the path to the submit filter used  to  pre-pro-
       cess  job  submission.  The  default path is $(libexecdir)/qsub_filter,
       which falls back to /usr/local/sbin/torque_submitfilter  for  backwards
       compatibility. This torque.cfg parameter overrides this default.

       SERVERHOST specifies the value for the PBS_SERVER environment variable

       QSUBHOST specifies the hostname for the jobs QSUB_O_HOST variable

       QSUBSENDUID specifies a uid to use for the jobs PBS_O_UID variable

       XAUTHPATH specifies the path to xauth

       CLIENTRETRY  specifies  the  integer  seconds between retry attempts to
       communicate with pbs_server

       VALIDATEGROUP set this parameter to force qsub to  verify  the  submit-
       ter's group id

       DEFAULTCKPT  specifies  the  default  value  for  the  jobs  checkpoint
       attribute.  The user overrides this with the -c qsub option.

       VALIDATEPATH set this parameter to force qsub to validate  local  exis-
       tence of a "-d" working directory

       RERUNNABLEBYDEFAULT  this parameter specifies if a job is rerunnable by
       default. The  default  is  true,  setting  this  to  false  causes  the
       rerunnable  attribute value to be false unless the users specifies oth-
       erwise with the -r option

       FAULT_TOLERANT_BY_DEFAULT this parameter specifies if a  job  is  fault
       tolerant  by  default.   The  default  value for the fault_tolerant job
       attribute is false, setting this parameter to true causes  the  default
       value  of  the attribute to be true. The user can specify their prefer-
       ence with the -f qsub option.

       For example:
              QSUBSLEEP  2
              RERUNNABLEBYDEFAULT  false



EXTENDED DESCRIPTION
       Script Processing:

       A job script may consist of PBS  directives,  comments  and  executable
       statements.    A  PBS  directive  provides  a  way  of  specifying  job
       attributes in addition to the command line options.  For example:
              :
              #PBS -N Job_name
              #PBS -l walltime=10:30,mem=320kb
              #PBS -m be
              #
              step1 arg1 arg2
              step2 arg3 arg4


       The qsub command scans the lines of the script file for directives.  An
       initial  line in the script that begins with the characters "#!" or the
       character ":" will be ignored and scanning will  start  with  the  next
       line.   Scanning will continue until the first executable line, that is
       a line that is not blank, not a directive line, nor a line whose  first
       non  white  space  character is "#".  If directives occur on subsequent
       lines, they will be ignored.

       A line in the script file will be processed as a directive to  qsub  if
       and  only if the string of characters starting with the first non white
       space character on the line and of the same  length  as  the  directive
       prefix matches the directive prefix.

       The  remainder of the directive line consists of the options to qsub in
       the same syntax as they appear on the command line.  The option charac-
       ter is to be preceded with the "-" character.

       If  an  option  is present in both a directive and on the command line,
       that option and its argument, if any, will be ignored in the directive.
       The command line takes precedence.

       If  an  option  is  present in a directive and not on the command line,
       that option and its argument, if any, will be processed as  if  it  had
       occurred on the command line.

       The  directive  prefix string will be determined in order of preference
       from:

           The value of the -C option argument if the option is  specified  on
           the command line.

           The value of the environment variable PBS_DPREFIX if it is defined.

           The four character string #PBS.

       If the -C option is found in a directive in the script file, it will be
       ignored.

       User Authorization:

       When  the  user submits a job from a system other than the one on which
       the PBS Server is running, the name under which the job is to  be  exe-
       cuted  is  selected  according to the rules listed under the -u option.
       The user submitting the job must be authorized to run the job under the
       execution user name.  This authorization is provided if

              (1)  The  host  on which qsub is run is trusted by the execution
                   host (see /etc/hosts.equiv),

              (2)  The execution user has an .rhosts file naming  the  submit-
                   ting user on the submitting host.

       C-Shell .logout File:

       The  following  warning  applies for users of the c-shell, csh.  If the
       job is executed under the csh and a .logout file  exists  in  the  home
       directory in which the job executes, the exit status of the job is that
       of the .logout script, not the job script.  This may impact any  inter-
       job  dependencies.   To preserve the job exit status, either remove the
       .logout file or place the following line  as  the  first  line  in  the
       .logout file
          set EXITVAL = $status
       and the following line as the last executable line in .logout
          exit $EXITVAL

       Interactive Jobs:

       If the -I option is specified on the command line or in a script direc-
       tive, or if the "interactive" job attribute declared true  via  the  -W
       option,  -W interactive=true, either on the command line or in a script
       directive, the job is an interactive job.  The script will be processed
       for  directives,  but  will not be included with the job.  When the job
       begins execution, all input to the job is from the terminal session  in
       which qsub is running.

       When  an interactive job is submitted, the qsub command will not termi-
       nate when the job is submitted.  Qsub will remain running until the job
       terminates, is aborted, or the user interrupts qsub with an SIGINT (the
       control-C key).  If qsub is interrupted prior to  job  start,  it  will
       query  if  the  user  wishes to exit.  If the user response "yes", qsub
       exits and the job is aborted.

       Once the interactive job has started execution,  input  to  and  output
       from  the  job  pass  through  qsub.  Keyboard generated interrupts are
       passed to the job.  Lines entered that begin with the tilde ('~') char-
       acter  and  contain  special sequences are escaped by qsub.  The recog-
       nized escape sequences are:

              ~.     Qsub terminates execution.  The batch job is also  termi-
                     nated.

              ~susp  Suspend  the  qsub  program if running under the C shell.
                     "susp" is the suspend character, usually CNTL-Z.

              ~asusp Suspend the input half of qsub  (terminal  to  job),  but
                     allow  output  to  continue  to be displayed.  Only works
                     under the C shell.   "asusp"  is  the  auxiliary  suspend
                     character, usually CNTL-Y.


EXIT STATUS
       Upon  successful  processing,  the  qsub exit status will be a value of
       zero.

       If the qsub command fails, the command exits with a value greater  than
       zero.

SEE ALSO
       qalter(1B),   qdel(1B),  qhold(1B),  qmove(1B),  qmsg(1B),  qrerun(1B),
       qrls(1B),   qselect(1B),    qsig(1B),    qstat(1B),    pbs_connect(3B),
       pbs_job_attributes(7B),                       pbs_queue_attributes(7B),
       pbs_resources_irix5(7B),                         pbs_resources_sp2(7B),
       pbs_resources_sunos4(7B),                    pbs_resources_unicos8(7B),
       pbs_server_attributes(7B), and pbs_server(8B)




Local                                                                 qsub(1B)