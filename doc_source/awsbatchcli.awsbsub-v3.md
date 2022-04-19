# `awsbsub`<a name="awsbatchcli.awsbsub-v3"></a>

Submits jobs to the job queue of the cluster\.

```
awsbsub [-h] [-jn JOB_NAME] [-c CLUSTER] [-cf] [-w WORKING_DIR]
        [-pw PARENT_WORKING_DIR] [-if INPUT_FILE] [-p VCPUS] [-m MEMORY]
        [-e ENV] [-eb ENV_DENYLIST] [-r RETRY_ATTEMPTS] [-t TIMEOUT]
        [-n NODES] [-a ARRAY_SIZE] [-d DEPENDS_ON]
        [command] [arguments [arguments ...]]
```

## Positional Arguments<a name="awsbatchcli.awsbsub-v3.args"></a>

`command`  
Submits the job \(the command specified must be available on the compute instances\) or the file name to be transferred\. See also `--command-file`\.

`arguments`  
\(Optional\) Specifies arguments for the command or the command\-file\.

## Named Arguments<a name="awsbatchcli.awsbsub-v3.namedargs"></a>

`-jn JOB_NAME, --job-name JOB_NAME`  
Names the job\. The first character must be either a letter or number\. The job name can contain letters \(both uppercase and lowercase\), numbers, hyphens, and underscores, and be up to 128 characters in length\. 

`-c CLUSTER, --cluster CLUSTER`  
Specifies the cluster to use\.

`-cf, --command-file`  
Indicates that the command is a file to be transferred to the compute instances\.  
Default: False

`-w WORKING_DIR, --working-dir WORKING_DIR`  
Specifies the folder to use as the job's working directory\. If a working directory isn't specified, the job is run in the `job-<AWS_BATCH_JOB_ID>` subfolder of the user’s home directory\. You can use either this parameter or the `--parent-working-dir` parameter\.

`-pw PARENT_WORKING_DIR, --parent-working-dir PARENT_WORKING_DIR`  
Specifies the parent folder of the job's working directory\. If a parent working directory isn't specified, it defaults to the user’s home directory\. A subfolder named `job-<AWS_BATCH_JOB_ID>` is created in the parent working directory\. You can use either this parameter or the `--working-dir` parameter\.

`-if INPUT_FILE, --input-file INPUT_FILE`  
Specifies the file to be transferred to the compute instances, in the job's working directory\. You can specify multiple input file parameters\.

`-p VCPUS, --vcpus VCPUS`  
Specifies the number of vCPUs to reserve for the container\. When used together with `–nodes`, it identifies the number of vCPUs for each node\.  
Default: 1

`-m MEMORY, --memory MEMORY`  
Specifies the hard limit of memory \(in MiB\) to provide for the job\. If your job attempts to exceed the memory limit specified here, the job is ended\.  
Default: 128

`-e ENV, --env ENV`  
Specifies a comma\-separated list of environment variable names to export to the job environment\. To export all environment variables, specify ‘all’\. Note that a list of 'all' environment variables doesn't include those listed in the `–env-blacklist` parameter, or variables starting with the `PCLUSTER_*` or `AWS_*` prefix\.

`-eb ENV_DENYLIST, --env-blacklist ENV_DENYLIST`  
Specifies a comma\-separated list of environment variable names to **not** export to the job environment\. By default, `HOME`, `PWD`, `USER`, `PATH`, `LD_LIBRARY_PATH`, `TERM`, and `TERMCAP` are not exported\.

`-r RETRY_ATTEMPTS, --retry-attempts RETRY_ATTEMPTS`  
Specifies the number of times to move a job to the `RUNNABLE` status\. You can specify between 1 and 10 attempts\. If the value of attempts is greater than 1, the job is retried if it fails, until it has moved to a `RUNNABLE` status for the specified number of times\.  
Default: 1

`-t TIMEOUT, --timeout TIMEOUT`  
Specifies the time duration in seconds \(measured from the job attempt’s `startedAt` timestamp\) after which AWS Batch terminates your job if it hasn't finished\. The timeout value must be at least 60 seconds\.

`-n NODES, --nodes NODES`  
Specifies the number of nodes to reserve for the job\. Specify a value for this parameter to enable multi\-node parallel submission\.  
When the `Scheduler` / `AwsBatchQueues` / `CapacityType` parameter is set to `SPOT`, multi\-node parallel jobs *aren't* supported\. Additionally, there must be an `AWSServiceRoleForEC2Spot` service\-linked role in your account\. You can create this role with the following AWS CLI command:  

```
$ aws iam create-service-linked-role --aws-service-name spot.amazonaws.com
```
For more information, see [Service\-linked role for Spot Instance requests](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-requests.html#service-linked-roles-spot-instance-requests) in the *Amazon EC2 User Guide for Linux Instances*\.

`-a ARRAY_SIZE, --array-size ARRAY_SIZE`  
Indicates the size of the array\. You can specify a value between 2 and 10,000\. If you specify array properties for a job, it becomes an array job\.

`-d DEPENDS_ON, --depends-on DEPENDS_ON`  
Specifies a semicolon\-separated list of dependencies for a job\. A job can depend upon a maximum of 20 jobs\. You can specify a `SEQUENTIAL` type dependency without specifying a job ID for array jobs\. A sequential dependency allows each child array job to complete sequentially, starting at index 0\. You can also specify an N\_TO\_N type dependency with a job ID for array jobs\. An N\_TO\_N dependency means that each index child of this job must wait for the corresponding index child of each dependency to complete before it can begin\. The syntax for this parameter is "jobId=*<string>*,type=*<string>*;\.\.\."\.