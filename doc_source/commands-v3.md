# AWS ParallelCluster CLI commands<a name="commands-v3"></a>

`pcluster` is the primary AWS ParallelCluster CLI command\. You use `pcluster` to launch and manage HPC clusters in the AWS Cloud and to create and manage custom AMI images\.

`pcluster3-config-converter` is used to convert cluster configurations in AWS ParallelCluster version 2 format into AWS ParallelCluster version 3 format\.

```
pcluster [-h] ( build-image | configure | 
                create-cluster | dcv-connect | 
                delete-cluster | delete-cluster-instances | delete-image |
                describe-cluster | describe-cluster-instances | 
                describe-compute-fleet | describe-image |
                export-cluster-logs | export-image-logs | 
                get-cluster-log-events | get-cluster-stack-events | 
                get-image-log-events | get-image-stack-events |
                list-cluster-log-streams | list-clusters | 
                list-images | list-image-log-streams | list-official-images |
                ssh | update-cluster | 
                update-compute-fleet | version ) ...
pcluster3-config-converter [-h] [-t CLUSTER_TEMPLATE]
                [-c CONFIG_FILE]
                [--force-convert]
                [-o OUTPUT_FILE]
```

**Topics**
+ [`pcluster`](pcluster-v3.md)
+ [`pcluster3-config-converter`](pcluster3-config-converter.md)