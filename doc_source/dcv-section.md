# `[dcv]` Section<a name="dcv-section"></a>

Defines configuration settings for the NICE DCV server running on the master instance\.

To create and configure a NICE DCV server, specify a [`dcv_settings`](cluster-definition.md#dcv-settings) with the name of your section, with [`enable`](#dcv-section-enable) set to `master`, and a [`base_os`](cluster-definition.md#base-os) set to `centos7`\.

The format is `[dcv <dcvname>]`\.

```
[dcv custom-dcv]
enable = master
port = 8443
access_from = 0.0.0.0/0
```

**Note**  
Support for the `[[dcv] section](#dcv-section)` was added in AWS ParallelCluster 2\.5\.0\.

## `enable`<a name="dcv-section-enable"></a>

 **\(Required\)** Indicates whether NICE DCV is enabled on the master node\. To enable NICE DCV on the master node and configure the required security group rule, set `enable` to `master`\.

The default value is `NONE`\.

The following example enables NICE DCV on the master node\.

```
enable = master
```

**Note**  
NICE DCV automatically generates a self\-signed certificate that is used to secure traffic between the NICE DCV client and NICE DCV server running on the master instance\. To configure your own certificate, see [NICE DCV HTTPS Certificate](dcv.md#dcv-certificate)\.

## `port`<a name="dcv-section-port"></a>

 **\(Optional\)** Specifies the port for NICE DCV\.

The default value is `8443`\.

```
port = 8443
```

## `access_from`<a name="dcv-section-access-from"></a>

 **\(Optional\)** Specifies the CIDR\-formatted IP range for connections to NICE DCV\. This setting is used only when AWS ParallelCluster creates the security group\.

The default value is `0.0.0.0/0`\.

```
access_from = 0.0.0.0/0
```