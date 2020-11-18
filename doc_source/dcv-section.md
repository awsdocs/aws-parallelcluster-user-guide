# `[dcv]` section<a name="dcv-section"></a>

Defines configuration settings for the NICE DCV server running on the head node\.

To create and configure a NICE DCV server, specify a [`dcv_settings`](cluster-definition.md#dcv-settings) with the name of your section, with [`enable`](#dcv-section-enable) set to `master`, and a [`base_os`](cluster-definition.md#base-os) set to `alinux2`, `centos7`, `centos8` or `ubuntu1804`\.

The format is `[dcv dcv-name]`\. *dcv\-name* must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

```
[dcv custom-dcv]
enable = master
port = 8443
access_from = 0.0.0.0/0
```

**Important**  
By default the NICE DCV port setup by AWS ParallelCluster is open to all IPv4 addresses\. However, you can connect to a NICE DCV port only if you have the URL for the NICE DCV session and connect to the NICE DCV session within 30 seconds of when the URL is returned from `pcluster dcv connect`\. Use the [`access_from`](#dcv-section-access-from) setting to further restrict access to the NICE DCV port with a CIDR\-formatted IP range, and use the [`port`](#dcv-section-port) setting to set a nonstandard port\.

**Note**  
Support for the [`[dcv]` section](#dcv-section)on `centos8` was added in AWS ParallelCluster version 2\.10\.0\. Support for the [`[dcv]` section](#dcv-section) on AWS Graviton\-based instances was added in AWS ParallelCluster version 2\.9\.0\. Support for the [`[dcv]` section](#dcv-section) on `alinux2` and `ubuntu1804` was added in AWS ParallelCluster version 2\.6\.0\. Support for the [`[dcv]` section](#dcv-section)on `centos7` was added in AWS ParallelCluster version 2\.5\.0\.

## `access_from`<a name="dcv-section-access-from"></a>

 **\(Optional, Recommended\)** Specifies the CIDR\-formatted IP range for connections to NICE DCV\. This setting is used only when AWS ParallelCluster creates the security group\.

The default value is `0.0.0.0/0`, which allows access from any internet address\.

```
access_from = 0.0.0.0/0
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `enable`<a name="dcv-section-enable"></a>

 **\(Required\)** Indicates whether NICE DCV is enabled on the head node\. To enable NICE DCV on the head node and configure the required security group rule, set the `enable` setting to `master`\.

The default value is `NONE`\.

The following example enables NICE DCV on the head node\.

```
enable = master
```

**Note**  
NICE DCV automatically generates a self\-signed certificate that is used to secure traffic between the NICE DCV client and NICE DCV server running on the head node\. To configure your own certificate, see [NICE DCV HTTPS certificate](dcv.md#dcv-certificate)\.

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `port`<a name="dcv-section-port"></a>

 **\(Optional\)** Specifies the port for NICE DCV\.

The default value is `8443`\.

```
port = 8443
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)