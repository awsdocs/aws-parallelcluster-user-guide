# Running an MPI job with AWS ParallelCluster and `awsbatch` scheduler<a name="tutorials_03_batch_mpi"></a>

This tutorial walks you through running an MPI job with `awsbatch` as a scheduler\.

If you haven't yet installed AWS ParallelCluster and configured your CLI, follow the instructions in the [getting started](getting_started.md) guide before continuing with this tutorial\. Also, make sure to read through the [awsbatch networking setup](networking.md#awsbatch-networking) documentation before moving to the next step\.

## Creating the cluster<a name="creating-the-cluster"></a>

First, let's create a configuration for a cluster that uses `awsbatch` as the scheduler\. Make sure to insert the missing data in the `vpc` section and the `key_name` field with the resources that you created at configuration time\.

```
[global]
sanity_check = true

[aws]
aws_region_name = us-east-1

[cluster awsbatch]
base_os = alinux
# Replace with the name of the key you intend to use.
key_name = key-#######
vpc_settings = my-vpc
scheduler = awsbatch
compute_instance_type = optimal
min_vcpus = 2
desired_vcpus = 2
max_vcpus = 24

[vpc my-vpc]
# Replace with the id of the vpc you intend to use.
vpc_id = vpc-#######
# Replace with id of the subnet for the Master node.
master_subnet_id = subnet-#######
# Replace with id of the subnet for the Compute nodes.
# A NAT Gateway is required for MNP.
compute_subnet_id = subnet-#######
```

You can now start the creation of the cluster\. Let's call our cluster *awsbatch\-tutorial*\.

```
$ pcluster create -c /path/to/the/created/config/aws_batch.config -t awsbatch awsbatch-tutorial
```

When the cluster is created, you see output similar to the following:

```
Beginning cluster creation for cluster: awsbatch-tutorial
Creating stack named: parallelcluster-awsbatch
Status: parallelcluster-awsbatch - CREATE_COMPLETE
MasterPublicIP: 54.160.xxx.xxx
ClusterUser: ec2-user
MasterPrivateIP: 10.0.0.15
```

## Logging into your head node<a name="logging-into-your-head-node"></a>

The [AWS ParallelCluster Batch CLI](awsbatchcli.md) commands are all available on the client machine where AWS ParallelCluster is installed\. However, we are going to SSH into the head node and submit the jobs from there\. This allows us to take advantage of the NFS volume that is shared between the head and all Docker instances that run AWS Batch jobs\.

Use your SSH pem file to log into your head node\.

```
$ pcluster ssh awsbatch-tutorial -i /path/to/keyfile.pem
```

When you are logged in, run the commands `awsbqueues` and `awsbhosts` to show the configured AWS Batch queue and the running Amazon ECS instances\.

```
[ec2-user@ip-10-0-0-111 ~]$ awsbqueues
jobQueueName                       status
---------------------------------  --------
parallelcluster-awsbatch-tutorial  VALID

[ec2-user@ip-10-0-0-111 ~]$ awsbhosts
ec2InstanceId        instanceType    privateIpAddress    publicIpAddress      runningJobs
-------------------  --------------  ------------------  -----------------  -------------
i-0d6a0c8c560cd5bed  m4.large        10.0.0.235          34.239.174.236                 0
```

