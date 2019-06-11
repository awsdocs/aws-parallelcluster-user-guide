# Configuration<a name="configuration"></a>

**Topics**
+ [Layout](#layout)
+ [[global] section](global.md)
+ [[aws] section](aws.md)
+ [[aliases] section](aliases.md)
+ [[cluster] section](cluster-definition.md)
+ [[ebs] section](ebs-section.md)
+ [[efs] section](efs-section.md)
+ [[fsx] section](fsx-section.md)
+ [[raid] section](raid-section.md)
+ [[scaling] section](scaling-section.md)
+ [[vpc] section](vpc-section.md)
+ [Example](examples.md)

By default, AWS ParallelCluster uses the file `~/.parallelcluster/config` for all configuration parameters\.

An example configuration file is installed with AWS ParallelCluster in the Python directory at `site-packages/aws-parallelcluster/examples/config`\. The example configuration file is also available on GitHub, at [https://github.com/aws/aws-parallelcluster/blob/release/cli/pcluster/examples/config](https://github.com/aws/aws-parallelcluster/blob/release/cli/pcluster/examples/config)\.

## Layout<a name="layout"></a>

An AWS ParallelCluster configuration is defined in multiple sections\.

The following sections are required: `[[global] section](global.md)` and `[[aws] section](aws.md)`\.

You also must include at least one `[[cluster] section](cluster-definition.md)` and one `[[vpc] section](vpc-section.md)`\.

A section starts with the section name in brackets, followed by parameters and configuration\.

```
[global]
cluster_template = default
update_check = true
sanity_check = true
```