# Schedulers supported by AWS ParallelCluster<a name="schedulers"></a>

AWS ParallelCluster supports several schedulers, set using the [`scheduler`](cluster-definition.md#scheduler) setting\.

**Note**  
Starting with version 2\.11\.5, AWS ParallelCluster doesn't support the use of SGE or Torque schedulers\. You can continue using them in versions up to and including 2\.11\.4, but they aren't eligible for future updates or troubleshooting support from the AWS service and AWS Support teams\.

**Topics**
+ [Son of Grid Engine](schedulers.sge.md)
+ [Slurm Workload Manager](schedulers.slurm.md)
+ [Torque Resource Manager](schedulers.torque.md)
+ [AWS Batch](awsbatchcli.md)