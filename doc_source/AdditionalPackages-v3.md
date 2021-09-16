# `AdditionalPackages` section<a name="AdditionalPackages-v3"></a>

**\(Optional\)** Used to identify additional packages to install\.

```
AdditionalPackages:
  IntelSoftware:
    IntelHpcPlatform: boolean
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

## `IntelSoftware`<a name="AdditionalPackages-v3-IntelSoftware"></a>

**\(Optional\)** Defines the configuration for Intel select solutions\.

```
IntelSoftware:
  IntelHpcPlatform: Boolean
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

### `IntelSoftware` Properties<a name="AdditionalPackages-v3-IntelSoftware.properties"></a>

`IntelHpcPlatform` \(**Optional**, `Boolean`\)  
If `true`, indicates that the [End user license agreement](https://software.intel.com/en-us/articles/end-user-license-agreement) for Intel Parallel Studio is accepted\. This causes Intel Parallel Studio to be installed on the head node and shared with the compute nodes\. This adds several minutes to the time it takes the head node to bootstrap\. The `IntelHpcPlatform` setting is only supported on CentOS 7 \.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)