# `[dcv]` Section<a name="dcv-section"></a>

Defines configuration settings for the NICE DCV server running on the master instance\.

To create and configure a NICE DCV server, specify a [`dcv_settings`](cluster-definition.md#dcv-settings) with the name of your section, with [`enable`](#dcv-section-enable) set to `master`, and a [`base_os`](cluster-definition.md#base-os) set to `centos7`\.

```
[dcv custom-dcv]
enable = master
port = 8443
access_from = 0.0.0.0/0
```

**Note**  
Support for the `[[dcv] section](#dcv-section)` was added in AWS ParallelCluster 2\.5\.0\.

## `enable`<a name="dcv-section-enable"></a>

 **\(Required\)** Indicates whether NICE DCV is enabled on the master node\. To enable NICE DCV on the master node, set `enable` to `master`\.

The default value is `NONE`\.

```
enable = master
```

## `port`<a name="dcv-section-port"></a>

 **\(Optional\)** Specifies the port for NICE DCV\.

The default value is `8443`\.

```
port = 8443
```

## `access_from`<a name="dcv-section-access-from"></a>

 **\(Optional\)** Specifies the CIDR for connections to NICE DCV\.

The default value is `0.0.0.0/0`\.

```
access_from = 0.0.0.0/0
```