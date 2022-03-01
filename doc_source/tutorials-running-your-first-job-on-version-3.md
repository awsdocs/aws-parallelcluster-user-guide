# Running your first job on AWS ParallelCluster<a name="tutorials-running-your-first-job-on-version-3"></a>

This tutorial walks you through running your first Hello World job on AWS ParallelCluster\. If you haven't yet completed installation of AWS ParallelCluster, and configured your CLI, follow the instructions at [Setting up AWS ParallelCluster](install-v3.md) before continuing with this tutorial\.

## Verifying your installation<a name="tutorial-1stjob-verify-install"></a>

 First, we verify that AWS ParallelCluster is correctly, including the Node\.js dependency, installed and configured\. 

```
$ node --version
v16.8.0
$ pcluster version
{
  "version": "3.1.1"
}
```

This returns the running version of AWS ParallelCluster\.

## Creating your first cluster<a name="tutorial-1stjob-first-cluster"></a>

Now it's time to create your first cluster\. Because the workload for this tutorial isn't performance intensive, we can use the default instance size of `t2.micro`\. \(For production workloads, you choose an instance size that best fits your needs\.\) Let's call your cluster `hello-world`\.

```
$ pcluster create-cluster \
    --cluster-name hello-world \
    --cluster-configuration hello-world.yaml
```

**Note**  
The AWS Region to use must be specified for most `pcluster` commands\. If it's not specified in the `AWS_DEFAULT_REGION` environment variable, or the `region` setting in the `[default]` section of the `~/.aws/config` file, then the `--region` parameter must be provided on the `pcluster` command line\.

If the output gives you a message about configuration, you need to run the following to configure AWS ParallelCluster: 

```
$ pcluster configure --config hello-world.yaml
```

 If the [`pcluster create-cluster`](pcluster.create-cluster-v3.md) command succeeds, you see output similar to the following: 

```
{
  "cluster": {
    "clusterName": "hello-world",
    "cloudformationStackStatus": "CREATE_IN_PROGRESS",
    "cloudformationStackArn": "arn:aws:cloudformation:xxx:stack/xxx",
    "region": "...",
    "version": "...",
    "clusterStatus": "CREATE_IN_PROGRESS"
  }
}
```

 You monitor the creation of the cluster using: 

```
$ pcluster describe-cluster --cluster-name hello-world
```

 The `clusterStatus` reports "`CREATE_IN_PROGRESS`" while the cluster is being created\. The `clusterStatus` transitions to "`CREATE_COMPLETE`" when the cluster is created successfully\. The output also provides us with the `publicIpAddress` and `privateIpAddress` of our head node\.

## Logging into your head node<a name="tutorial-1stjob-logging-in-head-node"></a>

 Use your OpenSSH pem file to log into your head node\. 

```
$ pcluster ssh --cluster-name hello-world -i /path/to/keyfile.pem
```

 After you log in, run the command `sinfo` to verify that your compute nodes are set up and configured\. 

```
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
queue1*      up   infinite     10  idle~ queue1-dy-queue1t2micro-[1-10]
```

 The output shows that we have one queue in our cluster, with up to ten nodes\. 

## Running your first job using Slurm<a name="tutorial-1stjob-first-slurm-job"></a>

Next, we create a job that sleeps for a little while and then outputs its own hostname\. Create a file called `hellojob.sh`, with the following contents\.

```
#!/bin/bash
sleep 30
echo "Hello World from $(hostname)"
```

 Next, submit the job using `sbatch`, and verify that it runs\. 

```
$ sbatch hellojob.sh
Submitted batch job 2
```

 Now, you can view your queue and check the status of the job\. The provisioning of a new Amazon EC2 instance is started in the background\. You can monitor the status of the cluster instances with the `sinfo` command\.

```
$ squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
                 2    queue1 hellojob ec2-user CF       3:30      1 queue1-dy-queue1t2micro-1
```

 The output shows that the job has been submitted to `queue1`\. Wait 30 seconds for the job to finish, and then run `squeue` again\. 

```
$ squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
```

 Now that there are no jobs in the queue, we can check for output in our current directory\. 

```
$ ls -l
total 8
-rw-rw-r-- 1 ec2-user ec2-user 57 Sep  1 14:25 hellojob.sh
-rw-rw-r-- 1 ec2-user ec2-user 43 Sep  1 14:30 slurm-2.out
```

 In the output, we see a "`out`" file\. We can see output from our job: 

```
$ cat slurm-2.out
Hello World from queue1-dy-queue1t2micro-1
```

The output also shows that our job ran successfully on instance `queue1-dy-queue1t2micro-1`\.

In the cluster you just created, only the home directory is shared among all nodes of the cluster\.

If your application requires shared software, libraries, or data, consider the following options:
+ Build a AWS ParallelCluster enabled custom AMI that includes your software as described in [Building a Custom AWS ParallelCluster AMI](building-custom-ami-v3.md)\.
+ Use the [StorageSettings](SharedStorage-v3.md) option in the AWS ParallelCluster configuration file to specify a shared filesystem and store your installed software in the specified mount location\.
+ Use [Custom Bootstrap Actions](custom-bootstrap-actions-v3.md) to automate the bootstrap procedure of each node of your cluster\.