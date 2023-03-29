# describeCluster<a name="describe-cluster"></a>

Get detailed information about an existing cluster\.

**Topics**
+ [Request syntax](#describe-cluster-request)
+ [Request body](#describe-cluster-request-body)
+ [Response syntax](#describe-cluster-response)
+ [Response body](#describe-cluster-response-body)
+ [Example](#describe-cluster-example)

## Request syntax<a name="describe-cluster-request"></a>

```
GET /v3/clusters/{clusterName}
{
  "region": "string"
}
```

## Request body<a name="describe-cluster-request-body"></a>

**clusterName**  
The name of the cluster\.  
Type: string  
Required: Yes

**region**  
The AWS Region that the cluster is in\.  
Type: string  
Required: No

## Response syntax<a name="describe-cluster-response"></a>

**Note**  
`failureReason` has changed to `failures` starting with AWS ParallelCluster version 3\.5\.0\.

```
{
  "clusterName": "string",
  "region": "string",
  "version": "string",
  "cloudFormationStackStatus": "CREATE_IN_PROGRESS",
  "clusterStatus": "CREATE_IN_PROGRESS",
  "scheduler": {
    "type": "string",
    "metadata": {
      "name": "string",
      "version": "string"
    }
  },
  "cloudformationStackArn": "string",
  "creationTime": "2019-08-24T14:15:22Z",
  "lastUpdatedTime": "2019-08-24T14:15:22Z",
  "clusterConfiguration": {
    "url": "string"
  },
  "computeFleetStatus": "START_REQUESTED",
  "tags": [
    {
      "key": "string",
      "value": "string"
    }
  ],
  "headNode": {
    "instanceId": "string",
    "instanceType": "string",
    "launchTime": "2019-08-24T14:15:22Z",
    "privateIpAddress": "string",
    "publicIpAddress": "string",
    "state": "pending"
  },
  "failures": [
    {
      "failureCode": "string",
      "failureReason": "string"
    }
  ]
}
```

## Response body<a name="describe-cluster-response-body"></a>

**clusterName**  
The name of the cluster\.  
Type: string

**cloudformationStackArn**  
The Amazon Resource Name \(ARN\) of the main CloudFormation stack\.  
Type: string

**cloudformationStackStatus**  
The CloudFormation stack status\.  
Type: string  
Valid values: `CREATE_IN_PROGRESS | CREATE_FAILED | CREATE_COMPLETE | ROLLBACK_IN_PROGRESS | ROLLBACK_FAILED | ROLLBACK_COMPLETE | DELETE_IN_PROGRESS | DELETE_FAILED | DELETE_COMPLETE | UPDATE_IN_PROGRESS | UPDATE_COMPLETE_CLEANUP_IN_PROGRESS | UPDATE_COMPLETE | UPDATE_ROLLBACK_IN_PROGRESS | UPDATE_ROLLBACK_FAILED | UPDATE_ROLLBACK_COMPLETE_CLEANUP_IN_PROGRESS | UPDATE_ROLLBACK_COMPLETE`

**clusterConfiguration**    
**url**  
The URL of the cluster configuration file\.  
Type: string

**clusterStatus**  
The cluster status\.  
Type: string  
Valid values: `CREATE_IN_PROGRESS | CREATE_FAILED | CREATE_COMPLETE | DELETE_IN_PROGRESS | DELETE_FAILED | DELETE_COMPLETE | UPDATE_IN_PROGRESS | UPDATE_COMPLETE | UPDATE_FAILED`

**computeFleetStatus**  
The compute fleet status\.  
Type: string  
Valid values: `START_REQUESTED | STARTING | RUNNING | PROTECTED | STOP_REQUESTED | STOPPING | STOPPED | UNKNOWN | ENABLED | DISABLED`

**creationTime**  
Timestamp for when the cluster was created\.  
Type: datetime

**lastUpdatedTime**  
Timestamp for when the cluster was last updated\.  
Type: datetime

**region**  
The AWS Region that the cluster is created in\.  
Type: string

**tags**  
The list of tags that are associated with the cluster\.    
**key**  
Tag name\.  
Type: string  
**tag**  
Tag value\.  
Type: string

**version**  
The AWS ParallelCluster version that's used to create the cluster\.  
Type: string

**failures**  
The list of failures when the cluster stack is in `CREATE_FAILED` status\.    
**failureCode**  
The failure code when the cluster stack is in `CREATE_FAILED` status\.  
Type: string  
**failureReason**  
The reason for the failure when the cluster stack is in `CREATE_FAILED` status\.  
Type: string

**head\_node**  
The cluster head node\.    
**instanceId**  
The EC2 instance ID\.  
Type: string  
**instanceType**  
The EC2 instance type\.  
Type: string  
**launchTime**  
The time when the EC2 instance was launched\.  
Type: datetime  
**privateIpAddress**  
The cluster private IP address\.  
Type: string  
**publicIpAddress**  
The cluster public IP address\.  
Type: string  
**state**  
The head node instance status\.  
Type: string  
Valid values: `pending | running | shutting-down | terminated | stopping | stopped`

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
The scheduler type\.  
Type: string

## Example<a name="describe-cluster-example"></a>

------
#### [ Python ]

**Request**

```
$ describe_cluster(cluster_name_3x)
```

**200 Response**

```
{
  'cloud_formation_stack_status': 'CREATE_COMPLETE',
  'cloudformation_stack_arn': 'arn:aws:cloudformation:us-east-1:123456789012:stack/cluster_name_3x/16b49540-aee5-11ec-8e18-0ac1d712b241',
  'cluster_configuration': {
    'url': 'https://parallelcluster-....'
  },
  'cluster_name': 'cluster_name_3x',
  'cluster_status': 'CREATE_COMPLETE',
  'compute_fleet_status': 'RUNNING',
  'creation_time': datetime.datetime(2022, 3, 28, 22, 19, 9, 661000, tzinfo=tzlocal()),
  'head_node': {
    'instance_id': 'i-abcdef01234567890',
    'instance_type': 't2.micro',
    'launch_time': datetime.datetime(2022, 3, 28, 22, 21, 56, tzinfo=tzlocal()),
    'private_ip_address': '172.31.56.3',
    'public_ip_address': '107.23.100.164',
    'state': 'running'
  },
  'last_updated_time': datetime.datetime(2022, 3, 28, 22, 19, 9, 661000, tzinfo=tzlocal()),
  'region': 'us-east-1',
  'tags': [
    {
      'key': 'parallelcluster:version', 'value': '3.2.1'
    }
  ],
  'version': '3.2.1'
}
```

------