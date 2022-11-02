# buildImage<a name="build-image"></a>

Create a custom AWS ParallelCluster image in a given AWS Region\.

**Topics**
+ [Request syntax](#build-image-request)
+ [Request body](#build-image-request-body)
+ [Response syntax](#build-image-response)
+ [Response body](#build-image-response-body)
+ [Example](#build-image-example)

## Request syntax<a name="build-image-request"></a>

```
POST /v3/images/custom
{
  "imageConfiguration": "string",
  "imageId": "string",
  "dryrun": boolean,
  "region": "string",
  "rollbackOnFailure": boolean,
  "supressValidators": [ "string" ],
  "validationFailureLevel": "string"
}
```

## Request body<a name="build-image-request-body"></a>

**imageConfiguration**  
The image configuration as a YAML document\.  
Type: string  
Required: Yes

**imageId**  
The ID of the image to be built\.  
Type: string  
Required: Yes

**dryrun**  
Only perform request validation without creating any resource\. This can be used to validate the image configuration\. The default is `false`\.  
Type: bool  
Required: No

**region**  
The AWS Region where the command is given to build the image\.  
Type: string  
Required: No

**rollbackOnFailure**  
When set, it automatically initiates an image stack rollback on failures\. The default is `false`\.  
Type: bool  
Required: No

**suppressValidators**  
Identify one or more configuration validators to suppress\.  
Type: list of strings  
Format: `(ALL|type:[A-Za-z0-9]+)`  
Required: No

**validationFailureLevel**  
The minimum validation level that causes the creation to fail\. The default is `ERROR`\.  
Type: string  
Valid values: `INFO | WARNING | ERROR`  
Required: No

## Response syntax<a name="build-image-response"></a>

```
{
  "image": {
    "imageId": "string",
    "ec2AmiInfo": {
      "amiId": "string"
    },
    "region": "string",
    "version": "string",
    "cloudformationStackArn": "string",
    "imageBuildStatus": "BUILD_IN_PROGRESS",
    "cloudformationStackStatus": "CREATE_IN_PROGRESS"
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

## Response body<a name="build-image-response-body"></a>

**image**    
**imageId**  
The ID of the image\.  
Type: string  
**cloudformationStackArn**  
The Amazon Resource Name \(ARN\) of the main CloudFormation stack\.  
Type: string  
**cloudformationStackStatus**  
The CloudFormation stack status\.  
Type: string  
Valid values: `CREATE_IN_PROGRESS | CREATE_FAILED | CREATE_COMPLETE | ROLLBACK_IN_PROGRESS | ROLLBACK_FAILED | ROLLBACK_COMPLETE | DELETE_IN_PROGRESS | DELETE_FAILED | DELETE_COMPLETE | UPDATE_IN_PROGRESS | UPDATE_COMPLETE_CLEANUP_IN_PROGRESS | UPDATE_COMPLETE | UPDATE_ROLLBACK_IN_PROGRESS | UPDATE_ROLLBACK_FAILED | UPDATE_ROLLBACK_COMPLETE_CLEANUP_IN_PROGRESS | UPDATE_ROLLBACK_COMPLETE`  
**ec2AmiInfo**    
**ami\_id**  
The EC2 AMI ID\.  
Type: string  
**imageBuildStatus**  
The image build status\.  
Type: string  
Valid values: `BUILD_IN_PROGRESS | BUILD_FAILED | BUILD_COMPLETE | DELETE_IN_PROGRESS | DELETE_FAILED | DELETE_COMPLETE`  
**region**  
The AWS Region where the image is built\.  
Type: string  
**version**  
The AWS ParallelCluster version used to build the image\.  
Type: string

**validationMessages**  
A list of messages collected during image configuration validation with a validation level lower than the `validationFailureLevel` set by the user\.    
**id**  
The ID of the validator\.  
Type: string  
**level**  
The validation level\.  
Type: string  
Valid values: `INFO | WARNING | ERROR`  
**message**  
A validation message\.  
Type: string  
**type**  
The type of validator\.  
Type: string

## Example<a name="build-image-example"></a>

------
#### [ Python ]

**Request**

```
$ build_image(custom-image-id, custom-image-config.yaml)
```

**200 Response**

```
{
  'image': {
    'cloudformation_stack_arn': 'arn:aws:cloudformation:us-east-1:123456789012:stack/custom-image-id/711b76b0-af81-11ec-a29f-0ee549109f1f',
    'cloudformation_stack_status': 'CREATE_IN_PROGRESS',
    'image_build_status': 'BUILD_IN_PROGRESS',
    'image_id': 'custom-image-id',
    'region': 'us-east-1',
    'version': '3.2.1'
  }
}
```

------