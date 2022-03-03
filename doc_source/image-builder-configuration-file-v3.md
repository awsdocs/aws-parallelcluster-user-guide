# Build image configuration files<a name="image-builder-configuration-file-v3"></a>

AWS ParallelCluster version 3 uses YAML 1\.1 files for build image configuration parameters\. Please confirm that indentation is correct to reduce configuration errors\. For more information, see the YAML 1\.1 spec at [https://yaml.org/spec/1.1/](https://yaml.org/spec/1.1/)\.

These configuration files are used to define how your custom AWS ParallelCluster AMIs are built using EC2 Image Builder\. Custom AMI building processes are triggered using the [`pcluster build-image`](pcluster.build-image-v3.md) command\. For some example configuration files, see [https://github\.com/aws/aws\-parallelcluster/tree/release\-3\.0/cli/tests/pcluster/schemas/test\_imagebuilder\_schema/test\_imagebuilder\_schema](https://github.com/aws/aws-parallelcluster/tree/release-3.0/cli/tests/pcluster/schemas/test_imagebuilder_schema/test_imagebuilder_schema)\.

**Topics**
+ [Build image configuration file properties](#build-image-v3.properties)
+ [`Build` section](Build-v3.md)
+ [`Image` section](build-Image-v3.md)

## Build image configuration file properties<a name="build-image-v3.properties"></a>

`Region` \(**Optional**, `String`\)  
Specifies the Region for the `build-image` operation\. For example, `us-east-2`\.