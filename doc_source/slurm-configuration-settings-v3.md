# Slurm configuration customization<a name="slurm-configuration-settings-v3"></a>

Starting with AWS ParallelCluster version 3\.6\.0, you can customize the `slurm.conf` Slurm configuration in an AWS ParallelCluster cluster configuration\.

In the cluster configuration, you can customize Slurm configuration parameters by using the following cluster configuration settings:
+ Customize Slurm parameters for the entire cluster by using either the [`SlurmSettings`](Scheduling-v3.md#Scheduling-v3-SlurmSettings) / [`CustomSlurmSettings`](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-CustomSlurmSettings) or the [`CustomSlurmSettingsIncludeFile`](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-CustomSlurmSettingsIncludeFile) parameter\. AWS ParallelCluster fails if you specify both\.
+ Customize Slurm parameters for a queue by using [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`CustomSlurmSettings`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-CustomSlurmSettings) \(mapped to Slurm partitions\)\.
+ Customize Slurm parameters for a compute resource by using [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`ComputeResources`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-ComputeResources) / [`CustomSlurmSettings`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-ComputeResources-CustomSlurmSettings) \(mapped to Slurm nodes\)\.

## Slurm configuration customization limits and considerations when using AWS ParallelCluster<a name="slurm-configuration-considerations-v3"></a>


+ For `CustomSlurmSettings` and `CustomSlurmSettingsIncludeFile` settings, you can only specify and update `slurm.conf` parameters that are included in the [Slurm version](slurm-workload-manager-v3.md) that's supported by the AWS ParallelCluster version that you are using to configure a cluster\.
+ If you specify custom Slurm configurations in any of the `CustomSlurmSettings` parameters, AWS ParallelCluster performs validation checks and prevents setting or updating Slurm configuration parameters that conflict with AWS ParallelCluster logic\. The Slurm configuration parameters that are known to conflict with AWS ParallelCluster are identified in deny lists\. The deny lists can change in future AWS ParallelCluster versions if other Slurm features are added\. For more information, see [Deny\-listed Slurm configuration parameters for `CustomSlurmSettings`](#slurm-configuration-denylists-v3)\.
+ AWS ParallelCluster only checks whether a parameter is in a deny list\. AWS ParallelCluster doesn't validate your custom Slurm configuration parameter syntax or semantics\. You are responsible for validating your custom Slurm configuration parameters\. Invalid custom Slurm configuration parameters can cause Slurm daemon failures that can lead to cluster create and update failures\.
+ If you specify custom Slurm configurations in `CustomSlurmSettingsIncludeFile`, AWS ParallelCluster doesn't perform any validation\.
+ You can update `CustomSlurmSettings` and `CustomSlurmSettingsIncludeFile` without stopping and starting the compute fleet\. In this case, AWS ParallelCluster restarts the `slurmctld` daemon and runs the `scontrol reconfigure` command\.

  Some Slurm configuration parameters might require different operations before a change is registered in the entire cluster\. For example, they might require a restart of all daemons in the cluster\. You are responsible for verifying whether AWS ParallelCluster operations are sufficient for propagating your custom Slurm configuration parameter settings during updates\. If you find that AWS ParallelCluster operations aren't sufficient, it's your responsibility to provide the additional actions required to propagate the updated settings as recommended in the [Slurm documentation](https://slurm.schedmd.com/documentation.html)\.

## Deny\-listed Slurm configuration parameters for `CustomSlurmSettings`<a name="slurm-configuration-denylists-v3"></a>

The following tables list the parameters with the AWS ParallelCluster versions that deny their use, starting with version 3\.6\.0\. `CustomSlurmSettings` isn't supported for AWS ParallelCluster versions earlier than version 3\.6\.0\.


**Deny\-listed parameters at cluster level:**  

| Slurm parameter | Deny\-listed in AWS ParallelCluster versions | 
| --- | --- | 
|  CommunicationParameters  |  3\.6\.0  | 
|  Epilog  |  3\.6\.0  | 
|  GresTypes  |  3\.6\.0  | 
|  LaunchParameters  |  3\.6\.0  | 
|  Prolog  |  3\.6\.0  | 
|  ReconfigFlags  |  3\.6\.0  | 
|  ResumeFailProgram  |  3\.6\.0  | 
|  ResumeProgram  |  3\.6\.0  | 
|  ResumeTimeout  |  3\.6\.0  | 
|  SlurmctldHost  |  3\.6\.0  | 
|  SlurmctldLogFile  |  3\.6\.0  | 
|  SlurmctldParameters  |  3\.6\.0  | 
|  SlurmdLogfile  |  3\.6\.0  | 
|  SlurmUser  |  3\.6\.0  | 
|  SuspendExcNodes  |  3\.6\.0  | 
|  SuspendProgram  |  3\.6\.0  | 
|  SuspendTime  |  3\.6\.0  | 
|  TaskPlugin  |  3\.6\.0  | 
|  TreeWidth  |  3\.6\.0  | 


**Deny\-listed parameters at cluster level when the [native Slurm accounting integration](slurm-accounting-v3.md) is configured in the cluster configuration:**  

| Slurm parameter | Deny\-listed in AWS ParallelCluster versions | 
| --- | --- | 
|  AccountingStorageType  |  3\.6\.0  | 
|  AccountingStorageHost  |  3\.6\.0  | 
|  AccountingStoragePort  |  3\.6\.0  | 
|  AccountingStorageUser  |  3\.6\.0  | 
|  JobAcctGatherType  |  3\.6\.0  | 


**Deny\-listed parameters at the queue \(partition\) level for queues managed by AWS ParallelCluster:**  

| Slurm parameter | Deny\-listed in AWS ParallelCluster versions | 
| --- | --- | 
|  Nodes  |  3\.6\.0  | 
|  PartitionName  |  3\.6\.0  | 
|  ResumeTimeout  |  3\.6\.0  | 
|  State  |  3\.6\.0  | 
|  SuspendTime  |  3\.6\.0  | 


**Deny\-listed parameters at the compute resource \(node\) level for compute resource managed by AWS ParallelCluster:**  

| Slurm parameter | Deny\-listed in AWS ParallelCluster versions | 
| --- | --- | 
|  CPUs  |  3\.6\.0  | 
|  Features  |  3\.6\.0  | 
|  Gres  |  3\.6\.0  | 
|  NodeAddr  |  3\.6\.0  | 
|  NodeHostname  |  3\.6\.0  | 
|  NodeName  |  3\.6\.0  | 