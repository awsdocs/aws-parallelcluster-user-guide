# Running jobs in a multiple queue mode cluster<a name="multi-queue-tutorial-v3"></a>

This tutorial walks you through running your first Hello World job on AWS ParallelCluster with [multiple queue mode](configuration-of-multiple-queues-v3.md)\.

**Prerequisites**
+ AWS ParallelCluster [is installed](install-v3-parallelcluster.md)\.
+ The AWS CLI [is installed and configured\.](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
+ You have an [EC2 key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)\.
+ You have an IAM role with the [permissions](iam-roles-in-parallelcluster-v3.md#iam-roles-in-parallelcluster-v3-example-user-policies) required to run the [`pcluster`](pcluster-v3.md) CLI\.

## Configure your cluster<a name="multi-queue-tutorial-v3-configure-cluster"></a>

First, verify that AWS ParallelCluster is correctly installed by running the following command\.

```
$ pcluster version
```

For more information about `pcluster version`, see [`pcluster version`](pcluster.version-v3.md)\.

This command returns the running version of AWS ParallelCluster\.

Next, run `pcluster configure` to generate a basic configuration file\. Follow all the prompts that follow this command\.

```
$ pcluster configure --config multi-queue-mode.yaml
```

For more information about the `pcluster configure` command, see [`pcluster configure`](pcluster.configure-v3.md)\.

After you complete this step, you should have a basic configuration file named `multi-queue-mode.yaml`\. This file should contain a basic cluster configuration\.

In the next step you modify your new configuration file and launch a cluster with multiple queues\.

**Note**  
Some instances used in this tutorial aren't free\-tier eligible\.

For this tutorial, modify your configuration file to match the following configuration\. The items highlighted in red represent your configuration file values\. Keep your own values\.

```
Region: region-id
Image:
 Os: alinux2
HeadNode:
 InstanceType: c5.xlarge
 Networking:
   SubnetId: subnet-abcdef01234567890
 Ssh:
   KeyName: yourkeypair
Scheduling:
 Scheduler: slurm
 SlurmQueues:
 - Name: spot
   ComputeResources:
   - Name: c5xlarge
     InstanceType: c5.xlarge
     MinCount: 1
     MaxCount: 10
   - Name: t2micro
     InstanceType: t2.micro
     MinCount: 1
     MaxCount: 10
   Networking:
     SubnetIds:
     - subnet-abcdef01234567890
 - Name: ondemand
   ComputeResources:
   - Name: c52xlarge
     InstanceType: c5.2xlarge
     MinCount: 0
     MaxCount: 10
   Networking:
     SubnetIds:
     - subnet-021345abcdef6789
```

## Create your cluster<a name="multi-queue-tutorial-v3-create-cluster"></a>

Create a cluster named `multi-queue-cluster` based on your configuration file:

```
$ pcluster create-cluster --cluster-name multi-queue-cluster --cluster-configuration multi-queue-mode.yaml
{
 "cluster": {
   "clusterName": "multi-queue-cluster",
   "cloudformationStackStatus": "CREATE_IN_PROGRESS",
   "cloudformationStackArn": "arn:aws:cloudformation:eu-west-1:123456789012:stack/multi-queue-cluster/1234567-abcd-0123-def0-abcdef0123456",
   "region": "eu-west-1",
   "version": "3.2.0",
   "clusterStatus": "CREATE_IN_PROGRESS"
 }
}
```

For more information, about the `pcluster create-cluster` command, see [`pcluster create-cluster`](pcluster.create-cluster-v3.md)\.

To check the status of the cluster, run the following command:

```
$ pcluster list-clusters
{
 "cluster": {
   "clusterName": "multi-queue-cluster",
   "cloudformationStackStatus": "CREATE_IN_PROGRESS",
   "cloudformationStackArn": "arn:aws:cloudformation:eu-west-1:123456789012:stack/multi-queue-cluster/1234567-abcd-0123-def0-abcdef0123456",
   "region": "eu-west-1",
   "version": "3.2.0",
   "clusterStatus": "CREATE_IN_PROGRESS"
 }
}
```

When the cluster is created, the `clusterStatus` field will show `CREATE_COMPLETE`\.

## Log into the head node<a name="multi-queue-tutorial-v3-log-into-head-node"></a>

Use your private SSH key file to log into the head node\.

```
$ pcluster ssh --cluster-name multi-queue-cluster -i ~/path/to/yourkeyfile.pem
```

For more information about `pcluster ssh`, see [`pcluster ssh`](pcluster.ssh-v3.md)\.

After logging in, run the `sinfo` command to verify that your scheduler queues are set up and configured\.

For more information about `sinfo`, see [sinfo](https://slurm.schedmd.com/sinfo.html) in the *Slurm documentation*\.

```
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
spot*        up   infinite     18  idle~ spot-dy-c5xlarge-[1-9],spot-dy-t2micro-[1-9]
spot*        up   infinite      2  idle  spot-st-c5xlarge-1,spot-st-t2micro-1
ondemand     up   infinite     10  idle~ ondemand-dy-c52xlarge-[1-10]
```

The output shows that you have one `t2.micro` and one `c5.xlarge` compute node in the `idle` state that are available in your cluster\.

Other nodes are all in power saving state, shown by the `~` suffix in node state, with no EC2 instances backing them\. The default queue is designated by a `*` suffix after its queue name, so `spot` is your default job queue\.

## Run job in multiple queue mode<a name="multi-queue-tutorial-v3-running-job-mqm"></a>

Next, try to run a job to sleep for a while\. The job will later output its own hostname\. Make sure this script can be run by the current user\.

```
$ tee <<EOF hellojob.sh
#!/bin/bash
sleep 30
echo "Hello World from \$(hostname)"
EOF

$ chmod +x hellojob.sh
$ ls -l hellojob.sh
-rwxrwxr-x 1 ec2-user ec2-user 57 Sep 23 21:57 hellojob.sh
```

Submit the job using the `sbatch` command\. Request two nodes for this job with the `-N 2` option, and verify that the job submits successfully\. For more information about `sbatch`, see [https://slurm.schedmd.com/sbatch.html](https://slurm.schedmd.com/sbatch.html) in the *Slurm documentation*\.

```
$ sbatch -N 2 --wrap "srun hellojob.sh"
Submitted batch job 1
```

You can view your queue and check the status of the job with the `squeue` command\. Note that, because you didn't specify a specific queue, the default queue \(`spot`\) is used\. For more information about `squeue`, see [https://slurm.schedmd.com/squeue.html](https://slurm.schedmd.com/squeue.html) in the *Slurm documentation*\.

```
$ squeue
JOBID PARTITION     NAME     USER  ST      TIME  NODES NODELIST(REASON)
   1      spot     wrap ec2-user  R       0:10      2 spot-st-c5xlarge-1,spot-st-t2micro-1
```

The output shows that the job is currently in a running state\. Wait 30 seconds for the job to finish, and then run `squeue` again\.

```
$ squeue
JOBID PARTITION     NAME     USER          ST       TIME  NODES NODELIST(REASON)
```

Now that the jobs in the queue have all finished, look for the output file `slurm-1.out` in your current directory\.

```
$ cat slurm-1.out
Hello World from spot-st-t2micro-1
Hello World from spot-st-c5xlarge-1
```

The output also shows that our job ran successfully on the `spot-st-t2micro-1` and `spot-st-c5xlarge-1` nodes\.

Now submit the same job by specifying constraints for specific instances with the following commands\.

```
$ sbatch -N 3 -p spot -C "[c5.xlarge*1&t2.micro*2]" --wrap "srun hellojob.sh"
Submitted batch job 2
```

You used these parameters for `sbatch`\.
+ `-N 3`– requests three nodes
+ `-p spot`– submits the job to the `spot` queue\. You can also submit a job to the `ondemand` queue by specifying `-p ondemand`\.
+ `-C "[c5.xlarge*1&t2.micro*2]"`– specifies the specific node constraints for this job\. This requests one \(1\) `c5.xlarge` node and two \(2\) `t2.micro` nodes to be used for this job\.

Run the `sinfo` command to view the nodes and queues\. Queues in AWS ParallelCluster are called partitions in Slurm\.

```
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
spot*        up   infinite      1  alloc# spot-dy-t2micro-1
spot*        up   infinite     17  idle~ spot-dy-c5xlarge-[2-10],spot-dy-t2micro-[2-9]
spot*        up   infinite      1  mix   spot-st-c5xlarge-1
spot*        up   infinite      1  alloc spot-st-t2micro-1
ondemand     up   infinite     10  idle~ ondemand-dy-c52xlarge-[1-10]
```

The nodes are powering up\. This is signified by the `#` suffix on the node state\. Run the squeue command to view information about the jobs in the cluster\.

```
$ squeue
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
   2      spot     wrap ec2-user CF       0:04      3 spot-dy-c5xlarge-1,spot-dy-t2micro-1,spot-st-t2micro-1
```

Your job is in the `CF` \(CONFIGURING\) state, waiting for instances to scale up and join the cluster\.

After about three minutes, the nodes should be available and the job enters the `R` \(RUNNING\) state\.

```
$ squeue
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
   2      spot     wrap ec2-user  R       0:07      3 spot-dy-t2micro-1,spot-st-c5xlarge-1,spot-st-t2micro-1
```

The job finishes, and all three nodes are in the `idle` state\.

```
$ squeue
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
spot*        up   infinite     17  idle~ spot-dy-c5xlarge-[1-9],spot-dy-t2micro-[2-9]
spot*        up   infinite      3  idle  spot-dy-t2micro-1,spot-st-c5xlarge-1,spot-st-t2micro-1
ondemand     up   infinite     10  idle~ ondemand-dy-c52xlarge-[1-10]
```

Then, after there are no jobs remaining in the queue, you can check for `slurm-2.out` in your local directory\.

```
$ cat slurm-2.out 
Hello World from spot-st-t2micro-1
Hello World from spot-dy-t2micro-1
Hello World from spot-st-c5xlarge-1
```

This should be the final state of the cluster\.

```
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
spot*        up   infinite     17  idle~ spot-dy-c5xlarge-[1-9],spot-dy-t2micro-[2-9]
spot*        up   infinite      3  idle  spot-dy-t2micro-1,spot-st-c5xlarge-1,spot-st-t2micro-1
ondemand     up   infinite     10  idle~ ondemand-dy-c52xlarge-[1-10]
```

After logging off of the cluster, you can clean up by running `pcluster delete-cluster`\. For more information, about `pcluster list-clusters` and `pcluster delete-cluster`, see [`pcluster list-clusters`](pcluster.list-clusters-v3.md) and [`pcluster delete-cluster`](pcluster.delete-cluster-v3.md)\.

```
$ pcluster list-clusters
{
 "clusters": [
   {
     "clusterName": "multi-queue-cluster",
     "cloudformationStackStatus": "CREATE_COMPLETE",
     "cloudformationStackArn": "arn:aws:cloudformation:eu-west-1:123456789012:stack/multi-queue-cluster/1234567-abcd-0123-def0-abcdef0123456",
     "region": "eu-west-1",
     "version": "3.1.4",
     "clusterStatus": "CREATE_COMPLETE"
   }
 ]
}
$ pcluster delete-cluster -n multi-queue-cluster
{
 "cluster": {
   "clusterName": "multi-queue-cluster",
   "cloudformationStackStatus": "DELETE_IN_PROGRESS",
   "cloudformationStackArn": "arn:aws:cloudformation:eu-west-1:123456789012:stack/multi-queue-cluster/1234567-abcd-0123-def0-abcdef0123456",
   "region": "eu-west-1",
   "version": "3.1.4",
   "clusterStatus": "DELETE_IN_PROGRESS"
 }
}
```