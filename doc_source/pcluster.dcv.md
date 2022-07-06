# `pcluster dcv`<a name="pcluster.dcv"></a>

Interacts with the NICE DCV server running on the head node\.

```
pcluster dcv [ -h ] ( connect )
```

**`pcluster dcv command`**  
Possible choices: [`connect`](#pcluster-dcv-connect)

**Note**  
OS support changes for the `pcluster dcv` command in different AWS ParallelCluster versions:  
Support for the `pcluster dcv` command on `centos8` was added in AWS ParallelCluster version 2\.10\.0\.
Support for the `pcluster dcv` command on AWS Graviton\-based instances was added in AWS ParallelCluster version 2\.9\.0\.
Support for the `pcluster dcv` command on `ubuntu1804` was added in AWS ParallelCluster version 2\.6\.0\.
Support for the `pcluster dcv` command on `centos7` was added in AWS ParallelCluster version 2\.5\.0\.

## Named arguments<a name="pcluster.dcv.namedarg"></a>

`-h, --help`  
Shows the help text for `pcluster dcv`\.

## Sub\-commands<a name="pcluster-dcv-subcommands"></a>

### `pcluster dcv connect`<a name="pcluster-dcv-connect"></a>

```
pcluster dcv connect [ -h ] [ -k SSH_KEY_PATH ] [ -r REGION ] cluster_name
```

**Important**  
The URL expires 30 seconds after it's issued\. If the connection isn't made before the URL expires, run `pcluster dcv connect` again to generate a new URL\.

#### Positional arguments<a name="pcluster.dcv.connect.arg"></a>

`cluster_name`  
Specifies the name of the cluster to connect to\.

#### Named arguments<a name="pcluster.dcv.connect.namedarg"></a>

`-h, --help`  
Shows the help text for `pcluster dcv connect`\.

`-k SSH_KEY_PATH, --key-path SSH_KEY_PATH`  
Key path of the SSH key to use for the connection\.  
The key must be the one specified at cluster creation time in the [`key_name`](cluster-definition.md#key-name) configuration parameter\. This argument is optional, but if it's not specified, then the key must be available by default for the SSH client\. For example, add it to the `ssh-agent` with `ssh-add`\.

`-r REGION, --region REGION`  
Specifies the AWS Region to use\. Defaults to the Region specified by using the [`pcluster configure`](pcluster.configure.md) command\.

`-s, --show-url`  
Displays a one\-time URL for connecting to the NICE DCV session\. The default browser isn't opened when this option is specified\.  
Support for the `--show-url` argument was added in AWS ParallelCluster version 2\.5\.1\.

Example:

```
$ pcluster dcv connect -k ~/.ssh/id_rsa
```

Opens the default browser to connect to the NICE DCV session running on the head node\.

A new NICE DCV session is created if one isn't already started\.