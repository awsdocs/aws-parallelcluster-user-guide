# listClusters<a name="list-clusters"></a>

Retrieve a list of existing clusters\.

**Topics**
+ [Request syntax](#list-clusters-request)
+ [Request body](#list-clusters-request-body)
+ [Response syntax](#list-clusters-response)
+ [Response body](#list-clusters-response-body)
+ [Example](#list-clusters-example)

## Request syntax<a name="list-clusters-request"></a>

```
GET /v3/clusters
{
  "clusterStatus": "string",
  "nextToken": "string",
  "region": "string"
}
```

## Request body<a name="list-clusters-request-body"></a>

**clusterStatus**  
Filter by cluster status\. The default is all clusters\.  
Type: string  
Valid values: `CREATE_IN_PROGRESS | CREATE_FAILED | CREATE_COMPLETE | DELETE_IN_PROGRESS | DELETE_FAILED | UPDATE_IN_PROGRESS | UPDATE_COMPLETE | UPDATE_FAILED`  
Required: no

**nextToken**  
A token to use for paginated requests\.  
Type: string  
Required: No

**region**  
The AWS Region of the cluster\.  
Type: string  
Required: No

## Response syntax<a name="list-clusters-response"></a>

```
{
  "nextToken": "string",
  "clusters": [
    {
      "clusterName": "string",
      "region": "string",
      "version": "string",
      "cloudformationStackArn": "string",
      "cloudformationStackStatus": "CREATE_IN_PROGRESS",
      "clusterStatus": "CREATE_IN_PROGRESS",
      "scheduler": {
        "type": "string",
        "metadata": {
          "name": "string",
          "version": "string"
        }
      }
    }
  ]
}
```

## Response body<a name="list-clusters-response-body"></a>

**clusters**    
**cloudformationStackArn**  
The Amazon Resource Name \(ARN\) of the main CloudFormation stack\.  
Type: string  
**cloudformationStackStatus**  
The CloudFormation stack status\.  
Type: string  
Valid values: `CREATE_IN_PROGRESS | CREATE_FAILED | CREATE_COMPLETE | ROLLBACK_IN_PROGRESS | ROLLBACK_FAILED | ROLLBACK_COMPLETE | DELETE_IN_PROGRESS | DELETE_FAILED | DELETE_COMPLETE | UPDATE_IN_PROGRESS | UPDATE_COMPLETE_CLEANUP_IN_PROGRESS | UPDATE_COMPLETE | UPDATE_ROLLBACK_IN_PROGRESS | UPDATE_ROLLBACK_FAILED | UPDATE_ROLLBACK_COMPLETE_CLEANUP_IN_PROGRESS | UPDATE_ROLLBACK_COMPLETE`  
**clusterName**  
The name of the cluster\.  
Type: string  
**clusterStatus**  
The cluster status\.  
Type: string  
Valid values: `CREATE_IN_PROGRESS | CREATE_FAILED | CREATE_COMPLETE | DELETE_IN_PROGRESS | DELETE_FAILED | DELETE_COMPLETE | UPDATE_IN_PROGRESS | UPDATE_COMPLETE | UPDATE_FAILED`  
**scheduler**    
**metadata**  
The scheduler metadata\.    
**name**  
The name of the scheduler\.  
Type: string  
**version**  
The scheduler version\.  
Type: string  
**type**  
The type of scheduler\.  
Type: string  
**region**  
The AWS Region where the cluster is created\.  
Type: string  
**version**  
The AWS ParallelCluster version used to create the cluster\.  
Type: string

**nextToken**  
A token to use for paginated requests\.  
Type: string

## Example<a name="list-clusters-example"></a>

------
#### [ Python ]

**Request**

```
$ list_clusters()
```

**200 Response**

```
{
  'clusters': 
   [
      {
        'cloudformation_stack_arn': 'arn:aws:cloudformation:us-east-1:123456789012:stack/cluster_name_3x/16b49540-aee5-11ec-8e18-0ac1d712b241',
        'cloudformation_stack_status': 'CREATE_COMPLETE',
        'cluster_name': 'cluster_name_3x',
        'cluster_status': 'CREATE_COMPLETE',
        'region': 'us-east-1',
        'version': '3.2.1'
      },
      ...
   ]
}
```

------