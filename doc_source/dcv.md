# Connect to the master instance through NICE DCV<a name="dcv"></a>

NICE DCV is a remote visualization technology that enables users to securely connect to graphic\-intensive 3D applications hosted on a remote high\-performance server\. For more information, see [NICE DCV](https://docs.aws.amazon.com/dcv/)\.

The NICE DCV software is automatically installed on the master instance when using `[`base_os`](cluster-definition.md#base-os) = centos7`\.

To enable NICE DCV on the master instance, `[`dcv_settings`](cluster-definition.md#dcv-settings)` must contain the name of a [[dcv] section](dcv-section.md) that has `[`enable`](dcv-section.md#dcv-section-enable) = master` and [`base_os`](cluster-definition.md#base-os) must be set to `centos7`\.

```
[cluster custom-cluster]
...
dcv_settings = custom-dcv
...
[dcv custom-dcv]
enable = master
```

For more information about NICE DCV configuration parameters, see [`dcv_settings`](cluster-definition.md#dcv-settings)\. To connect to the NICE DCV session, use the `[`pcluster dcv`](pcluster.dcv.md)` command\.

**Note**  
Support for NICE DCV was added in AWS ParallelCluster 2\.5\.0\.

## NICE DCV HTTPS Certificate<a name="dcv-certificate"></a>

NICE DCV automatically generates a self\-signed certificate to secure traffic between the NICE DCV client and NICE DCV server\.

To replace the default self\-signed NICE DCV certificate with another certificate, first connect to the master instance\. Then, copy both the certificate and key to the `/etc/dcv` folder before running the `[`pcluster dcv`](pcluster.dcv.md)` command\.

For more information, see [Changing the TLS Certificate](https://docs.aws.amazon.com/dcv/latest/adminguide/manage-cert.html) in the *NICE DCV Administrator Guide*\.

## Licensing NICE DCV<a name="dcv-license"></a>

The NICE DCV server does not require a license server when running on Amazon EC2 instances\. However, the NICE DCV server must periodically connect to an Amazon S3 bucket to determine whether a valid license is available\.

AWS ParallelCluster automatically adds the required permissions to the [ParallelClusterInstancePolicy](iam.md#parallelclusterinstancepolicy)\. When using a custom IAM Instance Policy, use the permissions described in [NICE DCV on Amazon EC2](https://docs.aws.amazon.com/dcv/latest/adminguide/setting-up-license.html#setting-up-license-ec2) in the *NICE DCV Administrator Guide*\.