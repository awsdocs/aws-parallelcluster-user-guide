# describeClusterInstances<a name="describe-cluster-instances"></a>

Describe the instances belonging to a given cluster\.

**Topics**
+ [Request syntax](#describe-cluster-instances-request)
+ [Request body](#describe-cluster-instances-request-body)
+ [Response syntax](#describe-cluster-instances-response)
+ [Response body](#describe-cluster-instances-response-body)
+ [Example](#describe-cluster-instances-example)

## Request syntax<a name="describe-cluster-instances-request"></a>

```
GET /v3/clusters/{clusterName}/instances
{
  "nextToken": "string",
  "nodeType": "string",
  "queueName": "string",
  "region": "string"
}
```

## Request body<a name="describe-cluster-instances-request-body"></a>

**clusterName**  
The name of the cluster\.  
Type: string  
Required: Yes

**nextToken**  
A token to use for paginated requests\.  
Type: string  
Required: No

**nodeType**  
Filter the instances by node type\.  
Type: string  
Valid values: `HeadNode`, `ComputeNode`  
Required: No

**queueName**  
Filter the instances by queue name\.  
Type: string  
Required: No

**region**  
The AWS Region of the cluster\.  
Type: string  
Required: No

## Response syntax<a name="describe-cluster-instances-response"></a>

```
{
  "nextToken": "string",
  "instances": [
    {
      "instanceId": "string",
      "instanceType": "string",
      "launchTime": "2019-08-24T14:15:22Z",
      "privateIpAddress": "string",
      "publicIpAddress": "string",
      "state": "pending",
      "nodeType": "HeadNode",
      "queueName": "string"
    }
  ]
}
```

## Response body<a name="describe-cluster-instances-response-body"></a>

**instances**  
The list of cluster instances\.    
**instanceId**  
The EC2 instance ID\.  
Type: string  
**instanceType**  
The EC2 instance type\.  
Type: string  
**launchTime**  
The time when the EC2 instance was launched\.  
Type: datetime  
**nodeType**  
The node type\.  
Type: string  
Valid values: `HeadNode`, `ComputeNode`  
**publicIpAddress**  
The cluster public IP address\.  
Type: string  
**queueName**  
The name of the queue where the EC2 instance is backing a node\.  
Type: string  
**state**  
The node EC2 instance status\.  
Type: string  
Valid values: `pending | running | shutting-down | terminated | stopping | stopped`

**nextToken**  
A token to use for paginated requests\.  
Type: string

## Example<a name="describe-cluster-instances-example"></a>

------
#### [ Python ]

**Request**

```
$ describe_cluster_instances(cluster_name_3x)
```

**200 Response**

```
{
  'instances': [
    {
      'instance_id': 'i-abcdef01234567890',
      'instance_type': 't2.micro',
      'launch_time': datetime.datetime(2022, 3, 30, 14, 2, 7, tzinfo=tzlocal()),
      'node_type': 'HeadNode',
      'private_ip_address': '192.0.2.5',
      'public_ip_address': '198.51.100.180',
      'state': 'running'
    }
  ]
}
```

------