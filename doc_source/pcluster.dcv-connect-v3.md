# `pcluster dcv-connect`<a name="pcluster.dcv-connect-v3"></a>

Permits to connect to the head node through an interactive session by using NICE DCV\.

```
pcluster dcv-connect [-h]
                 --cluster-name CLUSTER_NAME 
                [--debug]
                [--key-path KEY_PATH]
                [--region REGION]
                [--show-url]
```

## Named arguments<a name="pcluster-v3.dcv-connect.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster dcv-connect`\.

`--cluster-name, -n CLUSTER_NAME`  
Specifies the name of the cluster\.

`--debug`  
Enables debug logging\.

`--key-path KEY_PATH`  
Specifies the path of the SSH key to use for the connection\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

`--show-url`  
Prints the URL that would be used for the DCV connection and exits\.

**Example using AWS ParallelCluster version 3\.1\.4:**

```
$ pcluster dcv-connect -n cluster-3Dcv -r us-east-1 --key-path /home/user/.ssh/key.pem
```

 ![\[A screenshot of the DCV console.\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/Dcv-image.png) 