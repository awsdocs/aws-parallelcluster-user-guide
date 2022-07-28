# `pcluster list-official-images`<a name="pcluster.list-official-images-v3"></a>

Describe official AWS ParallelCluster AMIs\.

```
pcluster list-official-images [-h]
                [--architecture ARCHITECTURE]
                [--debug]
                [--os OS]
                [--query QUERY]
                [--region REGION]
```

## Named arguments<a name="pcluster-v3.list-official-images.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster list-official-images`\.

`--architecture ARCHITECTURE`  
Specifies the architecture to use to filter the results\. If this parameter is not specified, all architectures are returned\.

`--debug`  
Enables debug logging\.

`--os OS`  
Specifies the operating system to use to filter the results\. If this parameter is not specified, all operating systems are returned\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The Region must be specified, using the [Region](image-builder-configuration-file-v3.md#yaml-build-image-Region) setting in the image configuration file, the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.