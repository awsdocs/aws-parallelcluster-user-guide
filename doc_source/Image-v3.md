# `Image` section<a name="Image-v3"></a>

**\(Required\)** Defines the operating system for the cluster\.

```
Image:
  Os: string
  CustomAmi: string
```

## `Image` Properties<a name="Image-v3.properties"></a>

`Os` \(**Required**, `String`\)  
Specifies the operating system to use for the cluster\. The supported values are `alinux2`, `centos7`, `ubuntu1804`, and `ubuntu2004`  
Other than the specific Regions mentioned in the following table that don't support `centos7`\. All other AWS commercial Regions support all of the following operating systems\.      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/Image-v3.html)
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`CustomAmi` \(**Optional**, `String`\)  
Specifies the ID of a custom AMI to use for the head and compute nodes instead of the default AMI\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)