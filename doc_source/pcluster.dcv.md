# `pcluster dcv`<a name="pcluster.dcv"></a>

Interacts with the NICE DCV server running on the master instance\.

```
pcluster dcv [ -h ] ( connect )
```

`pcluster dcv command`  
Possible choices: `[connect](#pcluster-dcv-connect)`

**Note**  
Support for the `pcluster dcv` command was added in AWS ParallelCluster 2\.5\.0\.

## Named Arguments<a name="pcluster.dcv.namedarg"></a>

`-h, --help`  
Shows the help text for the specified command\.

## Sub\-commands<a name="pcluster-dcv-subcommands"></a>

### `pcluster dcv connect`<a name="pcluster-dcv-connect"></a>

```
pcluster dcv connect [ -h ] [ -k SSH_KEY_PATH ] cluster_name
```

#### Positional Arguments<a name="pcluster.dcv.connect.arg"></a>

`cluster_name`  
Specifies the name of the cluster to connect to\.

#### Named Arguments<a name="pcluster.dcv.connect.namedarg"></a>

`-h, --help`  
Shows the help text for the specified command\.

`-k SSH_KEY_PATH, --key-path SSH_KEY_PATH`  
Key path of the SSH key to use for the connection\.  
It must be the one specified at cluster creation time in the `key_name` configuration parameter\.

Example:

```
$ pcluster dcv connect -k ~/.ssh/id_rsa
```

Opens the default browser to connect to the NICE DCV session running on the master instance\.

A new NICE DCV session is created if one is not already started\.