# AWS ParallelCluster CLI commands<a name="commands"></a>

`pcluster` and `pcluster-config` are the AWS ParallelCluster CLI commands\. You use `pcluster` to launch and manage HPC clusters in the AWS Cloud and `pcluster-config` to update your configuration\.

To use `pcluster`, you must have an IAM role with the [permissions](iam.md#example-parallelcluser-policies) required to run it\.

```
pcluster [ -h ] ( create | update | delete | start | stop | status | list |
                  instances | ssh | dcv | createami | configure | version ) ...
 pcluster-config [-h] (convert) ...
```

**Topics**
+ [`pcluster`](pcluster.md)
+ [`pcluster-config`](pcluster-config.md)