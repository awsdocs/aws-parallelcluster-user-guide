# `pcluster ssh`<a name="pcluster.ssh-v3"></a>

Runs a `ssh` command with the cluster user name and IP address pre\-populated\. Arbitrary arguments are appended to the end of the `ssh` command line\.

```
pcluster ssh [-h]
                 --cluster-name CLUSTER_NAME
                [--debug]
                [--dryrun DRYRUN]
                [--region REGION]
```

## Named arguments<a name="pcluster-v3.ssh.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster ssh`\.

`--cluster-name, -n CLUSTER_NAME`  
Specifies the name of the cluster to connect to\.

`--debug`  
Enables debug logging\.

`--dryrun DRYRUN`  
When `true`, prints the command line that would be run and exits\. \(Defaults to `false`\.\)

`--region, -r REGION`  
Specifies the AWS Region to use\. The AWS Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

Example:

```
$ pcluster ssh --cluster-name mycluster -i ~/.ssh/id_rsa
```

Runs an `ssh` command with the user name and IP address of the cluster pre\-populated:

```
ssh ec2-user@1.1.1.1 -i ~/.ssh/id_rsa
```