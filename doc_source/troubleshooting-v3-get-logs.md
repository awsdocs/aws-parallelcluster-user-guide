# Retrieving and preserving logs<a name="troubleshooting-v3-get-logs"></a>

AWS ParallelCluster creates EC2 metrics for HeadNode and Compute instances and storage\. You can view the metrics in the CloudWatch console **Custom Dashboards**\. AWS ParallelCluster also creates cluster CloudWatch log streams in log groups\. You can view these logs in the CloudWatch console **Custom Dashboards** or **Log groups**\. The [Monitoring](Monitoring-v3.md#yaml-Monitoring-Logs-CloudWatch) cluster configuration section describes how you can modify the cluster CloudWatch logs and dashboard\. For more information, see [Integration with Amazon CloudWatch Logs](cloudwatch-logs-v3.md) and [Amazon CloudWatch dashboard](cloudwatch-dashboard-v3.md)\.

Logs are a useful resource for troubleshooting issues\. For example, if you want to delete a failing cluster, it might be useful to first create an archive of the cluster logs\. Follow the steps in [Archive logs](#troubleshooting-v3-get-logs-archive) to create an archive\.

**Topics**
+ [Cluster logs unavailable in CloudWatch](#troubleshooting-v3-get-logs-unavailable)
+ [Archive logs](#troubleshooting-v3-get-logs-archive)
+ [Preserved logs](#troubleshooting-v3-get-logs-preserve)
+ [Terminated node logs](#troubleshooting-v3-get-logs-terminated-node)

## Cluster logs unavailable in CloudWatch<a name="troubleshooting-v3-get-logs-unavailable"></a>

If cluster logs aren't available in CloudWatch, check to make sure you haven't overwritten the AWS ParallelCluster CloudWatch log configuration when adding custom logs to the configuration\.

To add custom logs to the CloudWatch configuration, make sure you append to the configuration rather than fetch and overwrite it\. For more information on `fetch-config` and `append-config`, see [Multiple CloudWatch agent configuration files](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-common-scenarios.html#CloudWatch-Agent-multiple-config-files) in the *CloudWatch User Guide*\.

To restore the AWS ParallelCluster CloudWatch log configuration, you can run the following command inside an AWS ParallelCluster node:

```
$ /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json -s
```

## Archive logs<a name="troubleshooting-v3-get-logs-archive"></a>

You can archive the logs in Amazon S3 or in a local file \(depending on the `--output-file` parameter\)\.

**Note**  
Add permissions to the Amazon S3 bucket policy to grant CloudWatch access\. For more information, see [Set permissions on an Amazon S3 bucket](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/S3ExportTasks.html#S3Permissions) in the *CloudWatch Logs User Guide*\.

```
$ pcluster export-cluster-logs --cluster-name mycluster --region eu-west-1 \
  --bucket bucketname --bucket-prefix logs
{
  "url": "https://bucketname.s3.eu-west-1.amazonaws.com/export-log/mycluster-logs-202109071136.tar.gz?..."
}

# use the --output-file parameter to save the logs locally
$ pcluster export-cluster-logs --cluster-name mycluster --region eu-west-1 \
  --bucket bucketname --bucket-prefix logs --output-file /tmp/archive.tar.gz
{
  "path": "/tmp/archive.tar.gz"
}
```

The archive contains the Amazon CloudWatch Logs streams and AWS CloudFormation stack events from the head node and compute nodes for the last 14 days, unless specified explicitly in the configuration or in the parameters for the `export-cluster-logs` command\. The time it takes for the command to finish depends on the number of nodes in the cluster and the number of log streams available in CloudWatch Logs\. For more information about the available log streams, see [Integration with Amazon CloudWatch Logs](cloudwatch-logs-v3.md)\.

## Preserved logs<a name="troubleshooting-v3-get-logs-preserve"></a>

Starting from version 3\.0\.0, AWS ParallelCluster preserves CloudWatch Logs by default when a cluster is deleted\. If you want to delete a cluster and preserve its logs, make sure that [`Monitoring`](Monitoring-v3.md) / [`Logs`](Monitoring-v3.md#yaml-Monitoring-Logs) / [`CloudWatch`](Monitoring-v3.md#yaml-Monitoring-Logs-CloudWatch) / [`DeletionPolicy`](Monitoring-v3.md#yaml-Monitoring-Logs-CloudWatch-DeletionPolicy) isn't set to `Delete` in the cluster configuration\. Otherwise, change the value for this field to `Retain`, and run the `pcluster update-cluster` command\. Then, run `pcluster delete-cluster --cluster-name <cluster_name>` to delete the cluster, but retain the log group that’s stored in Amazon CloudWatch\.

## Terminated node logs<a name="troubleshooting-v3-get-logs-terminated-node"></a>

If a static compute node unexpectedly terminates and CloudWatch has no logs for it, check if AWS ParallelCluster has recorded the console output for that compute node on the head node in the `/var/log/parallelcluster/compute_console_output` log\. For more information, see [Key logs for debugging](troubleshooting-v3-scaling-issues.md#troubleshooting-v3-key-logs)\.

If the `/var/log/parallelcluster/compute_console_output` log isn't available or doesn't contain the output for the node, use the AWS CLI to retrieve the console output from the failed node\. Log in to the cluster head node and get the failed node `instance-id` from the `/var/log/parallelcluster/slurm_resume.log` file\. 

Retrieve the console output by using the following command with the `instance-id`:

```
$ aws ec2 get-console-output --instance-id i-abcdef01234567890
```

If a dynamic compute node self\-terminates after launching and CloudWatch has no logs for it, submit a job that activates a cluster scaling action\. Wait for the instance to fail and retrieve the instance console log\.

Log in to the cluster head node and get the compute node `instance-id` from the `/var/log/parallelcluster/slurm_resume.log` file\.

Retrieve the instance console log by using the following command:

```
$ aws ec2 get-console-output --instance-id i-abcdef01234567890
```

The console output log can help you debug the root cause of a compute node failure when the compute node log isn't available\.