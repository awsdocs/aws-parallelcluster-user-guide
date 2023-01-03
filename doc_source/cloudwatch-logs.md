# Integration with Amazon CloudWatch Logs<a name="cloudwatch-logs"></a>

Starting with AWS ParallelCluster version 2\.6\.0, common logs are stored in CloudWatch Logs by default\. For more information about CloudWatch Logs, see [Amazon CloudWatch Logs User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/)\. To configure CloudWatch Logs integration, see the [`[cw_log]` section](cw-log-section.md) and the [`cw_log_settings`](cluster-definition.md#cw-log-settings) setting\.

A log group is created for each cluster with a name `/aws/parallelcluster/cluster-name` \(for example, `/aws/parallelcluster/testCluster`\)\. Each log \(or set of logs if the path contains a `*`\) on each node has a log stream named `{hostname}.{instance_id}.{logIdentifier}`\. \(For example `ip-172-31-10-46.i-02587cf29cc3048f3.nodewatcher`\.\) Log data is sent to CloudWatch by the [CloudWatch agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html), which runs as `root` on all cluster instances\.

Starting with AWS ParallelCluster version 2\.10\.0, an Amazon CloudWatch dashboard is created when the cluster is created\. This dashboard makes it easy to review the logs stored in CloudWatch Logs\. For more information, see [Amazon CloudWatch dashboard](cloudwatch-dashboard.md)\.

This list contains the *logIdentifier* and path for the log streams available for platforms, schedulers, and nodes\.


**Log streams available for platforms, schedulers, and nodes**  

| Platforms | Schedulers | Nodes | Log streams | 
| --- | --- | --- | --- | 
|  amazon centos ubuntu  |  awsbatch slurm  |  HeadNode  |  dcv\-authenticator: `/var/log/parallelcluster/pcluster_dcv_authenticator.log` dcv\-ext\-authenticator: `/var/log/parallelcluster/pcluster_dcv_connect.log` dcv\-agent: `/var/log/dcv/agent.*.log` dcv\-xsession: `/var/log/dcv/dcv-xsession.*.log` dcv\-server: `/var/log/dcv/server.log` dcv\-session\-launcher: `/var/log/dcv/sessionlauncher.log` Xdcv: `/var/log/dcv/Xdcv.*.log` cfn\-init: `/var/log/cfn-init.log` chef\-client: `/var/log/chef-client.log`  | 
|  amazon centos ubuntu  |  awsbatch slurm  |  ComputeFleet HeadNode  |  cloud\-init: `/var/log/cloud-init.log` supervisord: `/var/log/supervisord.log`  | 
|  amazon centos ubuntu  |  slurm  |  ComputeFleet  |  cloud\-init\-output: `/var/log/cloud-init-output.log` computemgtd: `/var/log/parallelcluster/computemgtd` slurmd: `/var/log/slurmd.log`  | 
|  amazon centos ubuntu  |  slurm  |  HeadNode  |  clustermgtd: `/var/log/parallelcluster/clustermgtd` slurm\_resume: `/var/log/parallelcluster/slurm_resume.log` slurm\_suspend: `/var/log/parallelcluster/slurm_suspend.log` slurmctld: `/var/log/slurmctld.log`  | 
|  amazon centos  |  awsbatch slurm  |  ComputeFleet HeadNode  |  system\-messages: `/var/log/messages`  | 
|  ubuntu  |  awsbatch slurm  |  ComputeFleet HeadNode  |  syslog: `/var/log/syslog`  | 

Jobs in clusters that use AWS Batch store the output of jobs that reached a `RUNNING`, `SUCCEEDED`, or `FAILED` state in CloudWatch Logs\. The log group is `/aws/batch/job`, and the log stream name format is `jobDefinitionName/default/ecs_task_id`\. By default, these logs are set to never expire, but you can modify the retention period\. For more information, see [Change log data retention in CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/SettingLogRetention.html) in the *Amazon CloudWatch Logs User Guide*\.

**Note**  
`chef-client`, `cloud-init-output`, `clustermgtd`, `computemgtd`, `slurm_resume`, and `slurm_suspend` were added in AWS ParallelCluster version 2\.9\.0\. For AWS ParallelCluster version 2\.6\.0, `/var/log/cfn-init-cmd.log` \(`cfn-init-cmd`\) and `/var/log/cfn-wire.log` \(`cfn-wire`\) were also stored in CloudWatch Logs\.