# deleteCluster<a name="delete-cluster"></a>

Initiate the deletion of a cluster\.

**Topics**
+ [Request syntax](#delete-cluster-request)
+ [Request body](#delete-cluster-request-body)
+ [Response syntax](#delete-cluster-response)
+ [Response body](#delete-cluster-response-body)
+ [Example](#delete-cluster-example)

## Request syntax<a name="delete-cluster-request"></a>

```
DELETE /v3/clusters/{clusterName}
{
  "region": "string"
}
```

## Request body<a name="delete-cluster-request-body"></a>

**clusterName**  
The name of the cluster\.  
Type: string  
Required: Yes

**region**  
The AWS Region of the cluster to be deleted\.  
Type: string  
Required: No

## Response syntax<a name="delete-cluster-response"></a>

```
{
   "cluster": {
       "clusterName": "string",
       "region": "string",
       "version": "string",
       "cloudformationStackArn": "string",
       "cloudformationStackStatus": "DELETE_IN_PROGRESS",
       "clusterStatus": "DELETE_IN_PROGRESS",
       "scheduler": {
           "type": "string",
           "metadata": {
               "name": "string",
               "version": "string"
           }
        }
    }
}
```

## Response body<a name="delete-cluster-response-body"></a>

**cluster**  
List of cluster instances    
**clusterName**  
The name of cluster\.  
Type: string  
**cloudformationStackArn**  
The Amazon Resource Name \(ARN\) of the main CloudFormation stack\.  
Type: string  
**cloudformationStackStatus**  
Type: string  
Valid values: `CREATE_IN_PROGRESS | CREATE_FAILED | CREATE_COMPLETE | ROLLBACK_IN_PROGRESS | ROLLBACK_FAILED | ROLLBACK_COMPLETE | DELETE_IN_PROGRESS | DELETE_FAILED | DELETE_COMPLETE | UPDATE_IN_PROGRESS | UPDATE_COMPLETE_CLEANUP_IN_PROGRESS | UPDATE_COMPLETE | UPDATE_ROLLBACK_IN_PROGRESS | UPDATE_ROLLBACK_FAILED | UPDATE_ROLLBACK_COMPLETE_CLEANUP_IN_PROGRESS | UPDATE_ROLLBACK_COMPLETE`  
**clusterStatus**  
Type: string  
Valid values: `CREATE_IN_PROGRESS | CREATE_FAILED | CREATE_COMPLETE | DELETE_IN_PROGRESS | DELETE_FAILED | DELETE_COMPLETE | UPDATE_IN_PROGRESS | UPDATE_COMPLETE | UPDATE_FAILED`  
**region**  
The AWS Region where the cluster is created\.  
Type: string  
**scheduler**    
**metadata**  
The scheduler metadata\.    
**name**  
The name of the scheduler\.  
Type: string  
**version**  
The scheduler version  
Type: string  
**type**  
The scheduler type\.  
Type: string  
**version**  
The AWS ParallelCluster version used to create the cluster\.  
Type: string

## Example<a name="delete-cluster-example"></a>

------
#### [ Python ]

**Request**

```
$ delete_cluster(cluster_name_3x)
```

**200 Response**

```
{
  'cluster': {
    'cloudformation_stack_arn': 'arn:aws:cloudformation:us-east-1:123456789012:stack/cluster_name_3x/16b49540-aee5-11ec-8e18-0ac1d712b241',
    'cloudformation_stack_status': 'DELETE_IN_PROGRESS',
    'cluster_name': 'cluster_name_3x',
    'cluster_status': 'DELETE_IN_PROGRESS',
    'region': 'us-east-1',
    'version': '3.2.1'
  }
}
```

------