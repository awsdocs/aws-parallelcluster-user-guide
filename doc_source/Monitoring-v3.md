# `Monitoring` section<a name="Monitoring-v3"></a>

**\(Optional\)** Specifies the monitoring settings for the cluster\.

```
Monitoring:
  DetailedMonitoring: boolean
  Logs:
    CloudWatch:
      Enabled: boolean
      RetentionInDays: integer
      DeletionPolicy: string
  Dashboards:
    CloudWatch:
      Enabled: boolean
```

[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

## `Monitoring` properties<a name="Monitoring-v3.properties"></a>

`DetailedMonitoring` \(**Optional**, `Boolean`\)  
If `true`, detailed monitoring is enabled for all cluster nodes\. This enables 1 minute monitoring in the Amazon EC2 console\. The default value is `false`\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

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
The number of days to retain the log events in CloudWatch Logs\. The default value is 14\. The supported values are 1, 3, 5, 7, 14, 30, 60, 90, 120, 150, 180, 365, 400, 545, 731, 1827, and 3653\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)  
`DeletionPolicy` \(**Optional**, `String`\)  
Indicates whether to delete log events on CloudWatch Logs when the cluster is deleted\. The possible values are `Delete` and `Retain`\. The default value is `Retain`\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)

`Dashboards` \(**Optional**\)  
The dashboard settings for the cluster\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)    
`CloudWatch` \(**Optional**\)  
The CloudWatch dashboard settings for the cluster\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)    
`Enabled` \(**Required**, `Boolean`\)  
If `true`, the CloudWatch dashboard is enabled\. The default value is `true`\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)