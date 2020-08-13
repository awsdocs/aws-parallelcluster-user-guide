# Schedulers supported by AWS ParallelCluster<a name="schedulers"></a>

AWS ParallelCluster supports several schedulers, set using the [`scheduler`](cluster-definition.md#scheduler) setting\.

**Warning**  
Starting on December 31, 2021, AWS will no longer include SGE and Torque support for all released versions of AWS ParallelCluster\. Previous versions of AWS ParallelCluster that support SGE and Torque will still be available for download and use\. However, these versions will not be eligible for future updates or troubleshooting support from AWS service and customer support teams\. Moreover, future releases of AWS ParallelCluster made before and after December 31, 2021 will not include support for either SGE or Torque\.

**Topics**
+ [Son of Grid Engine](schedulers.sge.md)
+ [Slurm Workload Manager](schedulers.slurm.md)
+ [Torque Resource Manager](schedulers.torque.md)
+ [AWS Batch](awsbatchcli.md)