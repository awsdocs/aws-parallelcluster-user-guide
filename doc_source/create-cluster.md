# createCluster<a name="create-cluster"></a>

Create a managed cluster in a given AWS Region\.

**Topics**
+ [Request syntax](#create-cluster-request)
+ [Request body](#create-cluster-request-body)
+ [Response syntax](#create-cluster-response)
+ [Response body](#create-cluster-response-body)
+ [Example](#create-cluster-example)

## Request syntax<a name="create-cluster-request"></a>

```
POST /v3/clusters
{
  "clusterName": "string",
  "clusterConfiguration": "string",
  "dryrun": boolean,
  "region": "string",
  "rollbackOnFailure", boolean,
  "suppressValidators": [ "string" ],
  "validationFailureLevel": "string"
}
```

## Request body<a name="create-cluster-request-body"></a>

**clusterConfiguration**  
The cluster configuration as a YAML document\.  
Type: string  
Required: Yes

**clusterName**  
The name of the cluster to be created\.  
Type: string  
Required: Yes

**dryrun**  
Only perform request validation without creating any resource\. This can be used to validate the cluster configuration\. The default is `false`\.  
Type: bool  
Required: No

**region**  
The AWS Region of the cluster\.  
Type: string  
Required: No

**rollbackOnFailure**  
When set, it automatically initiates a cluster stack rollback on failures\. The default is `true`\.  
Type: bool  
Required: No

**suppressValidators**  
Identify one or more config validators to suppress\.  
Type: list of strings  
Format: `(ALL|type:[A-Za-z0-9]+)`  
Required: No

**validationFailureLevel**  
The minimum validation level that causes the creation to fail\. The default is `ERROR`\.  
Type: string  
Valid values: `INFO | WARNING | ERROR`  
Required: No

## Response syntax<a name="create-cluster-response"></a>

```
{
  "cluster": {
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
  },
  "validationMessages": [
    {
      "id": "string",
      "type": "string",
      "level": "INFO",
      "message": "string"
    }
  ]
}
```

## Response body<a name="create-cluster-response-body"></a>

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
The scheduler metadata    
**name**  
The name of the scheduler\.  
Type: string  
**version**  
The scheduler version\.  
Type: string  
**type**  
The scheduler type\.  
Type: string

**version**  
The AWS ParallelCluster version used to create the cluster\.  
Type: string

**validation\_messages**  
A list of messages collected during cluster configuration validation with a validation level lower than the `validationFailureLevel` set by the user\.    
**id**  
The ID of the validator\.  
Type: string  
**level**  
Type: string  
Valid values: `INFO | WARNING | ERROR`  
**message**  
A validation message\.  
Type: string  
**type**  
The type of the validator\.  
Type: string

## Example<a name="create-cluster-example"></a>

------
#### [ Python ]

**Request**

```
$ create_cluster(cluster_name_3x, cluster-config.yaml)
```

**200 Response**

```
{
  'cluster': {
    'cloudformation_stack_arn': 'arn:aws:cloudformation:us-east-1:123456789012:stack/cluster-3x/e0462730-50b5-11ed-99a3-0a5ddc4a34c7',
    'cloudformation_stack_status': 'CREATE_IN_PROGRESS',
    'cluster_name': 'cluster-3x',
    'cluster_status': 'CREATE_IN_PROGRESS',
    'region': 'us-east-1',
    'scheduler': {
      'type': 'slurm'
    },
    'version': '3.2.1'
  }
}
```

------