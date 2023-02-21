# Troubleshooting issues in clusters with AWS Batch integration<a name="troubleshooting-v3-batch"></a>

This section is relevant to clusters with AWS Batch scheduler integration\.

**Topics**
+ [Head node issues](#troubleshooting-v3-batch-head-node)
+ [Compute issues](#troubleshooting-v3-batch-compute-nodes)
+ [Job failures](#troubleshooting-v3-batch-job-fail)
+ [Connect timeout on endpoint URL error](#troubleshooting-v3-batch-connect-timeout)

## Head node issues<a name="troubleshooting-v3-batch-head-node"></a>

You can troubleshoot head node setup issues in the same way as a Slurm cluster \(except for Slurm specific logs\)\. For more information about these issues, see [Head node](troubleshooting-v3-scaling-issues.md#troubleshooting-v3-node-init.head-node)\.

## Compute issues<a name="troubleshooting-v3-batch-compute-nodes"></a>

AWS Batch manages the scaling and compute aspects of your services\. If you encounter compute related issues, see the AWS Batch [troubleshooting](https://docs.aws.amazon.com/batch/latest/userguide/troubleshooting.html) documentation for help\.

## Job failures<a name="troubleshooting-v3-batch-job-fail"></a>

If a job fails, you can run the [`awsbout`](awsbatchcli.awsbout-v3.md) command to retrieve the job output\. You can also run the [`awsbstat`](awsbatchcli.awsbstat-v3.md) command to obtain a link to the job logs stored by Amazon CloudWatch\.

## Connect timeout on endpoint URL error<a name="troubleshooting-v3-batch-connect-timeout"></a>

If multi\-node parallel jobs fail with error: `Connect timeout on endpoint URL`:
+ In the `awsbout` output log, check that the job is multi\-node parallel from the output: `Detected 3/3 compute nodes. Waiting for all compute nodes to start.`
+ Verify whether the compute nodes subnet is public\.

Multi\-node parallel jobs don't support the use of public subnets when using AWS Batch in AWS ParallelCluster\. Use a private subnet for your compute nodes and jobs\. For more information, see [Compute environment considerations](https://docs.aws.amazon.com/batch/latest/userguide/multi-node-parallel-jobs.html#mnp-ce) in the *AWS Batch User Guide*\. To configure a private subnet for your compute nodes, see [AWS ParallelCluster with AWS Batch scheduler](network-configuration-v3.md#network-configuration-v3-batch)\.