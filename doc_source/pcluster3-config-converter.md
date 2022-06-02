# `pcluster3-config-converter`<a name="pcluster3-config-converter"></a>

Reads a AWS ParallelCluster version 2 configuration file and writes a AWS ParallelCluster version 3 configuration file\.

```
pcluster3-config-converter [-h]                            
                [-t CLUSTER_TEMPLATE]
                [-c CONFIG_FILE]
                [--force-convert]
                [-o OUTPUT_FILE]
```

## Named arguments<a name="pcluster3-config-converter.named.arguments"></a>

`-h, --help`  
Shows the help text for `pcluster3-config-converter`\.

`-t CLUSTER_TEMPLATE, --cluster-template CLUSTER_TEMPLATE`  
Specifies the [`[cluster]` section](cluster-definition.md) of the configuration file to convert\. If not specified the script will look for the [`cluster_template`](global.md#cluster-template) parameter in the [`[global]` section](global.md) or will search for `[cluster default]`\.

`-c CONFIG_FILE, --config-file CONFIG_FILE`  
Specifies the AWS ParallelCluster version 2 configuration file to be read\.

`--force-convert`  
Enables a conversion even if one or more settings is not supported and not recommended\.

`-o OUTPUT_FILE, --output-file OUTPUT_FILE`  
Specifies the AWS ParallelCluster version 3 configuration file to be written\. If this parameter is not specified, the configuration is written to stdout\.

**Note**  
The `pcluster3-config-converter` command was added in AWS ParallelCluster version 3\.0\.1\.