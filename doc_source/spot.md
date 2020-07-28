# Working with Spot Instances<a name="spot"></a>

AWS ParallelCluster uses Spot Instances if the cluster configuration has set [`cluster_type`](cluster-definition.md#cluster-type) = spot\. The primary feature of Spot Instances is they are available for less than the cost of On\-Demand Instances, but it is possible that they might be interrupted\. The effect of the interruption varies depending on the scheduler used\. It may help to take advantage of *Spot Instance interruption notices*, which provide a two\-minute warning before Amazon EC2 must stop or terminate your Spot Instance\. For more information, see [Spot Instance interruptions](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-interruptions.html) in *Amazon EC2 User Guide for Linux Instances*\. The following sections describe three scenarios in which Spot Instances can be interrupted\.

## Scenario 1: Spot Instance with no running jobs is interrupted<a name="no-jobs"></a>

When this interruption occurs, AWS ParallelCluster tries to replace the instance if the scheduler queue has pending jobs that require additional instances, or if the number of active instances is lower than the [`initial_queue_size`](cluster-definition.md#configuration-initial-queue-size) setting\. If AWS ParallelCluster is unable to provision new instances, then a request for new instances is periodically repeated\.

## Scenario 2: Spot Instance running single node jobs is interrupted<a name="single-node"></a>

The behavior of this interruption depends on the scheduler being used\.

Slurm  
The job is terminated and given a state code of `NODE_FAIL`\. The compute instance is removed from the scheduler queue\.

SGE  
The job is terminated\. If the job has enabled the rerun flag \(using either `qsub -r yes` or `qalter -r yes`\) or the queue has the `rerun` configuration set to `TRUE`, then the job is rescheduled\. The compute instance is removed from the scheduler queue\. This behavior comes from these SGE configuration parameters:  
+ `reschedule_unknown 00:00:30`
+ `ENABLE_FORCED_QDEL_IF_UNKNOWN`
+ `ENABLE_RESCHEDULE_KILL=1`

Torque  
The job is removed from the system and the node is removed from the scheduler\. The job is not rerun\. If multiple jobs are running on the instance when it is interrupted, Torque may time out during node removal\. An error may display in the [`sqswatcher`](processes.md#sqswatcher) log file\. This does not affect scaling logic, and a proper cleanup is performed by subsequent retries\.

## Scenario 3: Spot Instance running multi\-node jobs is interrupted<a name="multi-node"></a>

The behavior of this interruption depends on the scheduler being used\.

Slurm  
The job is terminated and given a state code of `NODE_FAIL`\. The compute instance is removed from the scheduler queue\. Other nodes that were running the terminated jobs may be scaled down after the configured [`scaledown_idletime`](scaling-section.md#scaledown-idletime) time has passed\.

SGE  
The job is not terminated and continues to run on the remaining nodes\. The compute node is removed from the scheduler queue, but will appear in the hosts list as an orphaned and unavailable node\.  
The user must delete the job when this occurs \(`qdel <jobid>`\)\. The node still displays in the hosts list \(`qhost`\), although this does not affect AWS ParallelCluster\. To remove the host from the list, you could run the following command after replacing the instance\.  

```
sudo -- bash -c 'source /etc/profile.d/sge.sh; qconf -dattr hostgroup hostlist <hostname> @allhosts; qconf -de <hostname>'
```

Torque  
The job is removed from the system and the node is removed from the scheduler\. The job is not rerun\. If multiple jobs are running on the instance when it is interrupted, Torque may time out during node removal\. An error may display in the [`sqswatcher`](processes.md#sqswatcher) log file\. This does not affect scaling logic, and a proper cleanup is performed by subsequent retries\.

For more information about Spot Instances, see [Spot Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.