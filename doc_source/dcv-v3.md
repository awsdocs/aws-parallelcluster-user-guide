# Connect to the head node through NICE DCV<a name="dcv-v3"></a>

NICE DCV is a remote visualization technology that enables users to securely connect to graphic\-intensive 3D applications that are hosted on a remote high\-performance server\. For more information, see [NICE DCV](https://docs.aws.amazon.com/dcv/)\.

The NICE DCV software is automatically installed on the head node and can be enabled by using the [`Dcv`](HeadNode-v3.md#HeadNode-v3-Dcv) section from the [`HeadNode`](HeadNode-v3.md) configuration\.

```
HeadNode:
  Dcv:
    Enabled: true
```

This way, AWS ParallelCluster sets `/home/<DEFAULT_AMI_USER>` in the head node to the [DCV server storage folder](https://docs.aws.amazon.com/dcv/latest/adminguide/manage-storage.html)\. For more information about NICE DCV configuration parameters, see [`HeadNode`](HeadNode-v3.md) / [`Dcv`](HeadNode-v3.md#HeadNode-v3-Dcv)\. To connect to the NICE DCV session, use the [`pcluster dcv-connect`](pcluster.dcv-connect-v3.md) command\.

## NICE DCV HTTPS certificate<a name="dcv-v3-certificate"></a>

NICE DCV automatically generates a self\-signed certificate to secure traffic between the NICE DCV client and NICE DCV server\.

To replace the default self\-signed NICE DCV certificate with another certificate, first connect to the head node\. Then, copy both the certificate and key to the `/etc/dcv` folder before running the [`pcluster dcv-connect`](pcluster.dcv-connect-v3.md) command\.

For more information, see [Changing the TLS certificate](https://docs.aws.amazon.com/dcv/latest/adminguide/manage-cert.html) in the *NICE DCV Administrator Guide*\.

## Licensing NICE DCV<a name="dcv-v3-license"></a>

The NICE DCV server doesn't require a license server when running on Amazon EC2 instances\. However, the NICE DCV server must periodically connect to an Amazon S3 bucket to determine if a valid license is available\.

AWS ParallelCluster automatically adds the required permissions to the head node IAM policy\. When using a custom IAM Instance Policy, use the permissions described in [NICE DCV on Amazon EC2](https://docs.aws.amazon.com/dcv/latest/adminguide/setting-up-license.html#setting-up-license-ec2) in the *NICE DCV Administrator Guide*\.

For troubleshooting tips, see [Troubleshooting issues in NICE DCV](troubleshooting-v3.md#troubleshooting-v3-nice-dcv)\.