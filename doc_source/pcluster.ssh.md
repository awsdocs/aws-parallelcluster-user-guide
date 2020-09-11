# `pcluster ssh`<a name="pcluster.ssh"></a>

Runs an `ssh` command with the user name and IP address of the cluster pre\-populated\. Arbitrary arguments are appended to the end of the `ssh` command\. This command can be customized in the aliases section of the configuration file\.

```
pcluster ssh [ -h ] [ -d ] cluster_name
```

## Positional arguments<a name="pcluster.ssh.arg"></a>

`cluster_name`  
Specifies the name of the cluster to connect to\.

## Named arguments<a name="pcluster.ssh.namedarg"></a>

`-h, --help`  
Shows the help text for `pcluster ssh`\.

`-d, --dryrun`  
Prints the command that would be run and exits\.  
Defaults to `False`\.

Example:

```
$ pcluster ssh -d mycluster -i ~/.ssh/id_rsa
```

Returns an `ssh` command with the user name and IP address of the cluster pre\-populated:

```
$ ssh ec2-user@1.1.1.1 -i ~/.ssh/id_rsa
```

The `ssh` command is defined in the global configuration file under the [`[aliases]` section](aliases.md)\. It can be customized as follows\.

```
[ aliases ]
ssh = ssh {CFN_USER}@{MASTER_IP} {ARGS}
```

Variables substituted:

`CFN_USER`  
The user name for the [`base_os`](cluster-definition.md#base-os) that is selected\.

`MASTER_IP`  
The IP address of the head node\.

`ARGS`  
Optional arguments to pass to the `ssh` command\.