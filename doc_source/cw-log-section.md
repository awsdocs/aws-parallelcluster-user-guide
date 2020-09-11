# `[cw_log]` section<a name="cw-log-section"></a>

Defines configuration settings for CloudWatch Logs\.

The format is `[cw_log cw-log-name]`\. *cw\-log\-name* must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

```
[cw_log custom-cw-log]
enable = true
retention_days = 14
```

For more information, see [Integration with Amazon CloudWatch Logs](cloudwatch-logs.md)\.

**Note**  
Support for `cw_log` was added in AWS ParallelCluster version 2\.6\.0\.

## `enable`<a name="cw-log-section-enable"></a>

 **\(Optional\)** Indicates whether CloudWatch Logs is enabled\.

The default value is `true`\. Use `false` to disable CloudWatch Logs\.

The following example enables CloudWatch Logs\.

```
enable = true
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `retention_days`<a name="cw-log-section-retention-days"></a>

 **\(Optional\)** Indicates how many days CloudWatch Logs retains individual log events\.

The default value is `14`\. The supported values are 1, 3, 5, 7, 14, 30, 60, 90, 120, 150, 180, 365, 400, 545, 731, 1827, and 3653\.

The following example configures CloudWatch Logs to retain log events for 30 days\.

```
retention_days = 30
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)