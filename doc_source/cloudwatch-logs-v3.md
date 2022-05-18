# Integration with Amazon CloudWatch Logs<a name="cloudwatch-logs-v3"></a>

 For more information about CloudWatch Logs, see [Amazon CloudWatch Logs User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/)\. To configure CloudWatch Logs integration, see the `Monitoring` section\. To learn how to append custom logs to the CloudWatch configuration using `append-config`, see [Multiple CloudWatch agent configuration files](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-common-scenarios.html#CloudWatch-Agent-multiple-config-files) in the *Amazon CloudWatch User Guide*\.

## Amazon CloudWatch Logs cluster logs<a name="cloudwatch-logs-clusters"></a>

A log group is created for each cluster with a name `/aws/parallelcluster/cluster-name-<timestamp>` \(for example, `/aws/parallelcluster/testCluster-202202050215`\)\. Each log \(or set of logs if the path contains a `*`\) on each node has a log stream named `{hostname}.{instance_id}.{logIdentifier}`\. \(For example `ip-172-31-10-46.i-02587cf29cc3048f3.nodewatcher`\.\) Log data is sent to CloudWatch by the [CloudWatch agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html), which runs as `root` on all cluster instances\.

An Amazon CloudWatch dashboard is created when the cluster is created\. This dashboard makes it easy to review the logs stored in CloudWatch Logs\. For more information, see [Amazon CloudWatch dashboard](cloudwatch-dashboard-v3.md)\.

This list contains the path of the logs and the *logIdentifier* used for those logs\.
+ `/var/log/cfn-init.log` \(`cfn-init`\)
+ `/var/log/chef-client.log` \(`chef-client`\)
+ `/var/log/cloud-init.log` \(`cloud-init`\)
+ `/var/log/cloud-init-output.log` \(`cloud-init-output`\)
+ `/var/log/dcv/agent.*.log` \(`dcv-agent`\)
+ `/var/log/dcv/dcv-xsession.*.log` \(`dcv-xsession`\)
+ `/var/log/dcv/server.log` \(`dcv-server`\)
+ `/var/log/dcv/sessionlauncher.log` \(`dcv-session-launcher`\)
+ `/var/log/dcv/Xdcv.*.log` \(`Xdcv`\)
+ `/var/log/messages` \(`system-messages`\)
+ `/var/log/parallelcluster/clustermgtd` \(`clustermgtd`\)
+ `/var/log/parallelcluster/computemgtd` \(`computemgtd`\)
+ `/var/log/parallelcluster/pcluster_dcv_authenticator.log` \(`dcv-authenticator`\)
+ `/var/log/parallelcluster/pcluster_dcv_connect.log` \(`dcv-ext-authenticator`\)
+ `/var/log/parallelcluster/slurm_resume.log` \(`slurm_resume`\)
+ `/var/log/parallelcluster/slurm_suspend.log` \(`slurm_suspend`\)
+ `/var/log/slurmctld.log` \(`slurmctld`\)
+ `/var/log/slurmd.log` \(`slurmd`\)
+ `/var/log/supervisord.log` \(`supervisord`\)
+ `/var/log/syslog` \(`syslog`\)

Jobs in clusters that use AWS Batch store the output of jobs that reached a `RUNNING`, `SUCCEEDED`, or `FAILED` state in CloudWatch Logs\. The log group is `/aws/batch/job`, and the log stream name format is `jobDefinitionName/default/ecs_task_id`\. By default, these logs are set to never expire, but you can modify the retention period\. For more information, see [Change log data retention in CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/SettingLogRetention.html) in the *Amazon CloudWatch Logs User Guide*\.

## Amazon CloudWatch Logs Build image logs<a name="cloudwatch-logs-build-images"></a>

A log group is created for each custom build image with a name `/aws/imagebuilder/ParallelClusterImage-<image-id>`\. A unique log stream with name *\{pcluster\-version\}*/1 contains the output of the build image process\.

You can access the logs using `pcluster` image commands\. For more information, see [AWS ParallelCluster AMI customization](custom-ami-v3.md)\.