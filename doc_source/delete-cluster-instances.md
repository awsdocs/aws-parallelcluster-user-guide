# deleteClusterInstances<a name="delete-cluster-instances"></a>

Initiate the forced termination of all cluster compute nodes\. This action doesn't support AWS Batch clusters\.

**Topics**
+ [Request syntax](#delete-cluster-instances-request)
+ [Request body](#delete-cluster-instances-request-body)
+ [Response body](#delete-cluster-instances-response-body)
+ [Example](#delete-cluster-instances-example)

## Request syntax<a name="delete-cluster-instances-request"></a>

```
DELETE /v3/clusters/{clusterName}/instances
{
  "force": boolean,
  "region": "string"
}
```

## Request body<a name="delete-cluster-instances-request-body"></a>

**clusterName**  
The name of the cluster\.  
Type: string  
Required: Yes

**force**  
Force the deletion when the cluster with the given name isn't found\. The default is `false`\.  
Type: bool  
Required: No

**region**  
The AWS Region of the cluster\.  
Type: string  
Required: No

## Response body<a name="delete-cluster-instances-response-body"></a>

None

## Example<a name="delete-cluster-instances-example"></a>

------
#### [ Python ]

**Request**

```
$ delete_cluster_instances(cluster_name_3x)
```

**200 Response**

None

------