As you can see from the output, we have one single running host\. This is due to the value we chose for [`min_vcpus`](cluster-definition.md#min-vcpus) in the configuration\. If you want to display additional details about the AWS Batch queue and hosts, add the `-d` flag to the command\.

## Running your first job using AWS Batch<a name="running-your-first-job-using-aws-batch"></a>

Before moving to MPI, let's create a dummy job that sleeps for a little while and then outputs its own hostname, greeting the name passed as a parameter\.

Create a file called "hellojob\.sh" with the following content\.

```
#!/bin/bash

sleep 30
echo "Hello $1 from $(hostname)"
echo "Hello $1 from $(hostname)" > "/shared/secret_message_for_${1}_by_${AWS_BATCH_JOB_ID}"
```

Next, submit the job using `awsbsub` and verify that it runs\.

```
$ awsbsub -jn hello -cf hellojob.sh Luca
Job 6efe6c7c-4943-4c1a-baf5-edbfeccab5d2 (hello) has been submitted.
```

View your queue, and check the status of the job\.

```
$ awsbstat
jobId                                 jobName      status    startedAt            stoppedAt    exitCode
------------------------------------  -----------  --------  -------------------  -----------  ----------
6efe6c7c-4943-4c1a-baf5-edbfeccab5d2  hello        RUNNING   2018-11-12 09:41:29  -            -
```

The output provides detailed information for the job\.

```
$ awsbstat 6efe6c7c-4943-4c1a-baf5-edbfeccab5d2
jobId                    : 6efe6c7c-4943-4c1a-baf5-edbfeccab5d2
jobName                  : hello
createdAt                : 2018-11-12 09:41:21
startedAt                : 2018-11-12 09:41:29
stoppedAt                : -
status                   : RUNNING
statusReason             : -
jobDefinition            : parallelcluster-myBatch:1
jobQueue                 : parallelcluster-myBatch
command                  : /bin/bash -c 'aws s3 --region us-east-1 cp s3://parallelcluster-mybatch-lui1ftboklhpns95/batch/job-hellojob_sh-1542015680924.sh /tmp/batch/job-hellojob_sh-1542015680924.sh; bash /tmp/batch/job-hellojob_sh-1542015680924.sh Luca'
exitCode                 : -
reason                   : -
vcpus                    : 1
memory[MB]               : 128
nodes                    : 1
logStream                : parallelcluster-myBatch/default/c75dac4a-5aca-4238-a4dd-078037453554
log                      : https://console.aws.amazon.com/cloudwatch/home?region=us-east-1#logEventViewer:group=/aws/batch/job;stream=parallelcluster-myBatch/default/c75dac4a-5aca-4238-a4dd-078037453554
-------------------------
```

Note that the job is currently in a `RUNNING` state\. Wait 30 seconds for the job to finish, and then run `awsbstat` again\.

```
$ awsbstat
jobId                                 jobName      status    startedAt            stoppedAt    exitCode
------------------------------------  -----------  --------  -------------------  -----------  ----------
```

Now you can see that the job is in the `SUCCEEDED` status\.

```
$ awsbstat -s SUCCEEDED
jobId                                 jobName      status     startedAt            stoppedAt              exitCode
------------------------------------  -----------  ---------  -------------------  -------------------  ----------
6efe6c7c-4943-4c1a-baf5-edbfeccab5d2  hello        SUCCEEDED  2018-11-12 09:41:29  2018-11-12 09:42:00           0
```

Because there are no jobs in the queue now, we can check for output through the `awsbout` command\.

```
$ awsbout 6efe6c7c-4943-4c1a-baf5-edbfeccab5d2
2018-11-12 09:41:29: Starting Job 6efe6c7c-4943-4c1a-baf5-edbfeccab5d2
download: s3://parallelcluster-mybatch-lui1ftboklhpns95/batch/job-hellojob_sh-1542015680924.sh to tmp/batch/job-hellojob_sh-1542015680924.sh
2018-11-12 09:42:00: Hello Luca from ip-172-31-4-234
```

We can see that our job successfully ran on instance "ip\-172\-31\-4\-234"\.

If you look into the `/shared` directory, you find a secret message for you\.

To explore all of the available features that are not part of this tutorial, see the [AWS ParallelCluster Batch CLI documentation](awsbatchcli.md)\. When you are ready to continue the tutorial, let's move on and see how to submit an MPI job\.

## Running an MPI job in a multi\-node parallel environment<a name="running-an-mpi-job-in-a-multi-node-parallel-environment"></a>

While still logged into the head node, create a file in the `/shared` directory named `mpi_hello_world.c`\. Add the following MPI program to the file:

```
// Copyright 2011 www.mpitutorial.com
//
// An intro MPI hello world program that uses MPI_Init, MPI_Comm_size,
// MPI_Comm_rank, MPI_Finalize, and MPI_Get_processor_name.
//
#include <mpi.h>
#include <stdio.h>
#include <stddef.h>

int main(int argc, char** argv) {
  // Initialize the MPI environment. The two arguments to MPI Init are not
  // currently used by MPI implementations, but are there in case future
  // implementations might need the arguments.
  MPI_Init(NULL, NULL);

  // Get the number of processes
  int world_size;
  MPI_Comm_size(MPI_COMM_WORLD, &world_size);

  // Get the rank of the process
  int world_rank;
  MPI_Comm_rank(MPI_COMM_WORLD, &world_rank);

  // Get the name of the processor
  char processor_name[MPI_MAX_PROCESSOR_NAME];
  int name_len;
  MPI_Get_processor_name(processor_name, &name_len);

  // Print off a hello world message
  printf("Hello world from processor %s, rank %d out of %d processors\n",
         processor_name, world_rank, world_size);

  // Finalize the MPI environment. No more MPI calls can be made after this
  MPI_Finalize();
}
```

Now save the following code as `submit_mpi.sh`:

```
#!/bin/bash
echo "ip container: $(/sbin/ip -o -4 addr list eth0 | awk '{print $4}' | cut -d/ -f1)"
echo "ip host: $(curl -s "http://169.254.169.254/latest/meta-data/local-ipv4")"

# get shared dir
IFS=',' _shared_dirs=(${PCLUSTER_SHARED_DIRS})
_shared_dir=${_shared_dirs[0]}
_job_dir="${_shared_dir}/${AWS_BATCH_JOB_ID%#*}-${AWS_BATCH_JOB_ATTEMPT}"
_exit_code_file="${_job_dir}/batch-exit-code"

if [[ "${AWS_BATCH_JOB_NODE_INDEX}" -eq  "${AWS_BATCH_JOB_MAIN_NODE_INDEX}" ]]; then
    echo "Hello I'm the main node $(hostname)! I run the mpi job!"

    mkdir -p "${_job_dir}"

    echo "Compiling..."
    /usr/lib64/openmpi/bin/mpicc -o "${_job_dir}/mpi_hello_world" "${_shared_dir}/mpi_hello_world.c"

    echo "Running..."
    /usr/lib64/openmpi/bin/mpirun --mca btl_tcp_if_include eth0 --allow-run-as-root --machinefile "${HOME}/hostfile" "${_job_dir}/mpi_hello_world"

    # Write exit status code
    echo "0" > "${_exit_code_file}"
    # Waiting for compute nodes to terminate
    sleep 30
else
    echo "Hello I'm the compute node $(hostname)! I let the main node orchestrate the mpi execution!"
    # Since mpi orchestration happens on the main node, we need to make sure the containers representing the compute
    # nodes are not terminated. A simple trick is to wait for a file containing the status code to be created.
    # All compute nodes are terminated by Batch if the main node exits abruptly.
    while [ ! -f "${_exit_code_file}" ]; do
        sleep 2
    done
    exit $(cat "${_exit_code_file}")
fi
```

We are now ready to submit our first MPI job and make it run concurrently on three nodes:

```
$ awsbsub -n 3 -cf submit_mpi.sh
```

Now let's monitor the job status, and wait for it to enter the `RUNNING` status:

```
$ watch awsbstat -d
```

When the job enters the `RUNNING` status, we can look at its output\. To show the output of the main node, append `#0` to the job id\. To show the output of the compute nodes, use `#1` and `#2`:

```
[ec2-user@ip-10-0-0-111 ~]$ awsbout -s 5b4d50f8-1060-4ebf-ba2d-1ae868bbd92d#0
2018-11-27 15:50:10: Job id: 5b4d50f8-1060-4ebf-ba2d-1ae868bbd92d#0
2018-11-27 15:50:10: Initializing the environment...
2018-11-27 15:50:10: Starting ssh agents...
2018-11-27 15:50:11: Agent pid 7
2018-11-27 15:50:11: Identity added: /root/.ssh/id_rsa (/root/.ssh/id_rsa)
2018-11-27 15:50:11: Mounting shared file system...
2018-11-27 15:50:11: Generating hostfile...
2018-11-27 15:50:11: Detected 1/3 compute nodes. Waiting for all compute nodes to start.
2018-11-27 15:50:26: Detected 1/3 compute nodes. Waiting for all compute nodes to start.
2018-11-27 15:50:41: Detected 1/3 compute nodes. Waiting for all compute nodes to start.
2018-11-27 15:50:56: Detected 3/3 compute nodes. Waiting for all compute nodes to start.
2018-11-27 15:51:11: Starting the job...
download: s3://parallelcluster-awsbatch-tutorial-iwyl4458saiwgwvg/batch/job-submit_mpi_sh-1543333713772.sh to tmp/batch/job-submit_mpi_sh-1543333713772.sh
2018-11-27 15:51:12: ip container: 10.0.0.180
2018-11-27 15:51:12: ip host: 10.0.0.245
2018-11-27 15:51:12: Compiling...
2018-11-27 15:51:12: Running...
2018-11-27 15:51:12: Hello I'm the main node! I run the mpi job!
2018-11-27 15:51:12: Warning: Permanently added '10.0.0.199' (RSA) to the list of known hosts.
2018-11-27 15:51:12: Warning: Permanently added '10.0.0.147' (RSA) to the list of known hosts.
2018-11-27 15:51:13: Hello world from processor ip-10-0-0-180.ec2.internal, rank 1 out of 6 processors
2018-11-27 15:51:13: Hello world from processor ip-10-0-0-199.ec2.internal, rank 5 out of 6 processors
2018-11-27 15:51:13: Hello world from processor ip-10-0-0-180.ec2.internal, rank 0 out of 6 processors
2018-11-27 15:51:13: Hello world from processor ip-10-0-0-199.ec2.internal, rank 4 out of 6 processors
2018-11-27 15:51:13: Hello world from processor ip-10-0-0-147.ec2.internal, rank 2 out of 6 processors
2018-11-27 15:51:13: Hello world from processor ip-10-0-0-147.ec2.internal, rank 3 out of 6 processors

[ec2-user@ip-10-0-0-111 ~]$ awsbout -s 5b4d50f8-1060-4ebf-ba2d-1ae868bbd92d#1
2018-11-27 15:50:52: Job id: 5b4d50f8-1060-4ebf-ba2d-1ae868bbd92d#1
2018-11-27 15:50:52: Initializing the environment...
2018-11-27 15:50:52: Starting ssh agents...
2018-11-27 15:50:52: Agent pid 7
2018-11-27 15:50:52: Identity added: /root/.ssh/id_rsa (/root/.ssh/id_rsa)
2018-11-27 15:50:52: Mounting shared file system...
2018-11-27 15:50:52: Generating hostfile...
2018-11-27 15:50:52: Starting the job...
download: s3://parallelcluster-awsbatch-tutorial-iwyl4458saiwgwvg/batch/job-submit_mpi_sh-1543333713772.sh to tmp/batch/job-submit_mpi_sh-1543333713772.sh
2018-11-27 15:50:53: ip container: 10.0.0.199
2018-11-27 15:50:53: ip host: 10.0.0.227
2018-11-27 15:50:53: Compiling...
2018-11-27 15:50:53: Running...
2018-11-27 15:50:53: Hello I'm a compute node! I let the main node orchestrate the mpi execution!
```

We can now confirm that the job completed successfully:

```
[ec2-user@ip-10-0-0-111 ~]$ awsbstat -s ALL
jobId                                 jobName        status     startedAt            stoppedAt            exitCode
------------------------------------  -------------  ---------  -------------------  -------------------  ----------
5b4d50f8-1060-4ebf-ba2d-1ae868bbd92d  submit_mpi_sh  SUCCEEDED  2018-11-27 15:50:10  2018-11-27 15:51:26  -
```

Note: if you want to terminate a job before it ends, you can use the `awsbkill` command\.