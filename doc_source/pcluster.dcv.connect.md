# `pcluster dcv connect`<a name="pcluster.dcv.connect"></a>

Open a browser and connect to the NICE DCV session running in the master instance\.
For more information about the NICE DCV integration, see [Connect to the master instance through NICE DCV](dcv.md)\.

```
pcluster dcv connect [ -h ] [ -k SSH_KEY_PATH ] cluster_name
```

## Positional Arguments<a name="pcluster.dcv.connect.arg"></a>

`cluster_name`  
Specifies the name of the cluster to connect to\.

## Named Arguments<a name="pcluster.dcv.connect.namedarg"></a>

`-h, --help`  
Shows the help text for the specified command\.

`-k, --key-path`  
Key path of the SSH key to use for the connection\.
It must be the one specified at cluster creation time in the `key_name` configuration parameter\.

Example:

```
$ pcluster dcv connect mycluster -k ~/.ssh/id_rsa
```

Opens the default browser and connect to the NICE DCV session running in the master instance\.
A new session is created if there is no one\.
