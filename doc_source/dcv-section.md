# `[dcv]` Section<a name="dcv-section"></a>

**Topics**
+ [`enable`](#enable)
+ [`port`](#port)
+ [`access_from`](#access-from)

Defines configuration settings for the NICE DCV Server running on the master instance\.
For more information about the NICE DCV integration, see [Connect to the master instance through NICE DCV](dcv.md)\.

The format is `[dcv <dcvname>]`\.

```
[dcv custom-dcv]
enable = master
port = 8443
access_from = 0.0.0.0/0
```

## `enable`<a name="enable"></a>

Defines the NICE DCV must be enabled on the master node or not\.

This parameter is required to enable NICE DCV Server in the master instance and configure the required security group rule\.

**Warning**  
NICE DCV automatically generates a self\-signed certificate that is used to secure traffic between the NICE DCV client
and NICE DCV server running on the master instance\.
To configure your own certificate, see [NICE DCV HTTPs certificate](dcv.md#dcv-certificate)\.

The following example enables DCV on the master node\.
```
enable = master
```

## `port`<a name="port"></a>

Indicates the HTTPs port on which the DCV Server can be contacted\.

The default value is `8443`\.

```
port = 8443
```

## `access_from`<a name="access-from"></a>

Specifies a CIDR\-formatted IP range to allow HTTPs access from\.

This parameter is used only when AWS ParallelCluster creates the security group\.

The default value is `0.0.0.0/0`\.

```
access_from = 0.0.0.0/0
```
