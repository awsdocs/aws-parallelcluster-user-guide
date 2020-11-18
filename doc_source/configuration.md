# Configuration<a name="configuration"></a>

**Topics**
+ [Layout](#layout)
+ [`[global]` section](global.md)
+ [`[aws]` section](aws.md)
+ [`[aliases]` section](aliases.md)
+ [`[cluster]` section](cluster-definition.md)
+ [`[compute_resource]` section](compute-resource-section.md)
+ [`[cw_log]` section](cw-log-section.md)
+ [`[dashboard]` section](dashboard-section.md)
+ [`[dcv]` section](dcv-section.md)
+ [`[ebs]` section](ebs-section.md)
+ [`[efs]` section](efs-section.md)
+ [`[fsx]` section](fsx-section.md)
+ [`[queue]` section](queue-section.md)
+ [`[raid]` section](raid-section.md)
+ [`[scaling]` section](scaling-section.md)
+ [`[vpc]` section](vpc-section.md)
+ [Example](examples.md)

By default, AWS ParallelCluster uses the `~/.parallelcluster/config` file for all configuration parameters\. You can specify a custom configuration file by using the `-c` or `--config` command line option or the `AWS_PCLUSTER_CONFIG_FILE` environment variable\.

An example configuration file is installed with AWS ParallelCluster in the Python directory at `site-packages/aws-parallelcluster/examples/config`\. The example configuration file is also available on GitHub, at [https://github.com/aws/aws-parallelcluster/blob/v2.10/cli/pcluster/examples/config](https://github.com/aws/aws-parallelcluster/blob/v2.10/cli/pcluster/examples/config)\.

## Layout<a name="layout"></a>

An AWS ParallelCluster configuration is defined in multiple sections\.

The following sections are required: [`[global]` section](global.md) and [`[aws]` section](aws.md)\.

You also must include at least one [`[cluster]` section](cluster-definition.md) and one [`[vpc]` section](vpc-section.md)\.

A section starts with the section name in brackets, followed by parameters and configuration\.

```
[global]
cluster_template = default
update_check = true
sanity_check = true
```