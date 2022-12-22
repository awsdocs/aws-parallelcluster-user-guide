# Integration with Amazon CloudWatch Logs<a name="cloudwatch-logs-v3"></a>

 For more information about CloudWatch Logs, see [Amazon CloudWatch Logs User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/)\. To configure CloudWatch Logs integration, see the [`Monitoring`](Monitoring-v3.md) section\. To learn how to append custom logs to the CloudWatch configuration using `append-config`, see [Multiple CloudWatch agent configuration files](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-common-scenarios.html#CloudWatch-Agent-multiple-config-files) in the *Amazon CloudWatch User Guide*\.

## Amazon CloudWatch Logs cluster logs<a name="cloudwatch-logs-clusters"></a>

A log group is created for each cluster with a name `/aws/parallelcluster/cluster-name-<timestamp>` \(for example, `/aws/parallelcluster/testCluster-202202050215`\)\. Each log \(or set of logs if the path contains a `*`\) on each node has a log stream named `{hostname}.{instance_id}.{logIdentifier}`\. \(For example `ip-172-31-10-46.i-02587cf29cc3048f3.nodewatcher`\.\) Log data is sent to CloudWatch by the [CloudWatch agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html), which runs as `root` on all cluster instances\.

An Amazon CloudWatch dashboard is created when the cluster is created\. This dashboard makes it easy to review the logs stored in CloudWatch Logs\. For more information, see [Amazon CloudWatch dashboard](cloudwatch-dashboard-v3.md)\.

This list contains the *logIdentifier* and path for the log streams available for platforms, schedulers, and nodes\.


**Log streams available for platforms, schedulers, and nodes**  

| Platforms | Schedulers | Nodes | Log streams | 
| --- | --- | --- | --- | 
|  amazon centos ubuntu  |  awsbatch slurm  |  HeadNode  |  dcv\-authenticator: `/var/log/parallelcluster/pcluster_dcv_authenticator.log` dcv\-ext\-authenticator: `/var/log/parallelcluster/pcluster_dcv_connect.log` dcv\-agent: `/var/log/dcv/agent.*.log` dcv\-xsession: `/var/log/dcv/dcv-xsession.*.log` dcv\-server: `/var/log/dcv/server.log` dcv\-session\-launcher: `/var/log/dcv/sessionlauncher.log` Xdcv: `/var/log/dcv/Xdcv.*.log` cfn\-init: `/var/log/cfn-init.log` chef\-client: `/var/log/chef-client.log`  | 
|  amazon centos ubuntu  |  awsbatch slurm  |  ComputeFleet HeadNode  |  cloud\-init: `/var/log/cloud-init.log` supervisord: `/var/log/supervisord.log`  | 
|  amazon centos ubuntu  |  slurm  |  HeadNode  |  sssd: `/var/log/sssd/sssd.log` sssd\_domain\_default: `/var/log/sssd/sssd_default.log` pam\_ssh\_key\_generator: `/var/log/parallelcluster/pam_ssh_key_generator.log` clusterstatusmgtd: `/var/log/parallelcluster/clusterstatusmgtd`  | 
|  amazon centos ubuntu  |  slurm  |  ComputeFleet  |  cloud\-init\-output: `/var/log/cloud-init-output.log`  | 
|  amazon centos ubuntu  |  slurm  |  HeadNode  |  clustermgtd: `/var/log/parallelcluster/clustermgtd` slurm\_resume: `/var/log/parallelcluster/slurm_resume.log` slurm\_suspend: `/var/log/parallelcluster/slurm_suspend.log` slurmctld: `/var/log/slurmctld.log` slurm\_fleet\_status\_manager: `/var/log/parallelcluster/slurm_fleet_status_manager.log`  | 
|  amazon centos ubuntu  |  slurm  |  ComputeFleet  |  computemgtd: `/var/log/parallelcluster/computemgtd` slurmd: `/var/log/slurmd.log` slurm\_prolog\_epilog: `/var/log/parallelcluster/slurm_prolog_epilog.log`  | 
|  amazon centos  |  awsbatch slurm  |  ComputeFleet HeadNode  |  system\-messages: `/var/log/messages`  | 
|  ubuntu  |  awsbatch slurm  |  ComputeFleet HeadNode  |  syslog: `/var/log/syslog`  | 

Jobs in clusters that use AWS Batch store the output of jobs that reached a `RUNNING`, `SUCCEEDED`, or `FAILED` state in CloudWatch Logs\. The log group is `/aws/batch/job`, and the log stream name format is `jobDefinitionName/default/ecs_task_id`\. By default, these logs are set to never expire, but you can modify the retention period\. For more information, see [Change log data retention in CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/SettingLogRetention.html) in the *Amazon CloudWatch Logs User Guide*\.

## Amazon CloudWatch Logs Build image logs<a name="cloudwatch-logs-build-images"></a>

A log group is created for each custom build image with a name `/aws/imagebuilder/ParallelClusterImage-<image-id>`\. A unique log stream with name *\{pcluster\-version\}*/1 contains the output of the build image process\.

You can access the logs using [`pcluster`](pcluster-v3.md) image commands\. For more information, see [AWS ParallelCluster AMI customization](custom-ami-v3.md)\.