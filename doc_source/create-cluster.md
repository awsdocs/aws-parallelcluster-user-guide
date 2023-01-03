# createCluster<a name="create-cluster"></a>

Create a managed cluster in an AWS Region\.

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
The name of the cluster to create\.  
The name must start with an alphabetical character\. The name can have up to 60 characters\. If Slurm accounting is enabled, the name can have up to 40 characters\.  
Type: string  
Required: Yes

**dryrun**  
If set to `true`, only perform request validation without creating any resource\. Use this parameter to validate the cluster configuration\. The default is `false`\.  
Type: boolean  
Required: No

**region**  
The AWS Region that the cluster is in\.  
Type: string  
Required: No

**rollbackOnFailure**  
If set to `true`, cluster stack rollback occurs if the cluster fails to create\. The default is `true`\.  
Type: boolean  
Required: No

**suppressValidators**  
Identify one or more configuration validators to suppress\.  
Type: list of strings  
Format: `(ALL|type:[A-Za-z0-9]+)`  
Required: No

**validationFailureLevel**  
The minimum validation level that causes cluster create to fail\. The default is `ERROR`\.  
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
The AWS Region that the cluster is created in\.  
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
The AWS ParallelCluster version that's used to create the cluster\.  
Type: string

**validation\_messages**  
A list of messages with a validation level lower than `validationFailureLevel`\. The list of messages is collected during configuration validation\.    
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