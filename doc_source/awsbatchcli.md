# AWS ParallelCluster Batch CLI Commands<a name="awsbatchcli"></a>

When you use the `awsbatch` scheduler, the AWS ParallelCluster batch CLI commands are automatically installed in the AWS ParallelCluster master node\. The CLI uses AWS Batch APIs and permits to:
+ Submit and manage jobs
+ Monitor jobs, queues, and hosts
+ Mirror traditional scheduler commands

**Topics**
+ [`awsbsub`](#awsbatchcli.awsbsub)
+ [`awsbstat`](#pcluster.awsbstat.arguments)
+ [`awsbout`](awsbatchcli_awsbout.md)
+ [`awsbkill`](awsbatchcli_awsbkill.md)
+ [`awsbqueues`](awsbatchcli_awsbqueues.md)
+ [`awsbhosts`](awsbatchcli_awsbhosts.md)

## `awsbsub`<a name="awsbatchcli.awsbsub"></a>

Submits jobs to the cluster’s job queue\.

```
awsbsub [-h] [-jn JOB_NAME] [-c CLUSTER] [-cf] [-w WORKING_DIR]
        [-pw PARENT_WORKING_DIR] [-if INPUT_FILE] [-p VCPUS] [-m MEMORY]
        [-e ENV] [-eb ENV_BLACKLIST] [-r RETRY_ATTEMPTS] [-t TIMEOUT]
        [-n NODES] [-a ARRAY_SIZE] [-d DEPENDS_ON]
        [command] [arguments [arguments ...]]
```

### Positional Arguments<a name="awsbatchcli.awsbsub.args"></a>

`command`  
Submits the job \(the command specified must be available on the compute instances\), or specifies the file name to be transferred \(also see the `--command-file` option\)\.

`arguments`  
\(Optional\) Specifies arguments for the command or the command\-file\.

### Named Arguments<a name="awsbatchcli.awsbsub.namedargs"></a>

`-jn JOB_NAME, --job-name JOB_NAME`  
Names the job\. The first character must be alphanumeric\. The job name can contain up to 128 characters\. Letters \(both uppercase and lowercase\), numbers, hyphens, and underscores are allowed\.

`-c CLUSTER, --cluster CLUSTER`  
Specifies the cluster to use\.

`-cf, --command-file`  
Indicates that the command is a file to be transferred to the compute instances\.  
Default: False

`-w WORKING_DIR, --working-dir WORKING_DIR`  
Specifies the folder to use as the job's working directory\. If a working directory is not specified, the job is executed in the `job-<AWS_BATCH_JOB_ID>` subfolder of the user’s home directory\. You can use either this parameter or the `--parent-working-dir` parameter\.

`-pw PARENT_WORKING_DIR, --parent-working-dir PARENT_WORKING_DIR`  
Specfies the parent folder of the job's working directory\. If a parent working directory is not specified, it defaults to the user’s home directory\. A subfolder named `job-<AWS_BATCH_JOB_ID>` is created in the parent working directory\. You can use either this parameter or the `--working-dir` parameter\.

`-if INPUT_FILE, --input-file INPUT_FILE`  
Specifies the file to be transferred to the compute instances, in the job's working directory\. You can specify multiple input file parameters\.

`-p VCPUS, --vcpus VCPUS`  
Specifies the number of vCPUs to reserve for the container\. When used together with `–nodes`, it identifies the number of vCPUs per node\.  
Default: 1

`-m MEMORY, --memory MEMORY`  
Specifies the hard limit of memory \(in MiB\) to provide for the job\. If your job attempts to exceed the memory limit specified here, the job is killed\.  
Default: 128

`-e ENV, --env ENV`  
Specifies a comma\-separated list of environment variable names to export to the job environment\. To export all environment variables, specify ‘all’\. Note that a list of 'all' environment variables will not include those listed in the `–env-blacklist` parameter, or variables starting with the `PCLUSTER_*` or `AWS_*` prefix\.

`-eb ENV_BLACKLIST, --env-blacklist ENV_BLACKLIST`  
Specifies a comma\-separated list of environment variable names to **not** export to the job environment\. By default, `HOME`, `PWD`, `USER`, `PATH`, `LD_LIBRARY_PATH`, `TERM`, and `TERMCAP` are not exported\.

`-r RETRY_ATTEMPTS, --retry-attempts RETRY_ATTEMPTS`  
Specifies the number of times to move a job to the RUNNABLE status\. You can specify between 1 and 10 attempts\. If the value of attempts is greater than 1, the job is retried if it fails, until it has moved to RUNNABLE that specified number of times\.  
Default: 1

`-t TIMEOUT, --timeout TIMEOUT`  
Specifies the time duration in seconds \(measured from the job attempt’s startedAt timestamp\) after which AWS Batch terminates your job if it has not finished\. The timeout value must be at least 60 seconds\.

`-n NODES, --nodes NODES`  
Specifies the number of nodes to reserve for the job\. Specify a value for this parameter to enable multi\-nNode parallel submission\.

`-a ARRAY_SIZE, --array-size ARRAY_SIZE`  
Indicates the size of the array\. You can specify a value between 2 and 10,000\. If you specify array properties for a job, it becomes an array job\.

`-d DEPENDS_ON, --depends-on DEPENDS_ON`  
Specifies a semicolon\-separated list of dependencies for a job\. A job can depend upon a maximum of 20 jobs\. You can specify a SEQUENTIAL type dependency without specifying a job ID for array jobs\. A sequential dependency allows each child array job to complete sequentially, starting at index 0\. You can also specify an N\_TO\_N type dependency with a job ID for array jobs\. An N\_TO\_N dependency means that each index child of this job must wait for the corresponding index child of each dependency to complete before it can begin\. The syntax for this parameter is "jobId=*<string>*,type=<string>;\.\.\."\.

## `awsbstat`<a name="pcluster.awsbstat.arguments"></a>

Shows the jobs that are submitted in the cluster’s job queue\.

```
awsbstat [-h] [-c CLUSTER] [-s STATUS] [-e] [-d] [job_ids [job_ids ...]]
```

### Positional Arguments<a name="pcluster.awsbstat.arguments"></a>

`job_ids`  
Specifies the space\-separated list of job IDs to show in the output\. If the job is a job array, all of the children are displayed\. If a single job is requested, it is shown in a detailed version\.

### Named Arguments<a name="pcluster.awsbstat.namedarguments"></a>

`-c CLUSTER, --cluster CLUSTER`  
Indicates the cluster to use\.

`-s STATUS, --status STATUS`  
Specifies a comma\-separated list of job statuses to include\. The default job status is “active\.”\. Accepted values are: `SUBMITTED`, `PENDING`, `RUNNABLE`, `STARTING`, `RUNNING`, `SUCCEEDED`, `FAILED`, and `ALL`\.  
Default: “`SUBMITTED`,`PENDING`,`RUNNABLE`,`STARTING`,`RUNNING`”

`-e, --expand-children`  
Expands jobs with children \(both array and multi\-node parallel\)\.  
Default: False

`-d, --details`  
Shows jobs details\.  
Default: False