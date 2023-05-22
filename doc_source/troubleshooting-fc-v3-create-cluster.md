# Trying to create a cluster<a name="troubleshooting-fc-v3-create-cluster"></a>

When using AWS ParallelCluster version 3\.5\.0 and later to create a cluster, and a cluster creation failed with `--rollback-on-failure` set to `false`, use the [`pcluster describe-cluster`](pcluster.describe-cluster-v3.md) CLI command to get status and failure information\. In this case, the expected `clusterStatus` of the `pcluster describe-cluster` output is `CREATE_FAILED`\. Check the `failures` section in the output to find the `failureCode` and `failureReason`\. Then, in the following section, find the matching `failureCode` for additional troubleshooting help\. For more information, see [`pcluster describe-cluster`](pcluster.describe-cluster-v3.md)\.

In the following sections, we recommend that you check the logs on the head node, such as the `/var/log/cfn-init.log` and `/var/log/chef-client.log` files\. For more information about AWS ParallelCluster logs and how to view them, see [Key logs for debugging](troubleshooting-v3-scaling-issues.md#troubleshooting-v3-key-logs) and [Retrieving and preserving logs](troubleshooting-v3-get-logs.md)\.

If you don't have a `failureCode`, navigate to the AWS CloudFormation console to view the cluster stack\. Check the `Status Reason` for the `HeadNodeWaitCondition` or failures on other resources to find additional failure details\. For more information, see [View AWS CloudFormation events on `CREATE_FAILED`](troubleshooting-v3-cluster-deployment.md#troubleshooting-v3-cluster-deployment-events)\. Check the `/var/log/cfn-init.log` and `/var/log/chef-client.log` files on the head node\.

## `failureCode` is `OnNodeConfiguredExecutionFailure`<a name="create-cluster-on-node-configured-executed-failure-v3"></a>
+ **Why did it fail?**

  You provided a custom script in `OnNodeConfigured` of the head node section in the configuration to create a cluster\. However, the custom script failed to run\.
+ **How to resolve?**

  Check the `/var/log/cfn-init.log` file to learn more about the failure and how to fix the issue in your custom script\. Near the end of this log, you might see run information related to the `OnNodeConfigured` script after the `Running command runpostinstall` message\.

## `failureCode` is `OnNodeConfiguredDownloadFailure`<a name="create-cluster-on-node-configured-download-failure-v3"></a>
+ **Why did it fail?**

  You provided a custom script in `OnNodeConfigured` of the head node section in the configuration to create a cluster\. However, the custom script failed to download\.
+ **How to resolve?**

  Make sure that the URL is valid and that the access is correctly configured\. For more information on the configuration of custom bootstrap scripts, see [Custom bootstrap actions](custom-bootstrap-actions-v3.md)\.

  Check the `/var/log/cfn-init.log`file\. Near the end of this log, you might see run information related to `OnNodeConfigured` script processing, including downloading, after the `Running command runpostinstall` message\.

## `failureCode` is `OnNodeConfiguredFailure`<a name="create-cluster-on-node-configured-failure-v3"></a>
+ **Why did it fail?**

  You provided a custom script in `OnNodeConfigured` of the head node section in the configuration to create a cluster\. However, the use of the custom script failed in the cluster deployment\. An immediate cause can't be determined and additional investigation is needed\.
+ **How to resolve?**

  Check the `/var/log/cfn-init.log`file\. Near the end of this log, you might see run information related to `OnNodeConfigured` script processing after the `Running command runpostinstall` message\.

## `failureCode` is `OnNodeStartExecutionFailure`<a name="create-cluster-on-node-start-execution-failure-v3"></a>
+ **Why did it fail?**

  You provided a custom script in `OnNodeStart` of the head node section in the configuration to create a cluster\. However, the custom script failed to run\.
+ **How to resolve?**

  Check the `/var/log/cfn-init.log` file to learn more about the failure and how to fix the issue in your custom script\. Near the end of this log, you might see run information related to the `OnNodeStart` script after the `Running command runpreinstall` message\.

## `failureCode` is `OnNodeStartDownloadFailure`<a name="create-cluster-on-node-start-download-failure-v3"></a>
+ **Why did it fail?**

  You provided a custom script in `OnNodeStart` of the head node section in the configuration to create a cluster\. However, the custom script failed to download\.
+ **How to resolve?**

  Make sure that the URL is valid and that the access is correctly configured\. For more information on the configuration of custom bootstrap scripts, see [Custom bootstrap actions](custom-bootstrap-actions-v3.md)\.

  Check the `/var/log/cfn-init.log`file\. Near the end of this log, you might see run information related to `OnNodeStart` script processing, including downloading, after the `Running command runpreinstall` message\.

## `failureCode` is `OnNodeStartFailure`<a name="create-cluster-on-node-start-failure-v3"></a>
+ **Why did it fail?**

  You provided a custom script in the `OnNodeStart` of the head node section in the configuration to create a cluster\. However, the use of the custom script failed in the cluster deployment\. An immediate cause can't be determined and additional investigation is needed\.
+ **How to resolve?**

  Check the `/var/log/cfn-init.log`file\. Near the end of this log, you might see run information related to `OnNodeStart` script processing after the `Running command runpreinstall` message\.

## `failureCode` is `EbsMountFailure`<a name="create-cluster-ebs-mount-failure-v3"></a>
+ **Why did it fail?**

  The EBS volume defined in the cluster configuration failed to mount\.
+ **How to resolve?**

  Check the `/var/log/chef-client.log` file for failure details\.

## `failureCode` is `EfsMountFailure`<a name="create-cluster-efs-mount-failure-v3"></a>
+ **Why did it fail?**

  The Amazon EFS volume defined in the cluster configuration failed to mount\.
+ **How to resolve?**

  If you defined an existing Amazon EFS file system, make sure that traffic is allowed between the cluster and the file system\. For more information, see [`SharedStorage`](SharedStorage-v3.md) / [`EfsSettings`](SharedStorage-v3.md#SharedStorage-v3-EfsSettings) / [`FileSystemId`](SharedStorage-v3.md#yaml-SharedStorage-EfsSettings-FileSystemId)\.

  Check the `/var/log/chef-client.log` file for failure details\.

## `failureCode` is `FsxMountFailure`<a name="create-cluster-fsx-mount-failure-v3"></a>
+ **Why did it fail?**

  The Amazon FSx file system defined in the cluster configuration failed to mount\.
+ **How to resolve?**

  If you defined an existing Amazon FSx file system, make sure that traffic is allowed between the cluster and the file system\. For more information, see [`SharedStorage`](SharedStorage-v3.md) / [`FsxLustreSettings`](SharedStorage-v3.md#SharedStorage-v3-FsxLustreSettings) / [`FileSystemId`](SharedStorage-v3.md#yaml-SharedStorage-FsxLustreSettings-FileSystemId)\.

  Check the `/var/log/chef-client.log` file for failure details\.

## `failureCode` is `RaidMountFailure`<a name="create-cluster-raid-mount-failure-v3"></a>
+ **Why did it fail?**

  The RAID volumes defined in the cluster configuration failed to mount\.
+ **How to resolve?**

  Check the `/var/log/chef-client.log` file for failure details\.

## `failureCode` is `AmiVersionMismatch`<a name="create-cluster-ami-version-mismatch-v3"></a>
+ **Why did it fail?**

  The AWS ParallelCluster version used to create the custom AMI is different than the AWS ParallelCluster version used to configure the cluster\. In the CloudFormation console, view the cluster CloudFormation stack details and check the `Status Reason` for the `HeadNodeWaitCondition` to get additional details on the AWS ParallelCluster versions and the AMI\. For more information, see [View AWS CloudFormation events on `CREATE_FAILED`](troubleshooting-v3-cluster-deployment.md#troubleshooting-v3-cluster-deployment-events)\.
+ **How to resolve?**

  Make sure the AWS ParallelCluster version used to create the custom AMI is the same AWS ParallelCluster version used to configure the cluster\. You can change either the custom AMI version or the `pcluster` CLI version to make them the same\.

## `failureCode` is `InvalidAmi`<a name="create-cluster-invalid-ami-v3"></a>
+ **Why did it fail?**

  The custom AMI is invalid because it wasn't built using AWS ParallelCluster\.
+ **How to resolve?**

  Use the `pcluster build-image` command to create an AMI by making your AMI the parent image\. For more information, see [`pcluster build-image`](pcluster.build-image-v3.md)\.

## `failureCode` is `HeadNodeBootstrapFailure` with `failureReason` Failed to set up the head node\.<a name="create-cluster-head-node-bootstrap-setup-failure-v3"></a>
+ **Why did it fail?**

  An immediate cause can't be determined and additional investigation is needed\. For example, it could be that the cluster is in protected status, and this could be caused by a failure to provision the static compute fleet\.
+ **How to resolve?**

  Check the `/var/log/chef-client.log.` file for failure details\.
**Note**  
If you see `RuntimeError` exception `Cluster state has been set to PROTECTED mode due to failures detected in static node provisioning`, the cluster is in protected status\. For more information, see [How to debug protected mode](slurm-protected-mode-v3.md#slurm-protected-mode-debug-v3)\.

## `failureCode` is `HeadNodeBootstrapFailure` with `failureReason` Cluster creation timed out\.<a name="create-cluster-head-node-bootstrap-timeout-failure-v3"></a>
+ **Why did it fail?**

  By default, there is a 30 minute time limit for cluster creation to complete\. If cluster creation hasn't completed within this time frame, the cluster creation fails with a timeout error\. The cluster creation can timeout for different reasons\. For example, timeout failures can be caused by a head node creation failure, a network issue, custom scripts that take too long to run in the head node, an error in a custom script that runs in compute nodes, or long wait times for compute node provisioning\. An immediate cause can't be determined and additional investigation is needed\.
+ **How to resolve?**

  Check the `/var/log/cfn-init.log` and `/var/log/chef-client.log` files for failure details\. For more information about AWS ParallelCluster logs and how to get them, see [Key logs for debugging](troubleshooting-v3-scaling-issues.md#troubleshooting-v3-key-logs) and [Retrieving and preserving logs](troubleshooting-v3-get-logs.md)\.

  You might discover the following in these logs\.
  + **Seeing `Waiting for static fleet capacity provisioning` near the end of the `chef-client.log`**

    This indicates that the cluster creation timed out when waiting for static nodes to power up\. For more information, see [Seeing errors in compute node initializations](troubleshooting-fc-v3-compute-node-initialization-v3.md)\.
  + **Seeing `OnNodeConfigured` or `OnNodeStart` head node script hasn't finished at the end of the `cfn-init.log`**

    This indicates that the `OnNodeConfigured` or `OnNodeStart` custom script took a long time to run and caused a timeout error\. Check your custom script for issues that might cause it to run for a long time\. If your custom script requires a long time to run, consider changing the timeout limit by adding a `DevSettings` section to your cluster configuration file, as shown in the following example:

    ```
    DevSettings:
      Timeouts:
        HeadNodeBootstrapTimeout: 1800 # default setting: 1800 seconds
    ```
  + **Can't find the logs, or the head node wasn't created successfully**

    It's possible that the head node wasn't created successfully and the logs can't be found\. In the CloudFormation console, view the cluster stack details to check for additional failure details\.

## `failureCode` is `HeadNodeBootstrapFailure` with `failureReason` Failed to bootstrap the head node\.<a name="create-cluster-head-node-bootstrap-failure-v3"></a>
+ **Why did it fail?**

  An immediate cause can't be determined and additional investigation is needed\.
+ **How to resolve?**

  Check the `/var/log/cfn-init.log` and `/var/log/chef-client.log` files\.

## `failureCode` is `ResourceCreationFailure`<a name="create-cluster-resource-creation-failure-v3"></a>
+ **Why did it fail?**

  The creation of some resources failed during the cluster creation process\. The failure can occur for various reasons\. For example, resource creation failures can be caused by capacity issues or a misconfigured IAM policy\.
+ **How to resolve?**

  In the CloudFormation console, view the cluster stack to check for additional resource creation failure details\.

## `failureCode` is `ClusterCreationFailure`<a name="cluster-creation-failure-v3"></a>
+ **Why did it fail?**

  An immediate cause can't be determined and additional investigation is needed\.
+ **How to resolve?**

  In the CloudFormation console, view the cluster stack and check the `Status Reason` for the `HeadNodeWaitCondition` to find additional failure details\.

  Check the `/var/log/cfn-init.log` and `/var/log/chef-client.log` files\.

## Seeing `WaitCondition timed out...` in CloudFormation stack<a name="create-cluster-wait-condition-timeout-v3"></a>

For more information, see [`failureCode` is `HeadNodeBootstrapFailure` with `failureReason` Cluster creation timed out\.](#create-cluster-head-node-bootstrap-timeout-failure-v3)\.

## Seeing `Resource creation cancelled` in CloudFormation stack<a name="create-cluster-resource-create-error-v3"></a>

For more information, see [`failureCode` is `ResourceCreationFailure`](#create-cluster-resource-creation-failure-v3)\.

## Seeing `Failed to run cfn-init...` or other errors in the AWS CloudFormation stack<a name="create-cluster-cfn-init-fail-error-v3"></a>

Check the `/var/log/cfn-init.log` and `/var/log/chef-client.log` for additional failure details\.

## Seeing `chef-client.log` ends with `INFO: Waiting for static fleet capacity provisioning`<a name="create-cluster-wait-on-fleet-capacity-v3"></a>

This is related to cluster creation timeout when waiting for static nodes to power up\. For more information, see [Seeing errors in compute node initializations](troubleshooting-fc-v3-compute-node-initialization-v3.md)\.

## Seeing `Failed to run preinstall or postinstall in cfn-init.log`<a name="create-cluster-pre-post-install-v3"></a>

You have an `OnNodeConfigured` or `OnNodeStart` script in the cluster configuration `HeadNode` section\. The script isn't working correctly\. Check the `/var/log/cfn-init.log` file for custom script error details\.

## Seeing `This AMI was created with xxx, but is trying to be used with xxx...` in CloudFormation stack<a name="create-cluster-ami-mismatch-error-v3"></a>

For more information, see [`failureCode` is `AmiVersionMismatch`](#create-cluster-ami-version-mismatch-v3)\.

## Seeing `This AMI was not baked by AWS ParallelCluster...` in CloudFormation stack<a name="create-cluster-ami-incomplete-error-v3"></a>

For more information, see [`failureCode` is `InvalidAmi`](#create-cluster-invalid-ami-v3)\.

## Seeing `pcluster create-cluster` command fails to run locally<a name="create-cluster-pcluster-cli-error-v3"></a>

Check the `~/.parallelcluster/pcluster-cli.log` in your local file system for failure details\.

## Additional support<a name="create-cluster-additional-support-v3"></a>

Follow the troubleshooting guidance in [Troubleshooting cluster deployment issues](troubleshooting-v3-cluster-deployment.md)\.

Check to see if your scenario is covered in [GitHub Known Issues](https://github.com/aws/aws-parallelcluster/wiki) at AWS ParallelCluster on GitHub\.

For additional support, see [Additional support](troubleshooting-v3-additional-support.md)\.