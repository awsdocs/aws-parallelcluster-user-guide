# `[aws]` section<a name="aws"></a>

Specifies AWS Region information\.

These settings apply to all clusters and they are required\.

To store credentials, you can use the environment, IAM roles for Amazon EC2, or the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html), rather than saving credentials into the AWS ParallelCluster config file\.

```
[aws]
# Defaults to us-east-1 if not defined in environment or below
aws_region_name = #region
```

[Update policy: These settings can be changed during an update.](using-pcluster-update.md#update-policy-settings-supported)