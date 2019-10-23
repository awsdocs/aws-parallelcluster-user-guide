# Connect to the master instance through NICE DCV<a name="dcv"></a>

NICE DCV is a remote visualization technology that enables users to securely connect to graphic\-intensive 3D
applications hosted on a remote server\. For more information, see [NICE DCV](https://docs.aws.amazon.com/dcv/index.html)\.

The NICE DCV software is automatically installed on the master instance when using `base_os = centos7`\.

To enable NICE DCV in the master instance, it is required to define the [`dcv_settings`](cluster-definition.md#dcv-settings)
parameter in the [[cluster] section](cluster-definition.md) and create a new [[dcv] section](dcv-section.md), which
must contain at least the `enable = master` configuration parameter\.

```
[cluster custom-cluster]
...
dcv_settings = custom-dcv

[dcv custom-dcv]
enable = master
```

For more information about NICE DCV related configuration parameters, see [[dcv] section](dcv-section.md)\. 


## NICE DCV HTTPs certificate<a name="dcv-certificate"></a>

NICE DCV automatically generates a self\-signed certificate that is used to secure traffic between the NICE DCV client
and NICE DCV server running on the master instance\.
This certificate is used by default if no other certificate is installed on your NICE DCV server\. 

To replace the default NICE DCV certificate with your own certificate,
you need connect to the master instance (see [pcluster ssh](pcluster.ssh.md)) and place the certificate and its key
in the `/etc/dcv/` folder, before running the [pcluster dcv](pcluster.dcv.md) command\.

For more information, see [Changing the TLS Certificate](https://docs.aws.amazon.com/en_us/dcv/latest/adminguide/manage-cert.html)\.


## Licensing NICE DCV<a name="dcv-license"></a>

The NICE DCV server does not require a license server when running on EC2 instances but it requires to periodically 
connects to an Amazon S3 bucket to determine whether a valid license is available\.

AWS ParallelCluster automatically adds the right permissions to the [ParallelCluster Instance Policy](iam.md#parallelclusterinstancepolicy)\.
When using a custom IAM Instance Policy, please be sure to have the right permissions, 
as described in [NICE DCV on Amazon EC2](https://docs.aws.amazon.com/dcv/latest/adminguide/setting-up-license.html#setting-up-license-ec2)\.
