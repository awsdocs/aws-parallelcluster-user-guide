# `pcluster ssh`<a name="pcluster.ssh-v3"></a>

Runs a `ssh` command with the cluster user name and IP address pre\-populated\. Arbitrary arguments are appended to the end of the `ssh` command line\.

```
pcluster ssh [-h] [--debug] [--region REGION]
             --cluster-name CLUSTER_NAME [--dryrun DRYRUN]
```

## Named arguments<a name="pcluster-v3.ssh.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster ssh`\.

`--region REGION`  
Specifies the AWS Region to use\. The Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

`--cluster-name CLUSTER_NAME`  
Specifies the name of the cluster to connect to\.

`--dryrun DRYRUN`  
Prints the command line that would be run and exits\.

Example:

```
$ pcluster ssh --cluster-name mycluster -i ~/.ssh/id_rsa
```

Runs an `ssh` command with the user name and IP address of the cluster pre\-populated:

```
ssh ec2-user@1.1.1.1 -i ~/.ssh/id_rsa
```