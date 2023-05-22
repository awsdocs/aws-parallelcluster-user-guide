# AWS ParallelCluster configured log rotation<a name="log-rotation-v3"></a>

The AWS ParallelCluster log rotation configuration is located in the `/etc/logrotate.d/parallelcluster_log_rotation` file\. When a configured log rotates, the current log content is preserved in a single backup and the emptied log resumes logging\.

Only 1 backup is maintained for each configured log\.

AWS ParallelCluster configures a fast\-growing log to rotate when it reaches 50 MB in size\. Fast\-growing logs are related to scaling and Slurm, including `/var/log/parallelcluster/clustermgtd`, `/var/log/parallelcluster/slurm_resume.log`, and `/var/log/slurmctld.log`\.

AWS ParallelCluster configures a slow\-growing log to rotate when it reaches 10 MB in size\.

You can view earlier logs that are retained for the number of days defined in the cluster configuration [`Logs`](Monitoring-v3.md#yaml-Monitoring-Logs) / [`CloudWatch`](Monitoring-v3.md#yaml-Monitoring-Logs-CloudWatch) / [`RetentionInDays`](Monitoring-v3.md#yaml-Monitoring-Logs-CloudWatch-RetentionInDays) setting with CloudFormation logging enabled\. Check the `RetentionInDays` settings to see if the number of days needs to be increased for your use case\.

AWS ParallelCluster configures and rotates the following logs:

**Head node logs**

```
/var/log/cloud-init.log
/var/log/supervisord.log
/var/log/cfn-init.log
/var/log/chef-client.log
/var/log/dcv/server.log
/var/log/dcv/sessionlauncher.log
/var/log/dcv/agent.*.log
/var/log/dcv/dcv-xsession.*.log
/var/log/dcv/Xdcv.*.log
/var/log/parallelcluster/pam_ssh_key_generator.log
/var/log/parallelcluster/clustermgtd
/var/log/parallelcluster/clusterstatusmgtd
/var/log/parallelcluster/slurm_fleet_status_manager.log
/var/log/parallelcluster/slurm_resume.log
/var/log/parallelcluster/slurm_suspend.log
/var/log/slurmctld.log
/var/log/slurmdbd.log
/var/log/parallelcluster/compute_console_output.log
```

**Compute node logs**

```
/var/log/cloud-init.log
/var/log/supervisord.log
/var/log/cloud-init-output.log
/var/log/parallelcluster/computemgtd
/var/log/slurmd.log
```