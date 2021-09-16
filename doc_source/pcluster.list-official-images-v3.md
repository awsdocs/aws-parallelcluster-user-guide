# `pcluster list-official-images`<a name="pcluster.list-official-images-v3"></a>

Describe official AWS ParallelCluster AMIs\.

```
pcluster list-official-images [-h] [--region REGION] [--os OS]
                                  [--architecture ARCHITECTURE]
                                  [--debug] [--query QUERY]
```

## Named arguments<a name="pcluster-v3.list-official-images.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster list-official-images`\.

`--region REGION`  
Specifies the AWS Region to use\.

`--os OS`  
Specifies the operating system to use to filter the results\. If this parameter is not specified, all operating systems are returned\.

`--architecture ARCHITECTURE`  
Specifies the architecture to use to filter the results\. If this parameter is not specified, all archiectures are returned\.

`--debug`  
Enables debug logging\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.