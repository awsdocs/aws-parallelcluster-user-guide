# `pcluster update-cluster`<a name="pcluster.update-cluster-v3"></a>

Updates an existing cluster to match the settings of a specified configuration file\.

```
pcluster update-cluster [-h] --cluster-name CLUSTER_NAME
                        [--suppress-validators SUPPRESS_VALIDATORS [SUPPRESS_VALIDATORS ...]]
                        [--validation-failure-level {INFO,WARNING,ERROR}]
                        [--region REGION] [--dryrun DRYRUN]
                        [--force-update FORCE_UPDATE]
                        --cluster-configuration CLUSTER_CONFIGURATION
                        [--debug] [--query QUERY]
```

## Named arguments<a name="pcluster-v3.update-cluster.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster update-cluster`\.

`--cluster-name, -n CLUSTER_NAME`  
Specifies the name of the cluster\.

`--suppress-validators SUPPRESS_VALIDATORS [SUPPRESS_VALIDATORS ...]`  
Identifies one or more config validators to suppress\.  
Format: \(`ALL`\|`type:[A-Za-z0-9]+`\)

`--region, -r REGION`  
Specifies the AWS Region to use\. The Region must be specified, using the [`Region`](cluster-configuration-file-v3.md#yaml-Region) setting in the cluster configuration file, the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

`--dryrun DRYRUN`  
Only perform the requested validation without creating any resources\. It can be used to validate the image configuration and update requirements\. \(Defaults to `false`\.\)

`--force-update FORCE_UPDATE`  
Forces the update by ignoring the update validation errors\. \(Defaults to `false`\.\)

`--cluster-configuration, -c CLUSTER_CONFIGURATION`  
Specifies the YAML cluster configuration file\.

`--debug`  
Enables debug logging\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.