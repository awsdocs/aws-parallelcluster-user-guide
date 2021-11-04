# Multiple queue mode tutorial<a name="tutorial-mqm"></a>



## Running your jobs on AWS ParallelCluster with multiple queue mode<a name="tutorial-mqm-running-jobs"></a>

This tutorial walks you through running your first Hello World job on AWS ParallelCluster with [Multiple queue mode](queue-mode.md)\. If you haven't already installed AWS ParallelCluster and configured your CLI, follow the instructions in the [Setting up AWS ParallelCluster](getting_started.md) guide before continuing with this tutorial\.

**Note**  
Multiple queue mode is only supported for AWS ParallelCluster version 2\.9\.0 or later\.

### Configuring your cluster<a name="tutorial-mqm-configure-cluster"></a>

First, verify that AWS ParallelCluster is correctly installed by running the following command\.

```
$ pcluster version
```

For more information about `pcluster version`, see [`pcluster version`](pcluster.version.md)\.

This command returns the running version of AWS ParallelCluster\.

Next, run `pcluster configure` to generate a basic configuration file\. Follow all the prompts that follow this command\.

```
$ pcluster configure
```

For more information about the `pcluster configure` command, see [`pcluster configure`](pcluster.configure.md)\.

After you complete this step, you should have a basic configuration file under `~/.parallelcluster/config`\. This file should contain a basic cluster configurations and a VPC section\.

This next part of the tutorial outlines how to modify your newly created configuration and launch a cluster with multiple queues\.

**Note**  
Some instances used in this tutorial aren't free\-tier eligible\.

For this tutorial, use the following configuration\.

```
[global]
update_check = true
sanity_check = true
cluster_template = multi-queue

[aws]
aws_region_name = <Your AWS Region>

[scaling demo]
scaledown_idletime = 5              # optional, defaults to 10 minutes

[cluster multi-queue-special]
key_name = < Your key name >
base_os = alinux2                   # optional, defaults to alinux2
scheduler = slurm
master_instance_type = c5.xlarge    # optional, defaults to t2.micro
vpc_settings = <Your VPC section>
scaling_settings = demo             # optional, defaults to no custom scaling settings
queue_settings = efa,gpu

[cluster multi-queue]
key_name = <Your SSH key name>
base_os = alinux2                   # optional, defaults to alinux2
scheduler = slurm
master_instance_type = c5.xlarge    # optional, defaults to t2.micro
vpc_settings = <Your VPC section>
scaling_settings = demo
queue_settings = spot,ondemand

[queue spot]
compute_resource_settings = spot_i1,spot_i2
compute_type = spot                 # optional, defaults to ondemand

[compute_resource spot_i1]
instance_type = c5.xlarge
min_count = 0                       # optional, defaults to 0
max_count = 10                      # optional, defaults to 10

[compute_resource spot_i2]
instance_type = t2.micro
min_count = 1
initial_count = 2

[queue ondemand]
compute_resource_settings = ondemand_i1
disable_hyperthreading = true       # optional, defaults to false

[compute_resource ondemand_i1]
instance_type = c5.2xlarge
```

### Creating your cluster<a name="tutorial-mqm-creating-cluster"></a>

This section details how to create the multiple queue mode cluster\.

First, name your cluster `multi-queue-hello-world`, and create the cluster according to the `multi-queue` cluster section defined in the previous section\.

```
$ pcluster create multi-queue-hello-world -t multi-queue
```

For more information about `pcluster create`, see [`pcluster create`](pluster.create.md)\.

When the cluster is created, the following output is displayed:

```
Beginning cluster creation for cluster: multi-queue-hello-world
Creating stack named: parallelcluster-multi-queue-hello-world
Status: parallelcluster-multi-queue-hello-world - CREATE_COMPLETE
MasterPublicIP: 3.130.xxx.xx
ClusterUser: ec2-user
MasterPrivateIP: 172.31.xx.xx
```

The message `CREATE_COMPLETE` indicates that the cluster was successfully created\. The output also provides the public and private IP addresses of the head node\.

### Logging into your head node<a name="tutorial-mqm-log-into-head-node"></a>

Use your private SSH key file to log into your head node\.

```
$ pcluster ssh multi-queue-hello-world -i ~/path/to/keyfile.pem
```

For more information about `pcluster ssh`, see [`pcluster ssh`](pcluster.ssh.md)\.

After logging in, run the `sinfo` command to verify that your scheduler queues are set up and configured\.

