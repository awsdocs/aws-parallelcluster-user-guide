# `pcluster dcv`<a name="pcluster.dcv"></a>

Interacts with the NICE DCV server running on the master instance\.

```
pcluster dcv [ -h ] ( connect )
```

`pcluster dcv command`  
Possible choices: `connect`

**Note**  
Support for the `pcluster dcv` command on `ubuntu1804` was added in AWS ParallelCluster 2\.6\.0\. Support for the `pcluster dcv` command on `centos7` was added in AWS ParallelCluster 2\.5\.0\.

## Named Arguments<a name="pcluster.dcv.namedarg"></a>

`-h, --help`  
Shows the help text for the specified command\.

## Sub\-commands<a name="pcluster-dcv-subcommands"></a>

### `pcluster dcv connect`<a name="pcluster-dcv-connect"></a>

```
pcluster dcv connect [ -h ] [ -k SSH_KEY_PATH ] cluster_name
```

**Important**  
The URL expires 30 seconds after it is issued\. If the connection is not made before the URL expires, run `pcluster dcv connect` again to generate a new URL\.

#### Positional Arguments<a name="pcluster.dcv.connect.arg"></a>

`cluster_name`  
Specifies the name of the cluster to connect to\.

#### Named Arguments<a name="pcluster.dcv.connect.namedarg"></a>

`-h, --help`  
Shows the help text for the specified command\.

`-k SSH_KEY_PATH, --key-path SSH_KEY_PATH`  
Key path of the SSH key to use for the connection\.  
The key must be the one specified at cluster creation time in the `` configuration parameter\. This argument is optional, but if it is not specified, then the key must be available by default for the SSH client\. For example, add it to the `ssh-agent` with `ssh-add`\.

`-s, --show-url`  
Displays a one\-time URL for connecting to the NICE DCV session\. The default browser is not opened when this option is specified\.  
Support for the `--show-url` argument was added in AWS ParallelCluster 2\.5\.1\.

Example:

```
$ pcluster dcv connect -k ~/.ssh/id_rsa
```

Opens the default browser to connect to the NICE DCV session running on the master instance\.

A new NICE DCV session is created if one is not already started\.