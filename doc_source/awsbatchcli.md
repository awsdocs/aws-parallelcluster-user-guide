# AWS Batch \(`awsbatch`\)<a name="awsbatchcli"></a>

For information about AWS Batch, see [AWS Batch](http://aws.amazon.com/batch/)\. For documentation, see the [AWS Batch User Guide](https://docs.aws.amazon.com/batch/latest/userguide/)\.

**AWS ParallelCluster CLI commands for AWS Batch**

When you use the `awsbatch` scheduler, the AWS ParallelCluster CLI commands for AWS Batch are automatically installed in the AWS ParallelCluster head node\. The CLI uses AWS Batch API operations and permits the following operations:
+ Submit and manage jobs\.
+ Monitor jobs, queues, and hosts\.
+ Mirror traditional scheduler commands\.

**Important**  
AWS ParallelCluster doesn't support GPU jobs for AWS Batch\. For more information, see [GPU jobs](https://docs.aws.amazon.com/batch/latest/userguide/gpu-jobs.html)\.

**Topics**
+ [`awsbsub`](awsbatchcli.awsbsub.md)
+ [`awsbstat`](awsbatchcli.awsbstat.md)
+ [`awsbout`](awsbatchcli_awsbout.md)
+ [`awsbkill`](awsbatchcli_awsbkill.md)
+ [`awsbqueues`](awsbatchcli_awsbqueues.md)
+ [`awsbhosts`](awsbatchcli_awsbhosts.md)