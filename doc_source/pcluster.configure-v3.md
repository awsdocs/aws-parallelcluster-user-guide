# `pcluster configure`<a name="pcluster.configure-v3"></a>

Begins an interactive configuration wizard for AWS ParallelCluster version 3\.

```
pcluster configure [-h]
                 --config CONFIG
                [--debug]
                [--region REGION]
```

## Named arguments<a name="pcluster-v3.configure.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster configure`\.

`--config CONFIG`  
Path to output the generated config file\.

`--debug`  
Turn on debug logging\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The Region must be specified, using the [Region](image-builder-configuration-file-v3.md#yaml-build-image-Region) setting in the image configuration file, the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.