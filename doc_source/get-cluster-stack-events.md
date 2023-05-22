# getClusterStackEvents<a name="get-cluster-stack-events"></a>

Retrieve the events that are associated with the stack for a cluster\.

**Note**  
Starting in version 3\.6\.0, AWS ParallelCluster uses nested stacks to create the resources associated with queues and compute resources\. The `GetClusterStackEvents` API and the `pcluster get-cluster-stack-events` command only return the cluster main stack events\. You can view the cluster stack events, including those related to queues and compute resources, in the CloudFormation console\.

**Topics**
+ [Request syntax](#get-cluster-stack-events-request)
+ [Request body](#get-cluster-stack-events-request-body)
+ [Response syntax](#get-cluster-stack-events-response)
+ [Response body](#get-cluster-stack-events-response-body)
+ [Example](#get-cluster-stack-events-example)

## Request syntax<a name="get-cluster-stack-events-request"></a>

```
GET /v3/clusters/{clusterName}/stackevents
{
  "nextToken": "string",
  "region": "string"
}
```

## Request body<a name="get-cluster-stack-events-request-body"></a>

**clusterName**  
The name of the cluster\.  
Type: string  
Required: Yes

**nextToken**  
A token that's used for paginated requests\.  
Type: string  
Required: No

**region**  
The AWS Region that the cluster is in\.  
Type: string  
Required: No

## Response syntax<a name="get-cluster-stack-events-response"></a>

```
{
  "nextToken": "string",
  "events": [
    {
      "stackId": "string",
      "eventId": "string",
      "stackName": "string",
      "logicalResourceId": "string",
      "physicalResourceId": "string",
      "resourceType": "string",
      "timestamp": "2019-08-24T14:15:22Z",
      "resourceStatus": "CREATE_IN_PROGRESS",
      "resourceStatusReason": "string",
      "resourceProperties": "string",
      "clientRequestToken": "string"
    }
  ]
}
```

## Response body<a name="get-cluster-stack-events-response-body"></a>

**events**  
List of filtered events\.    
**clientRequestToken**  
The token passed to the action that generated this event\.  
Type: string  
**eventId**  
The unique ID of this event\.  
Type: string  
**logicalResourceId**  
The logical name of the resource specified in the template\.  
Type: string  
**physicalResourceId**  
The name or unique identifier that's associated with the physical instance of the resource\.  
Type: string  
**resourceProperties**  
A BLOB of the properties that are used to create the resource\.  
Type: string  
**resourceStatus**  
The resource status\.  
Type: string  
Valid values: `CREATE_IN_PROGRESS | CREATE_FAILED | CREATE_COMPLETE | DELETE_IN_PROGRESS | DELETE_FAILED | DELETE_COMPLETE | DELETE_SKIPPED | UPDATE_IN_PROGRESS | UPDATE_FAILED | UPDATE_COMPLETE | IMPORT_FAILED | IMPORT_COMPLETE | IMPORT_IN_PROGRESS | IMPORT_ROLLBACK_IN_PROGRESS | IMPORT_ROLLBACK_FAILED | IMPORT_ROLLBACK_COMPLETE`  
**resourceStatusReason**  
A success or failure message that's associated with the resource\.  
Type: string  
**resourceType**  
The type of resource\.  
Type: string  
**stackId**  
The unique ID name of the instance of the stack\.  
Type: string  
**stackName**  
The name that's associated with a stack\.  
Type: string  
**timestamp**  
The time when the status was updated\.  
Type: datetime

**nextToken**  
A token that's used for paginated requests\.  
Type: string

## Example<a name="get-cluster-stack-events-example"></a>

------
#### [ Python ]

**Request**

```
$ get_cluster_stack_events(cluster_name_3x)
```

**200 Response**

```
{
  'events': [
    {
      'event_id': '590b3820-b081-11ec-985e-0a7af5751497',
      'logical_resource_id': 'cluster_name_3x',
      'physical_resource_id': 'arn:aws:cloudformation:us-east-1:123456789012:stack/cluster_name_3x/11a59710-b080-11ec-b8bd-129def1380e9',
      'resource_status': 'CREATE_COMPLETE',
      'resource_type': 'AWS::CloudFormation::Stack',
      'stack_id': 'arn:aws:cloudformation:us-east-1:123456789012:stack/cluster_name_3x/11a59710-b080-11ec-b8bd-129def1380e9',
      'stack_name': 'cluster_name_3x',
      'timestamp': datetime.datetime(2022, 3, 30, 23, 30, 13, 268000, tzinfo=tzlocal())
    },
    ...
  ]
}
```

------