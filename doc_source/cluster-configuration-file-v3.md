# Cluster configuration file<a name="cluster-configuration-file-v3"></a>

AWS ParallelCluster version 3 uses separate configuration files to control the definition of cluster infrastructure and the definition of custom AMIs\. All configuration files use YAML 1\.1 files\. Detailed information for each of these configuration files is linked below\. For some example configurations, see [https://github\.com/aws/aws\-parallelcluster/tree/release\-3\.0/cli/tests/pcluster/example\_configs](https://github.com/aws/aws-parallelcluster/tree/release-3.0/cli/tests/pcluster/example_configs)\.

These objects are used for the AWS ParallelCluster version 3 cluster configuration\.

**Topics**
+ [Cluster configuration file properties](#cluster-configuration-file-v3.properties)
+ [`Image` section](Image-v3.md)
+ [`HeadNode` section](HeadNode-v3.md)
+ [`Scheduling` section](Scheduling-v3.md)
+ [`SharedStorage` section](SharedStorage-v3.md)
+ [`Iam` section](Iam-v3.md)
+ [`Monitoring` section](Monitoring-v3.md)
+ [`Tags` section](Tags-v3.md)
+ [`AdditionalPackages` section](AdditionalPackages-v3.md)

## Cluster configuration file properties<a name="cluster-configuration-file-v3.properties"></a>

`Region` \(**Optional**, `String`\)  
Specifies the Region for the cluster\. For example, `us-east-2`\.  
[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update-cluster-v3.md#update-policy-fail-v3)

`CustomS3Bucket` \(**Optional**, `String`\)  
Specifies the name of an existing Amazon S3 bucket to be used by the cluster\. By default, AWS ParallelCluster will create a new bucket named `parallelcluster-hash-v1-DO-NOT-DELETE`\.  
[Update policy: If this setting is changed, the update is not allowed. If you force the update, the new value will be ignored and the old value will be used.](using-pcluster-update-cluster-v3.md#update-policy-read-only-resource-bucket-v3)

`AdditionalResources` \(**Optional**, `String`\)  
Defines an additional AWS CloudFormation template to launch along with the cluster\. This additional template is used for creating resources that are outside of the cluster but are part of the cluster's lifecycle\.  
The value must be an HTTPS URL to a public template, with all parameters provided\.  
There is no default value\.  
[Update policy: This setting can be changed during an update.](using-pcluster-update-cluster-v3.md#update-policy-setting-supported-v3)