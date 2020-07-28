# Moving from CfnCluster to AWS ParallelCluster<a name="moving-from-cfncluster-to-aws-parallelcluster"></a>

AWS ParallelCluster is an enhanced version of CfnCluster\.

If you currently use CfnCluster, we encourage you to start using and creating new clusters with AWS ParallelCluster instead\. Although you can continue to use CfnCluster, it is no longer being developed, and no new features or functionality will be added\.

The main differences between CfnCluster and AWS ParallelCluster are described in the following sections\.

**AWS ParallelCluster CLI manages a different set of clusters** 

Clusters created with the `cfncluster` CLI cannot be managed with the `pcluster` CLI\. The following commands do not work on clusters created by CfnCluster:

```
pcluster list
pcluster update cluster_name
pcluster start cluster_name
pcluster status cluster_name
```

To manage clusters that you created with CfnCluster, you must use the `cfncluster` CLI\.

If you need a CfnCluster package to manage your old clusters, we recommend that you install and use it from a [Python Virtual Environment](https://docs.python.org/3/tutorial/venv.html)\.

 **AWS ParallelCluster and CfnCluster use different IAM custom policies** 

Custom IAM policies that were previously used for CfnCluster cluster creation cannot be used with AWS ParallelCluster\. If you require custom policies for AWS ParallelCluster, you must create new ones\. See the AWS ParallelCluster guide\.

 **AWS ParallelCluster and CfnCluster use different configuration files** 

The AWS ParallelCluster configuration file resides in the `~/.parallelcluster` folder\. The CfnCluster configuration file resides in the `~/.cfncluster` folder\.

If you want to use an existing CfnCluster configuration file with AWS ParallelCluster, you must:

1. Move the configuration file from `~/.cfncluster/config` to `~/.parallelcluster/config`\.

1. If you use the [`extra_json`](https://docs.aws.amazon.com/parallelcluster/latest/ug/cluster-definition.html#extra-json) configuration parameter, change it as shown\.

CfnCluster setting:

```
extra_json = { "cfncluster" : { } }
```

AWS ParallelCluster setting:

```
extra_json = { "cluster" : { } }
```

 **In AWS ParallelCluster, ganglia is disabled by default** 

In AWS ParallelCluster, ganglia is disabled by default\. To enable ganglia:

1. Set the [`extra_json`](https://docs.aws.amazon.com/parallelcluster/latest/ug/cluster-definition.html#extra-json) parameter as shown:

   ```
   extra_json = { "cluster" : { "ganglia_enabled" : "yes" } }
   ```

1. Change the master security group to allow connections to port 80\.

   The `parallelcluster-<CLUSTER_NAME>-MasterSecurityGroup-<xxx>` security group must be modified by adding a new security group rule to allow Inbound connection to port 80 from your Public IP\. For more information, see [Adding Rules to a Security Group](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html#adding-security-group-rule) in the *Amazon EC2 User Guide for Linux Instances*\.
