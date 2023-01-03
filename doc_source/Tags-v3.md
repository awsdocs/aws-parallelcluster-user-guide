# `Tags` section<a name="Tags-v3"></a>

**\(Optional\), Array** Defines the tags that are used by AWS CloudFormation and propagated to all the cluster resources\. For more information, see [AWS CloudFormation resource tag](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-resource-tags.html) in the *AWS CloudFormation User Guide*\.

```
Tags:
  - Key: string
    Value: string
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

## `Tags` properties<a name="Tags-v3.properties"></a>

`Key` \(**Required**, `String`\)  
Defines the name of the tag\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`Value` \(**Required**, `String`\)  
Defines the value of the tag\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)