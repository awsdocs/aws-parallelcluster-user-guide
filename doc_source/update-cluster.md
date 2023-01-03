# updateCluster<a name="update-cluster"></a>

Update the cluster\.

**Topics**
+ [Request syntax](#update-cluster-request)
+ [Request body](#update-cluster-request-body)
+ [Response syntax](#update-cluster-response)
+ [Response body](#update-cluster-response-body)
+ [Example](#update-cluster-example)

## Request syntax<a name="update-cluster-request"></a>

```
PUT /v3/clusters/{clusterName}
{
  "clusterConfiguration": "string",
  "dryrun": boolean,
  "forceUpdate": boolean,
  "region": "string",
  "suppressValidators": "string",
  "validationFailureLevel": "string"
}
```

## Request body<a name="update-cluster-request-body"></a>

**clusterConfiguration**  
The cluster configuration as a YAML document\.  
Required: Yes

**clusterName**  
The name of the cluster\.  
Type: string  
Required: Yes

**dryrun**  
If set to `true`, only perform request validation without creating any resource\. Use this parameter to validate the cluster configuration and update requirements\. The default is `false`\.  
Type: boolean  
Required: No

**forceUpdate**  
If set to `true`, ignore the update validation errors and force the update\. The default is `false`\.  
Type: boolean  
Required: No

**region**  
The AWS Region that the cluster is in\.  
Type: string  
Required: No

**suppressValidators**  
Identifies one or more configuration validators to suppress\.  
Type: string  
Format: `(ALL|type:[A-Za-z0-9]+)`  
Required: No  
Example valid values: `currentValue`, `requestedValue`, `message`

**validationFailureLevel**  
The minimum validation level to cause the update to fail\.  
Type: string  
Valid values: `INFO | WARNING | ERROR`  
Required: No

## Response syntax<a name="update-cluster-response"></a>

```
{
  "cluster": {
    "clusterName": "string",
    "region": "string",
    "version": "string",
    "cloudformationStackArn": "string",
    "cloudformationStackStatus": "UPDATE_IN_PROGRESS",
    "clusterStatus": "UPDATE_IN_PROGRESS",
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
  ],
  "changeSet": [
    {
      "parameter": "string",
      "currentValue": "string",
      "requestedValue": "string"
    }
  ]
}
```

## Response body<a name="update-cluster-response-body"></a>

**changeSet**  
The change set for the cluster update\.    
**currentValue**  
The current value of the parameter to be updated\.  
Type: string  
**parameter**  
The parameter to be updated\.  
Type: string  
**requestedValue**  
The requested value for the parameter to be updated\.  
Type: string

**cluster**    
**cloudformationStackArn**  
The Amazon Resource Name \(ARN\) of the main CloudFormation stack\.  
Type: string  
**cloudformationStackStatus**  
The CloudFormation stack status\.  
Type: string  
Valid values: `CREATE_IN_PROGRESS | CREATE_FAILED | CREATE_COMPLETE | ROLLBACK_IN_PROGRESS | ROLLBACK_FAILED | ROLLBACK_COMPLETE | DELETE_IN_PROGRESS | DELETE_FAILED | DELETE_COMPLETE | UPDATE_IN_PROGRESS | UPDATE_COMPLETE_CLEANUP_IN_PROGRESS | UPDATE_COMPLETE | UPDATE_ROLLBACK_IN_PROGRESS | UPDATE_ROLLBACK_FAILED | UPDATE_ROLLBACK_COMPLETE_CLEANUP_IN_PROGRESS | UPDATE_ROLLBACK_COMPLETE`  
**clusterName**  
The name of cluster\.  
Type: string  
**clusterStatus**  
The cluster status\.  
Type: string  
Valid values: `CREATE_IN_PROGRESS | CREATE_FAILED | CREATE_COMPLETE | DELETE_IN_PROGRESS | DELETE_FAILED | DELETE_COMPLETE | UPDATE_IN_PROGRESS | UPDATE_COMPLETE | UPDATE_FAILED`  
**region**  
The AWS Region that the cluster is created in\.  
Type: string  
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

**version**  
AWS ParallelCluster version that's used to create the cluster\.  
Type: string

**validationMessages**  
A list of messages with a validation level lower than `validationFailureLevel`\. The list of messages is collected during configuration validation\.    
**id**  
The ID of the validator\.  
Type: string  
**level**  
The validation level\.  
Type: string  
Valid values: `INFO | WARNING | ERROR`  
**message**  
The validation message\.  
Type: string  
**type**  
The type of the validator\.  
Type: string

## Example<a name="update-cluster-example"></a>

------
#### [ Python ]

**Request**

```
$ update_cluster(cluster_name_3x, path/config-file.yaml)
```

**200 Response**

```
{
  'change_set': [
    {
      'current_value': '10',
      'parameter': 'Scheduling.SlurmQueues[queue1].ComputeResources[t2micro].MaxCount',
      'requested_value': '15'
    }
  ],
  'cluster': {
    'cloudformation_stack_arn': 'arn:aws:cloudformation:us-east-1:123456789012:stack/test-api-cluster/e0462730-50b5-11ed-99a3-0a5ddc4a34c7',
    'cloudformation_stack_status': 'UPDATE_IN_PROGRESS',
    'cluster_name': 'cluster-3x',
    'cluster_status': 'UPDATE_IN_PROGRESS',
    'region': 'us-east-1',
    'scheduler': {
      'type': 'slurm'
    },
    'version': '3.2.1'
  }
}
```

------