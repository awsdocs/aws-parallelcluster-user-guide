# `pcluster list-clusters`<a name="pcluster.list-clusters-v3"></a>

Retrieve the list of existing clusters\.

```
pcluster list-clusters [-h] [--region REGION] [--next-token NEXT_TOKEN]
                       [--cluster-status {CREATE_IN_PROGRESS,CREATE_FAILED,CREATE_COMPLETE,
                                          DELETE_IN_PROGRESS,DELETE_FAILED,UPDATE_IN_PROGRESS,
                                          UPDATE_COMPLETE,UPDATE_FAILED}
                                         [{CREATE_IN_PROGRESS,CREATE_FAILED,CREATE_COMPLETE,
                                           DELETE_IN_PROGRESS,DELETE_FAILED,UPDATE_IN_PROGRESS,
                                           UPDATE_COMPLETE,UPDATE_FAILED} ...]]
                       [--debug] [--query QUERY]
```

## Named arguments<a name="pcluster-v3.list-clusters.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster list-clusters`\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

`--next-token NEXT_TOKEN`  
Specifies the token to use for paginated requests\.

`--cluster-status {CREATE_IN_PROGRESS, CREATE_FAILED, CREATE_COMPLETE, DELETE_IN_PROGRESS, DELETE_FAILED, UPDATE_IN_PROGRESS, UPDATE_COMPLETE, UPDATE_FAILED} [{CREATE_IN_PROGRESS, CREATE_FAILED, CREATE_COMPLETE, DELETE_IN_PROGRESS, DELETE_FAILED, UPDATE_IN_PROGRESS, UPDATE_COMPLETE, UPDATE_FAILED} ...]`  
Specifies the list of cluster statuses to filter for\. \(Defaults to `all`\.\)

`--debug`  
Enables debug logging\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.