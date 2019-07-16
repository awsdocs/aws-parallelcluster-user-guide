# `pcluster createami`<a name="pcluster.createami"></a>

\(Linux/macOS\) Creates a custom AMI to use with AWS ParallelCluster\.

```
pcluster createami [ -h ] - ai BASE_AMI_ID - os BASE_AMI_OS [ -ap CUSTOM_AMI_NAME_PREFIX ]
                   [ -cc CUSTOM_AMI_COOKBOOK ] [ -c CONFIG_FILE ] [ -r REGION ]
```

## Named Arguments<a name="pcluster.createami.namedarg"></a>

`-h, --help`  
Shows the help text for the specified command\.

`-ai BASE_AMI_ID, --ami-id BASE_AMI_ID`  
Specifies the base AMI to use for building the AWS ParallelCluster AMI\.

`-os BASE_AMI_OS, --os BASE_AMI_OS`  
Specifies the OS of the base AMI\. Valid options are: `alinux`, `ubuntu1404`, `ubuntu1604`, `centos6`, and `centos7`\.

`-ap CUSTOM_AMI_NAME_PREFIX, --ami-name-prefix CUSTOM_AMI_NAME_PREFIX`  
Specifies the prefix name of the resulting AWS ParallelCluster AMI\.  
Defaults to `custom-ami-`\.

`-cc CUSTOM_AMI_COOKBOOK, --custom-cookbook CUSTOM_AMI_COOKBOOK`  
Specifies the cookbook to use to build the AWS ParallelCluster AMI\.

`-c CONFIG_FILE, --config CONFIG_FILE`  
Specifies the full path of the alternative configuration file to use\.  
Defaults to `~/.parallelcluster/config`\.

`-r REGION, --region REGION`  
Specifies the Region to connect to\.