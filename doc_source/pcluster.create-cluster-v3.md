# `pcluster create-cluster`<a name="pcluster.create-cluster-v3"></a>

Creates an AWS ParallelCluster cluster\.

```
pcluster create-cluster [-h]
                 --cluster-configuration CLUSTER_CONFIGURATION
                 --cluster-name CLUSTER_NAME
                [--debug]
                [--dryrun DRYRUN]   
                [--query QUERY]
                [--region REGION]
                [--rollback-on-failure ROLLBACK_ON_FAILURE]
                [--suppress-validators SUPPRESS_VALIDATORS [SUPPRESS_VALIDATORS ...]]                        
                [--validation-failure-level {INFO,WARNING,ERROR}]
```

## Named arguments<a name="pcluster-v3.create-cluster.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster create-cluster`\.

`--cluster-configuration, -c CLUSTER_CONFIGURATION`  
Specifies the YAML cluster configuration file\.

`--cluster-name, -n CLUSTER_NAME`  
Specifies the name of the cluster to be created\.  
The name must start with an alphabetical character\. The name can have up to 60 characters\. If Slurm accounting is enabled, the name can have up to 40 characters\.  
Valid characters: a\-z, A\-Z, 0\-9, and \- \(hyphen\)\.

`--debug`  
Enables debug logging\.

`--dryrun DRYRUN`  
When `true`, the command performs validation without creating any resources\. You can use this to validate the cluster configuration\. \(Defaults to `false`\.\)

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The AWS Region must be specified, using the [`Region`](cluster-configuration-file-v3.md#yaml-Region) setting in the cluster configuration file, the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

`--rollback-on-failure ROLLBACK_ON_FAILURE`  
When `true`, automatically initiates a cluster stack rollback on failures\. \(Defaults to `true`\.\)

`--suppress-validators SUPPRESS_VALIDATORS [SUPPRESS_VALIDATORS ...]`  
Identifies one or more config validators to suppress\.  
 Format: \(`ALL`\|type:`[A-Za-z0-9]+`\)

`--validation-failure-level {INFO,WARNING,ERROR}`  
Specifies the minimum validation level that will cause the creation to fail\. \(Defaults to `ERROR`\.\)

**Example using AWS ParallelCluster version 3\.1\.4:**

```
$ pcluster create-cluster -c cluster-config.yaml -n cluster-v3
{
  "cluster": {
    "clusterName": "cluster-v3",
    "cloudformationStackStatus": "CREATE_IN_PROGRESS",
    "cloudformationStackArn": "arn:aws:cloudformation:us-east-1:123456789012:stack/cluster-v3/1234abcd-56ef-78gh-90ij-abcd1234efgh",
    "region": "us-east-1",
    "version": "3.1.4",
    "clusterStatus": "CREATE_IN_PROGRESS"
  }
}
```