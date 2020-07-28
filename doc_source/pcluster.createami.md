# `pcluster createami`<a name="pcluster.createami"></a>

\(Linux/macOS\) Creates a custom AMI to use with AWS ParallelCluster\.

```
pcluster createami [ -h ] -ai BASE_AMI_ID -os BASE_AMI_OS [ -i INSTANCE_TYPE ]
                   [ -ap CUSTOM_AMI_NAME_PREFIX ] [ -cc CUSTOM_AMI_COOKBOOK ]
                   [ -c CONFIG_FILE ] [ -r REGION ]
```

## Required dependencies<a name="pcluster.createami.required-dependencies"></a>

In addition to the AWS ParallelCluster CLI, the following dependency is required to run `pcluster createami`:
+ **Packer**: Download the latest version from [https://www.packer.io/downloads.html](https://www.packer.io/downloads.html)\.

**Note**  
Prior to AWS ParallelCluster 2\.8\.0, [Berkshelf](https://github.com/berkshelf/berkshelf) \(installed using `gem install berkshelf`\) was required to use `pcluster createami`\.

## Named arguments<a name="pcluster.createami.namedarg"></a>

`-h, --help`  
Shows the help text for the specified command\.

`-ai BASE_AMI_ID, --ami-id BASE_AMI_ID`  
Specifies the base AMI to use for building the AWS ParallelCluster AMI\.

`-os BASE_AMI_OS, --os BASE_AMI_OS`  
Specifies the OS of the base AMI\. Valid options are: `alinux`, `alinux2`, `ubuntu1604`, `ubuntu1804`, `centos6`, and `centos7`\.  
Support for `alinux2` was added in AWS ParallelCluster 2\.6\.0\. Support for `ubuntu1804` was added in AWS ParallelCluster 2\.5\.0\.

`-ap CUSTOM_AMI_NAME_PREFIX, --ami-name-prefix CUSTOM_AMI_NAME_PREFIX`  
Specifies the prefix name of the resulting AWS ParallelCluster AMI\.  
Defaults to `custom-ami-`\.

`-cc CUSTOM_AMI_COOKBOOK, --custom-cookbook CUSTOM_AMI_COOKBOOK`  
Specifies the cookbook to use to build the AWS ParallelCluster AMI\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the alternative configuration file to use\.  
Defaults to `~/.parallelcluster/config`\.

`-i INSTANCE_TYPE, --instance-type INSTANCE_TYPE`  
Specifies the instance type to use to create the AMI\.  
Defaults to `t2.xlarge`\.  
Support for the `--instance-type` argument was added in AWS ParallelCluster 2\.4\.1\.

`-r REGION, --region REGION`  
Specifies the Region to connect to\.