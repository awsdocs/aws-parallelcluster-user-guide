# EC2 console output logs<a name="console-logs-v3"></a>

When AWS ParallelCluster detects that a static compute node instance terminates unexpectedly, it attempts to retrieve the EC2 console output from the terminated node instance after a period of time elapses\. This way, if the compute node was unable to communicate with Amazon CloudWatch, useful troubleshooting information on why the node terminated might still be retrieved from the console output\. This console output is recorded in the `/var/log/parallelcluster/compute_console_output` log on the head node\. For more information about the EC2 console output, see [Instance console output](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-console.html#instance-console-console-output) in the *Amazon EC2 User Guide for Linux Instances*\.

By default, AWS ParallelCluster only retrieves the console output from a sample subset of terminated nodes\. This prevents the cluster head node from being overwhelmed with multiple console output requests caused by large numbers of terminations\. By default, AWS ParallelCluster waits 5 minutes between termination detection and console output retrieval to give EC2 time to retrieve the final console output from the nodes\.

You can edit the sample size and wait time parameter values in the `/etc/parallelcluster/slurm_plugin/parallelcluster_clustermgtd.conf` file on the head node\.

This feature is added in AWS ParallelCluster version 3\.5\.0\.

## EC2 console output parameters<a name="console-logs-parameters-v3"></a>

You can edit the values of the following EC2 console output parameters in the `/etc/parallelcluster/slurm_plugin/parallelcluster_clustermgtd.conf` file on the head node\.

### `compute_console_logging_enabled`<a name="console-logs-enable-v3"></a>

To disable console output log collection, set `compute_console_logging_enabled` to `false`\. The default is `true`\.

You can update this parameter at any time, without stopping the compute fleet\.

### `compute_console_logging_max_sample_size`<a name="console-logs-max-sample-size-v3"></a>

`compute_console_logging_max_sample_size` sets the maximum number of compute nodes from which AWS ParallelCluster collects console outputs each time it detects an unexpected termination\. If this value is less than `1`, AWS ParallelCluster retrieves the console output from all terminated nodes\. The default value is `1`\.

You can update this parameter at any time, without stopping the compute fleet\.

### `compute_console_wait_time`<a name="console-logs-wait-time-v3"></a>

`compute_console_wait_time` sets the time, in seconds, that AWS ParallelCluster waits between detecting a node failure and collecting the console output from that node\. You can increase the wait time if you determine that EC2 needs more time to collect the final output from the terminated node\. The default value is 300 seconds \(5 minutes\)\.

You can update this parameter at any time, without stopping the compute fleet\.