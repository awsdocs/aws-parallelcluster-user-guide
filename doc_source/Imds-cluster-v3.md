# `Imds` section<a name="Imds-cluster-v3"></a>

**\(Optional\)** Specifies the global instance metadata service \(IMDS\) configuration\.

```
Imds:
  ImdsSupport: string
```

## `Imds` Properties<a name="Imds-cluster-v3.properties"></a>

`ImdsSupport` \(**Optional**, `String`\)  
Specifies which IMDS versions are supported in the cluster nodes\. Supported values are `v1.0` and `v2.0`\. The default value is `v1.0`\.  
If `ImdsSupport` is set to `v1.0`, both IMDSv1 and IMDSv2 are supported\.  
If `ImdsSupport` is set to `v2.0`, only IMDSv2 is supported\.  
For more information, see [Use IMDSv2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service.html) in the *EC2 User Guide for Linux instances*\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)  
At a future date, the `ImdsSupport` default value is going to change from `v1.0` to `v2.0`\. We recommend that you set `ImdsSupport` to `v2.0` and replace IMDSv1 with IMDSv2 in your custom actions calls\.  
Support for [`Imds`](#Imds-cluster-v3) / [`ImdsSupport`](#yaml-cluster-Imds-ImdsSupport) is added with AWS ParallelCluster version 3\.3\.0\.