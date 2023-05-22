# `Monitoring` section<a name="Monitoring-v3"></a>

**\(Optional\)** Specifies the monitoring settings for the cluster\.

```
Monitoring:
  Logs:
    CloudWatch:
      Enabled: boolean
      RetentionInDays: integer
      DeletionPolicy: string
    Rotation:
      Enabled: boolean
  Dashboards:
    CloudWatch:
      Enabled: boolean
  DetailedMonitoring: boolean
```

[Update policy: This setting is not analyzed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-ignored-v3)

## `Monitoring` properties<a name="Monitoring-v3.properties"></a>

`Logs` \(**Optional**\)  
The log settings for the cluster\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)    
`CloudWatch` \(**Optional**\)  
The CloudWatch Logs settings for the cluster\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)    
`Enabled` \(**Required**, `Boolean`\)  
If `true`, cluster logs are streamed to CloudWatch Logs\. The default value is `true`\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)  
`RetentionInDays` \(**Optional**, `Integer`\)  
The number of days to retain the log events in CloudWatch Logs\. The default value is 180\. The supported values are 1, 3, 5, 7, 14, 30, 60, 90, 120, 150, 180, 365, 400, 545, 731, 1827, and 3653\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)  
`DeletionPolicy` \(**Optional**, `String`\)  
Indicates whether to delete log events on CloudWatch Logs when the cluster is deleted\. The possible values are `Delete` and `Retain`\. The default value is `Retain`\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)  
`Rotation` \(**Optional**\)  
The log rotation settings for the cluster\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)    
`Enabled` \(**Required**, `Boolean`\)  
If `true`, log rotation is enabled\. The default is `true`\. When a AWS ParallelCluster configured log file reaches a certain size, it is rotated and a single backup is maintained\. For more information, see [AWS ParallelCluster configured log rotation](log-rotation-v3.md)\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`Dashboards` \(**Optional**\)  
The dashboard settings for the cluster\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)    
`CloudWatch` \(**Optional**\)  
The CloudWatch dashboard settings for the cluster\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)    
`Enabled` \(**Required**, `Boolean`\)  
If `true`, the CloudWatch dashboard is enabled\. The default value is `true`\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)  
Starting with AWS ParallelCluster version 3\.6\.0, you can view `disk_used_percent` and `memory_used_percent` metric alarms in the Amazon CloudWatch console\. For more information, see [Amazon CloudWatch alarms for cluster metrics](cloudwatch-alarms-v3.md)\.

`DetailedMonitoring` \(**Optional**, `Boolean`\)  
If set to `true`, detailed monitoring is enabled for the compute fleet EC2 instances\. When enabled, the Amazon EC2 console displays graphs for monitoring the instances at 1 minute intervals\. There are added costs when this feature is enabled\. The default is `false`\.  
For more information, see [Enable or turn off detailed monitoring for your instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-cloudwatch-new.html) in the *Amazon EC2 User Guide for Linux Instances*\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)  
`DetailedMonitoring` is added starting with AWS ParallelCluster version 3\.6\.0\.