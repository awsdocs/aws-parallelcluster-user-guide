# `[dashboard]` section<a name="dashboard-section"></a>

Defines configuration settings for the CloudWatch dashboard\.

The format is `[dashboard dashboard-name]`\. *dashboard\-name* must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

```
[dashboard custom-dashboard]
enable = true
```

**Note**  
Support for `dashboard` was added in AWS ParallelCluster version 2\.10\.0\.

## `enable`<a name="dashboard-section-enable"></a>

 **\(Optional\)** Indicates whether the CloudWatch dashboard is enabled\.

The default value is `true`\. Use `false` to disable the CloudWatch dashboard\.

The following example enables the CloudWatch dashboard\.

```
enable = true
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)