# `pcluster version`<a name="pcluster.version"></a>

Displays the AWS ParallelCluster version\.

```
pcluster version [ -h ]
```

For command\-specific flags, run: `pcluster [command] –-help`\.

## Named arguments<a name="pcluster.version.named.arguments"></a>

`-h, --help`  
Shows the help text for `pcluster version`\.

When the command is called and begins polling for the status of that call, it's safe to use "Ctrl\-C" to exit\. You can return to viewing the current status by calling `pcluster status mycluster`\.

```
$ pcluster version
2.11.7
```