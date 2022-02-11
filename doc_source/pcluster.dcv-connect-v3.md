# `pcluster dcv-connect`<a name="pcluster.dcv-connect-v3"></a>

Permits to connect to the head node through an interactive session by using NICE DCV\.

```
pcluster dcv-connect [-h] [--debug] [--region REGION]
                     --cluster-name CLUSTER_NAME [--key-path KEY_PATH]
                     [--show-url]
```

## Named arguments<a name="pcluster-v3.dcv-connect.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster dcv-connect`\.

`--debug`  
Enables debug logging\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

`--cluster-name, -n CLUSTER_NAME`  
Specifies the name of the cluster\.

`--key-path KEY_PATH`  
Specifies the path of the SSH key to use for the connection\.

`--show-url`  
Prints the URL that would be used for the DCV connection and exits\.