For more information about `sinfo`, see [sinfo](https://slurm.schedmd.com/sinfo.html) in the *Slurm documentation*\.

```
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
ondemand     up   infinite     10  idle~ ondemand-dy-c52xlarge-[1-10]
spot*        up   infinite     18  idle~ spot-dy-c5xlarge-[1-10],spot-dy-t2micro-[2-9]
spot*        up   infinite      2   idle spot-dy-t2micro-1,spot-st-t2micro-1
```

The output shows that you have two `t2.micro` compute nodes in the `idle` state that are available in your cluster\.

**Note**  
`spot-st-t2micro-1` is a static node with `st` in its name\. This node is always available and corresponds to the ``min_count` = 1` in your cluster configuration\.
`spot-dy-t2micro-1` is a dynamic node with `dy` in its name\. This node is currently available because it corresponds to ``initial_count` - `min_count` = 1` according to your cluster configuration\. This node scales down after your custom [`scaledown_idletime`](scaling-section.md#scaledown-idletime) of five minutes\.

Other nodes are all in power saving state, shown by the `~` suffix in node state, with no EC2 instances backing them\. The default queue is designated by a `*` suffix after its queue name, so `spot` is your default job queue\.

### Running job in multiple queue mode<a name="tutorial-mqm-running-job-mqm"></a>

Next, try to run a job to sleep for a while\. The job will later output its own hostname\. Make sure this script can be run by the current user\.

```
$ cat hellojob.sh
#!/bin/bash
sleep 30
echo "Hello World from $(hostname)"

$ chmod +x hellojob.sh
$ ls -l hellojob.sh
-rwxrwxr-x 1 ec2-user ec2-user 57 Sep 23 21:57 hellojob.sh
```

Submit the job using the `sbatch` command\. Request two nodes for this job with the `-N 2` option, and verify that the job submits successfully\. For more information about `sbatch`, see [https://slurm.schedmd.com/sbatch.html](https://slurm.schedmd.com/sbatch.html) in the *Slurm documentation*\.

```
$ sbatch -N 2 --wrap "srun hellojob.sh"
Submitted batch job 2
```

You can view your queue and check the status of the job with the `squeue` command\. Note that, because you didn't specify a specific queue, the default queue \(`spot`\) is used\. For more information about `squeue`, see [https://slurm.schedmd.com/squeue.html](https://slurm.schedmd.com/squeue.html) in the *Slurmdocumentation*\.

```
$ squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
                 2      spot     wrap ec2-user  R       0:10      2 spot-dy-t2micro-1,spot-st-t2micro-1
```

The output shows that the job is currently in a running state\. Wait 30 seconds for the job to finish, and then run `squeue` again\.

```
$ squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
```

Now that the jobs in the queue have all finished, look for the output file `slurm-2.out` in your current directory\.

```
$ cat slurm-2.out
Hello World from spot-dy-t2micro-1
Hello World from spot-st-t2micro-1
```

The output also shows that our job ran successfully on the `spot-st-t2micro-1` and `spot-st-t2micro-2` nodes\.

Now submit the same job by specifying constraints for specific instances with the following commands\.

```
$ sbatch -N 3 -p spot -C "[c5.xlarge*1&t2.micro*2]" --wrap "srun hellojob.sh"
Submitted batch job 3
```

You used these parameters for `sbatch`\.
+ `-N 3`– requests three nodes
+ `-p spot`– submits the job to the `spot` queue\. You can also submit a job to the `ondemand` queue by specifying `-p ondemand`\.
+ `-C "[c5.xlarge*1&t2.micro*2]"`– specifies the specific node constraints for this job\. This requests one \(1\) `c5.xlarge` node and two \(2\) `t2.micro` nodes to be used for this job\.

Run the `sinfo` command to view the nodes and queues\. \(Queues in AWS ParallelCluster are called partitions in Slurm\.\)

```
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
ondemand     up   infinite     10  idle~ ondemand-dy-c52xlarge-[1-10]
spot*        up   infinite      1   mix# spot-dy-c5xlarge-1
spot*        up   infinite     17  idle~ spot-dy-c5xlarge-[2-10],spot-dy-t2micro-[2-9]
spot*        up   infinite      2  alloc spot-dy-t2micro-1,spot-st-t2micro-1
```

The nodes are powering up\. This is signified by the `#` suffix on the node state\. Run the squeue command to view information about the jobs in the cluster\.

```
$ squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
                 3      spot     wrap ec2-user CF       0:04      3 spot-dy-c5xlarge-1,spot-dy-t2micro-1,spot-st-t2micro-1
```

Your job is in the `CF` \(CONFIGURING\) state, waiting for instances to scale up and join the cluster\.

After about three minutes, the nodes should be available and the job enters the `R` \(RUNNING\) state\.

```
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
ondemand     up   infinite     10  idle~ ondemand-dy-c52xlarge-[1-10]
spot*        up   infinite     17  idle~ spot-dy-c5xlarge-[2-10],spot-dy-t2micro-[2-9]
spot*        up   infinite      1    mix spot-dy-c5xlarge-1
spot*        up   infinite      2  alloc spot-dy-t2micro-1,spot-st-t2micro-1
$ squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
                 3      spot     wrap ec2-user  R       0:04      3 spot-dy-c5xlarge-1,spot-dy-t2micro-1,spot-st-t2micro-1
```

The job finishes, and all three nodes are in the `idle` state\.

```
$ squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
ondemand     up   infinite     10  idle~ ondemand-dy-c52xlarge-[1-10]
spot*        up   infinite     17  idle~ spot-dy-c5xlarge-[2-10],spot-dy-t2micro-[2-9]
spot*        up   infinite      3   idle spot-dy-c5xlarge-1,spot-dy-t2micro-1,spot-st-t2micro-1
```

Then, after there are no jobs remaining in the queue, you can check for `slurm-3.out` in your local directory\.

```
$ cat slurm-3.out 
Hello World from spot-dy-c5xlarge-1
Hello World from spot-st-t2micro-1
Hello World from spot-dy-t2micro-1
```

The output also shows that the job ran successfully on the corresponding nodes\.

You can observe the scale down process\. In your cluster configuration that you specified a custom [`scaledown_idletime`](scaling-section.md#scaledown-idletime) of 5 minutes\. After five minutes in an idle state, your dynamic nodes `spot-dy-c5xlarge-1` and `spot-dy-t2micro-1` automatically scale down and enter into the `POWER_DOWN` mode\. Note that static node `spot-st-t2micro-1` doesn't scale down\.

```
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
ondemand     up   infinite     10  idle~ ondemand-dy-c52xlarge-[1-10]
spot*        up   infinite      2  idle% spot-dy-c5xlarge-1,spot-dy-t2micro-1
spot*        up   infinite     17  idle~ spot-dy-c5xlarge-[2-10],spot-dy-t2micro-[2-9]
spot*        up   infinite      1   idle spot-st-t2micro-1
```

From the above code, you can see that `spot-dy-c5xlarge-1` and `spot-dy-t2micro-1` are in `POWER_DOWN` mode\. This is indicated by the `%` suffix\. The corresponding instances are immediately terminated, but nodes remain in the `POWER_DOWN` state and aren't available for use for 120 seconds \(two minutes\)\. After this time, the nodes return in power saving and are available for use again\. For more information, see [Slurm guide for multiple queue mode](multiple-queue-mode-slurm-user-guide.md)\.

This should be the final state of the cluster:

```
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
ondemand     up   infinite     10  idle~ ondemand-dy-c52xlarge-[1-10]
spot*        up   infinite     19  idle~ spot-dy-c5xlarge-[1-10],spot-dy-t2micro-[1-9]
spot*        up   infinite      1   idle spot-st-t2micro-1
```

After logging off of the cluster, you can clean up by running `pcluster delete`\. For more information, about `pcluster list` and `pcluster delete`, see [`pcluster list`](pcluster.list.md) and [`pcluster delete`](pcluster.delete.md)\.

```
$ pcluster list
multi-queue CREATE_COMPLETE 2.11.3
$ pcluster delete multi-queue
Deleting: multi-queue
...
```

### Running jobs on cluster with EFA and GPU instances<a name="tutorial-mqm-running-job-efa-gpu"></a>

This part of the tutorial details how to modify the configuration and launch a cluster with multiple queues that contains instances with EFA networking and GPU resources\. Note that instances used in this tutorial are higher priced instances\.

**Check your account limits to make sure that you're authorized to use these instances before proceeding with the steps outlined in this tutorial\.**

Modify the configuration file by using the following\.

```
[global]
update_check = true
sanity_check = true
cluster_template = multi-queue-special

[aws]
aws_region_name = <Your AWS Region>

[scaling demo]
scaledown_idletime = 5

[cluster multi-queue-special]
key_name = <Your SSH key name>
base_os = alinux2                   # optional, defaults to alinux2
scheduler = slurm
master_instance_type = c5.xlarge    # optional, defaults to t2.micro
vpc_settings = <Your VPC section>
scaling_settings = demo
queue_settings = efa,gpu

[queue gpu]
compute_resource_settings = gpu_i1
disable_hyperthreading = true       # optional, defaults to false

[compute_resource gpu_i1]
instance_type = g3.8xlarge

[queue efa]
compute_resource_settings = efa_i1
enable_efa = true
placement_group = DYNAMIC           # optional, defaults to no placement group settings

[compute_resource efa_i1]
instance_type = c5n.18xlarge
max_count = 5
```

Create the cluster

```
$ pcluster create multi-queue-special -t multi-queue-special
```

After the cluster is created, use your private SSH key file to log into your head node\.

```
$ pcluster ssh multi-queue-special -i ~/path/to/keyfile.pem
```

This should be the initial state of the cluster:

```
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
efa*         up   infinite      5  idle~ efa-dy-c5n18xlarge-[1-5]
gpu          up   infinite     10  idle~ gpu-dy-g38xlarge-[1-10]
```

This section describes how to submit some jobs to check that nodes have EFA or GPU resources\.

First, write the job scripts\. `efa_job.sh` will sleep for 30 seconds\. After which, look for EFA in the output of the `lspci` command\. `gpu_job.sh` will sleep for 30 seconds\. After which, run `nvidia-smi` to show the GPU information about the node\.

```
$ cat efa_job.sh
#!/bin/bash

sleep 30
lspci | grep "EFA"

$ cat gpu_job.sh
#!/bin/bash

sleep 30
nvidia-smi

$ chmod +x efa_job.sh
$ chmod +x gpu_job.sh
```

Submit the job with `sbatch`,

```
$ sbatch -p efa --wrap "srun efa_job.sh"
Submitted batch job 2
$ sbatch -p gpu --wrap "srun gpu_job.sh" -G 1
Submitted batch job 3 
$ squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
                 2       efa     wrap ec2-user CF       0:32      1 efa-dy-c5n18xlarge-1
                 3       gpu     wrap ec2-user CF       0:20      1 gpu-dy-g38xlarge-1
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
efa*         up   infinite      1   mix# efa-dy-c5n18xlarge-1
efa*         up   infinite      4  idle~ efa-dy-c5n18xlarge-[2-5]
gpu          up   infinite      1   mix# gpu-dy-g38xlarge-1
gpu          up   infinite      9  idle~ gpu-dy-g38xlarge-[2-10]
```

After a few minutes, you should see the nodes online and jobs running\.

```
[ec2-user@ip-172-31-15-251 ~]$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
efa*         up   infinite      4  idle~ efa-dy-c5n18xlarge-[2-5]
efa*         up   infinite      1    mix efa-dy-c5n18xlarge-1
gpu          up   infinite      9  idle~ gpu-dy-g38xlarge-[2-10]
gpu          up   infinite      1    mix gpu-dy-g38xlarge-1
[ec2-user@ip-172-31-15-251 ~]$ squeue
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
                 4       gpu     wrap ec2-user  R       0:06      1 gpu-dy-g38xlarge-1
                 5       efa     wrap ec2-user  R       0:01      1 efa-dy-c5n18xlarge-1
```

After the job completes, check the output\. From the output in the `slurm-2.out` file, you can see that EFA is present on the `efa-dy-c5n18xlarge-1` node\. \. From the output in the `slurm-3.out` file, you can see the `nvidia-smi` output contains GPU information for the `gpu-dy-g38xlarge-1` node\.

```
$ cat slurm-2.out
00:06.0 Ethernet controller: Amazon.com, Inc. Elastic Fabric Adapter (EFA)

$ cat slurm-3.out
Thu Oct  1 22:19:18 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 450.51.05    Driver Version: 450.51.05    CUDA Version: 11.0     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  Tesla M60           Off  | 00000000:00:1D.0 Off |                    0 |
| N/A   28C    P0    38W / 150W |      0MiB /  7618MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
|   1  Tesla M60           Off  | 00000000:00:1E.0 Off |                    0 |
| N/A   36C    P0    37W / 150W |      0MiB /  7618MiB |     98%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```

You can observe the scale down process\. In the cluster configuration, you previously specified a custom [`scaledown_idletime`](scaling-section.md#scaledown-idletime) of five minutes\. As a result, after five minutes in an idle state, your dynamic nodes, `spot-dy-c5xlarge-1` and `spot-dy-t2micro-1`, automatically scale down and enter into the `POWER_DOWN` mode\. Eventually, the nodes enter the power saving mode and are available for use again\.

```
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
efa*         up   infinite      1  idle% efa-dy-c5n18xlarge-1
efa*         up   infinite      4  idle~ efa-dy-c5n18xlarge-[2-5]
gpu          up   infinite      1  idle% gpu-dy-g38xlarge-1
gpu          up   infinite      9  idle~ gpu-dy-g38xlarge-[2-10]  

# After 120 seconds
$ sinfo
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
efa*         up   infinite      5  idle~ efa-dy-c5n18xlarge-[1-5]
gpu          up   infinite     10  idle~ gpu-dy-g38xlarge-[1-10]
```

After logging off of the cluster, you can clean up by running ``pcluster delete` <cluster name>`\.

```
$ pcluster list
multi-queue-special CREATE_COMPLETE 2.11.3
$ pcluster delete multi-queue-special
Deleting: multi-queue-special
...
```

 For more information, see [Slurm guide for multiple queue mode](multiple-queue-mode-slurm-user-guide.md)\.