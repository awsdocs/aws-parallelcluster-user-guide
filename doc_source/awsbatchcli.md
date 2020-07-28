# AWS Batch \(`awsbatch`\)<a name="awsbatchcli"></a>

For information about AWS Batch, see [AWS Batch](http://aws.amazon.com/batch/)\. For documentation, see the [AWS Batch User Guide](https://docs.aws.amazon.com/batch/latest/userguide/)\.

**AWS ParallelCluster CLI commands for AWS Batch**

**Important**  
When using AWS Batch, GPU jobs are not supported\.

When you use the `awsbatch` scheduler, the AWS ParallelCluster CLI commands for AWS Batch are automatically installed in the AWS ParallelCluster master node\. The CLI uses AWS Batch APIs and permits to:
+ Submit and manage jobs\.
+ Monitor jobs, queues, and hosts\.
+ Mirror traditional scheduler commands\.

**Topics**
+ [`awsbsub`](awsbatchcli.awsbsub.md)
+ [`awsbstat`](awsbatchcli.awsbstat.md)
+ [`awsbout`](awsbatchcli_awsbout.md)
+ [`awsbkill`](awsbatchcli_awsbkill.md)
+ [`awsbqueues`](awsbatchcli_awsbqueues.md)
+ [`awsbhosts`](awsbatchcli_awsbhosts.md)