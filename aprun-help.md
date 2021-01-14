# `aprun` Help

```
$ aprun --help
Usage: aprun [global_options] [command_options] cmd1
          [: [command_options] cmd2 [: ...] ]
          [--help] [--version]

--help         Print this help information and exit
--version      Print version information
:              Separate binaries for MPMD mode
               (Multiple Program, Multiple Data)

Global Options:
-b, --bypass-app-transfer
    Bypass application transfer to compute node
-B, --batch-args
    Get values from Batch reservation for -n, -N, -d, and -m
-C, --reconnect
    Reconnect fanout control tree around failed nodes
-D, --debug level
    Debug level bitmask (0-7)
-e, --environment-override env
    Set an environment variable on the compute nodes
    Must use format VARNAME=value
    Set multiple env variables using multiple -e args
-m, --memory-per-pe size
    Per PE memory limit in megabytes
    (default node memory/number of processors)
    K|M|G suffix supported (16 == 16M == 16 megabytes)
    Add an 'h' suffix to request per PE huge page memory
    Add an 's' to the 'h' suffix to make the per PE huge page
    memory size strict (required)
-P, --pipes pipes
    Write[,read] pipes (not applicable for general use)
-p, --protection-domain pdi
    Protection domain identifier
-q, --quiet
    Quiet mode; suppress aprun non-fatal messages
-R, --relaunch max_shrink
    Relaunch application; max_shrink is zero or more maximum
    PEs to shrink for a relaunch
-T, --sync-output
    Use synchronous TTY
-t, --cpu-time-limit sec
    Per PE CPU time limit in seconds (default unlimited)

Command Options:
-a, --architecture arch
    Architecture type (only XT currently supported)
--cc, --cpu-binding cpu_list
    CPU binding list or keyword
    ([cpu#[,cpu# | cpu1-cpu2] | x]...] | keyword)
--cp, --cpu-binding-file file
    CPU binding placement filename
-d, --cpus-per-pe depth
    Number of CPUs allocated per PE (number of threads)
-E, --exclude-node-list node_list
    List of nodes to exclude from placement
--exclude-node-list-file node_list_file
    File with a list of nodes to exclude from placement
-F, --access-mode flag
    Exclusive or share node resources flag
-j, --cpus-per-cu CPUs
    CPUs to use per Compute Unit (CU)
-L, --node-list node_list
    Manual placement list (node[,node | node1-node2]...)
-l, --node-list-file node_list_file
    File with manual placement list
-N, --pes-per-node pes
    PEs per node
-n, --pes width
    Number of PEs requested
-r, --specialized-cpus CPUs
    Restrict this many CPUs per node to specialization
-S, --pes-per-numa-node pes
    PEs per NUMA node
--sl, --numa-node-list numa_node_list
    List of NUMA nodes to use
    (numa_node[,numa_node | numa_node1-numa_node2]...)
--sn, --numa-nodes-per-node numa_nodes
    Maximum number of NUMA nodes used on compute node
--ss, --strict-memory-containment
    Strict memory containment per NUMA node
```
