# `pcluster list-clusters`<a name="pcluster.list-clusters-v3"></a>

Retrieve the list of existing clusters\.

```
pcluster list-clusters [-h]
                [--cluster-status {CREATE_IN_PROGRESS,CREATE_FAILED,CREATE_COMPLETE,
                           DELETE_IN_PROGRESS,DELETE_FAILED,UPDATE_IN_PROGRESS,
                           UPDATE_COMPLETE,UPDATE_FAILED}
                           [{CREATE_IN_PROGRESS,CREATE_FAILED,CREATE_COMPLETE,
                             DELETE_IN_PROGRESS,DELETE_FAILED,UPDATE_IN_PROGRESS,
                             UPDATE_COMPLETE,UPDATE_FAILED} ...]]
                [--debug]
                [--next-token NEXT_TOKEN]
                [--query QUERY]
                [--region REGION]
```

## Named arguments<a name="pcluster-v3.list-clusters.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster list-clusters`\.

`--cluster-status {CREATE_IN_PROGRESS, CREATE_FAILED, CREATE_COMPLETE, DELETE_IN_PROGRESS, DELETE_FAILED, UPDATE_IN_PROGRESS, UPDATE_COMPLETE, UPDATE_FAILED} [{CREATE_IN_PROGRESS, CREATE_FAILED, CREATE_COMPLETE, DELETE_IN_PROGRESS, DELETE_FAILED, UPDATE_IN_PROGRESS, UPDATE_COMPLETE, UPDATE_FAILED} ...]`  
Specifies the list of cluster statuses to filter for\. \(Defaults to `all`\.\)

`--debug`  
Enables debug logging\.

`--next-token NEXT_TOKEN`  
Specifies the token to use for paginated requests\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The AWS Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

**Example using AWS ParallelCluster version 3\.1\.4:**

```
$ pcluster list-clusters
{
  "clusters": [
    {
      "clusterName": "cluster-v3",
      "cloudformationStackStatus": "CREATE_COMPLETE",
      "cloudformationStackArn": "arn:aws:cloudformation:us-east-1:123456789012:stack/cluster-v3/1234abcd-56ef-78gh-90ij-abcd1234efgh",
      "region": "us-east-1",
      "version": "3.1.4",
      "clusterStatus": "CREATE_COMPLETE"
    }
  ]
}
```