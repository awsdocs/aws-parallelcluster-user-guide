# AWS Batch \(`awsbatch`\)<a name="awsbatchcli-v3"></a>

For information about AWS Batch, see [AWS Batch](http://aws.amazon.com/batch/)\. For documentation, see the [AWS Batch User Guide](https://docs.aws.amazon.com/batch/latest/userguide/)\.

**AWS ParallelCluster CLI commands for AWS Batch**

**Important**  
When using AWS Batch, GPU jobs are not supported\.

When you use the `awsbatch` scheduler, the AWS ParallelCluster CLI commands for AWS Batch are automatically installed in the AWS ParallelCluster head node\. The CLI uses AWS Batch API operations and permits the following operations:
+ Submit and manage jobs\.
+ Monitor jobs, queues, and hosts\.
+ Mirror traditional scheduler commands\.

This CLI is distributed as a separate package\. For more information, see [Scheduler Support](moving-from-v2-to-v3.md#scheduler_support)\.

**Topics**
+ [`awsbsub`](awsbatchcli.awsbsub-v3.md)
+ [`awsbstat`](awsbatchcli.awsbstat-v3.md)
+ [`awsbout`](awsbatchcli.awsbout-v3.md)
+ [`awsbkill`](awsbatchcli.awsbkill-v3.md)
+ [`awsbqueues`](awsbatchcli.awsbqueues-v3.md)
+ [`awsbhosts`](awsbatchcli.awsbhosts-v3.md)