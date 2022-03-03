# AWS ParallelCluster CLI commands<a name="commands-v3"></a>

`pcluster` is the primary AWS ParallelCluster CLI command\. You use `pcluster` to launch and manage HPC clusters in the AWS Cloud and to create and manage custom AMI images\.

`pcluster3-config-converter` is used to convert cluster configurations in AWS ParallelCluster version 2 format into AWS ParallelCluster version 3 format\.

```
pcluster [ -h ] ( list-clusters | create-cluster | delete-cluster | describe-cluster |
                   update-cluster | describe-compute-fleet | update-compute-fleet |
                   delete-cluster-instances | describe-cluster-instances |
                   list-cluster-log-streams | get-cluster-log-events | get-cluster-stack-events |
                   list-images | build-image | delete-image | describe-image |
                   list-image-log-streams | get-image-log-events | get-image-stack-events |
                   list-official-images | configure | dcv-connect | export-cluster-logs |
                   export-image-logs | ssh | version ) ...
pcluster3-config-converter [-h] [-c CONFIG_FILE] [-t CLUSTER_TEMPLATE]
                                  [-o OUTPUT_FILE] [--force-convert]
```

**Topics**
+ [`pcluster`](pcluster-v3.md)
+ [`pcluster3-config-converter`](pcluster3-config-converter.md)