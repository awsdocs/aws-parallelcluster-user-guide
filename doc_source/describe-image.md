# describeImage<a name="describe-image"></a>

Get detailed information about an existing image\.

**Topics**
+ [Request syntax](#describe-image-request)
+ [Request body](#describe-image-request-body)
+ [Response syntax](#describe-image-response)
+ [Response body](#describe-image-response-body)
+ [Example](#describe-image-example)

## Request syntax<a name="describe-image-request"></a>

```
GET /v3/images/custom/{imageId}
{
  "region": "string"
}
```

## Request body<a name="describe-image-request-body"></a>

**imageId**  
The ID of the image\.  
Type: string  
Required: Yes

**region**  
The AWS Region where the image was created\.  
Type: string  
Required: No

## Response syntax<a name="describe-image-response"></a>

```
{
  "imageId": "string",
  "region": "string",
  "version": "string",
  "imageBuildStatus": "BUILD_IN_PROGRESS",
  "imageBuildLogsArn": "string",
  "cloudformationStackStatus": "CREATE_IN_PROGRESS",
  "cloudformationStackStatusReason": "string",
  "cloudformationStackArn": "string",
  "creationTime": "2019-08-24T14:15:22Z",
  "cloudformationStackCreationTime": "2019-08-24T14:15:22Z",
  "cloudformationStackTags": [
    {
      "key": "string",
      "value": "string"
    }
  ],
  "imageConfiguration": {
    "url": "string"
  },
  "imagebuilderImageStatus": "PENDING",
  "imagebuilderImageStatusReason": "string",
  "ec2AmiInfo": {
    "amiId": "string",
    "tags": [
      {
        "key": "string",
        "value": "string"
      }
    ],
    "amiName": "string",
    "architecture": "string",
    "state": "PENDING",
    "description": "string"
  }
}
```

## Response body<a name="describe-image-response-body"></a>

**imageId**  
ID of the image to retrieve detailed information for\.  
Type: string

**imageBuildStatus**  
The image build status\.  
Type: string  
Valid values: `BUILD_IN_PROGRESS | BUILD_FAILED | BUILD_COMPLETE | DELETE_IN_PROGRESS | DELETE_FAILED | DELETE_COMPLETE`

**imageConfiguration**    
**url**  
The URL of the image configuration file\.  
Type: string

**region**  
The AWS Region where the image is created\.  
Type: string

**version**  
The AWS ParallelCluster version used to build the image\.  
Type: string

**cloudformationStackArn**  
The Amazon Resource Name \(ARN\) of the main CloudFormation stack\.  
Type: string

**cloudformationStackCreationTime**  
The timestamp representing the CloudFormation stack creation time\.  
Type: datetime

**cloudformationStackStatus**  
The CloudFormation stack status\.  
Type: string  
Valid values: `CREATE_IN_PROGRESS | CREATE_FAILED | CREATE_COMPLETE | ROLLBACK_IN_PROGRESS | ROLLBACK_FAILED | ROLLBACK_COMPLETE | DELETE_IN_PROGRESS | DELETE_FAILED | DELETE_COMPLETE | UPDATE_IN_PROGRESS | UPDATE_COMPLETE_CLEANUP_IN_PROGRESS | UPDATE_COMPLETE | UPDATE_ROLLBACK_IN_PROGRESS | UPDATE_ROLLBACK_FAILED | UPDATE_ROLLBACK_COMPLETE_CLEANUP_IN_PROGRESS | UPDATE_ROLLBACK_COMPLETE`

**cloudformationStackStatusReason**  
The reason for the CloudFormation stack status\.  
Type: string

**cloudformationStackTags**  
The list of tags for the CloudFormation stack\.    
**key**  
The tag name\.  
Type: string  
**value**  
The tag value\.  
Type: string

**creationTime**  
Timestamp representing the image creation time\.  
Type: datetime

**ec2AmiInfo**    
**amiId**  
The EC2 AMI ID\.  
Type: string  
**amiName**  
The EC2 AMI name\.  
Type: string  
**architecture**  
The EC2 AMI architecture\.  
Type: string  
**state**  
The state of the EC2 AMI\.  
Type: string  
Valid values: `PENDING | AVAILABLE | INVALID | DEREGISTERED | TRANSIENT | FAILED | ERROR`  
**tags**  
List of EC2 AMI Tags\.    
**key**  
Tag name\.  
Type: string  
**value**  
Tag value\.  
Type: string

**imagebuilderImageStatus**  
The ImageBuilder status\.  
Type: string  
Valid values: `PENDING | CREATING | BUILDING | TESTING | DISTRIBUTING | INTEGRATING | AVAILABLE | CANCELLED | FAILED | DEPRECATED | DELETED`

**imagebuilderImageStatusReason**  
Reason for the ImageBuilder Image status\.  
Type: string

**imageBuildLogsArn**  
The Amazon Resource Name \(ARN\) of the logs for the image build process\.  
Type: string

## Example<a name="describe-image-example"></a>

------
#### [ Python ]

**Request**

```
$ describe_image(custom-image-id)
```

**200 Response**

```
{
  'cloudformation_stack_arn': 'arn:aws:cloudformation:us-east-1:123456789012:stack/custom-image-id/6accc570-b080-11ec-845e-0e2dc6386985',
  'cloudformation_stack_creation_time': datetime.datetime(2022, 3, 30, 23, 23, 33, 731000, tzinfo=tzlocal()),
  'cloudformation_stack_status': 'CREATE_IN_PROGRESS',
  'cloudformation_stack_tags': [
    {
      'key': 'parallelcluster:version', 'value': '3.2.1'
    },
    {
      'key': 'parallelcluster:image_name',
      'value': 'custom-image-id'
    },
    {
      'key': 'parallelcluster:custom-image-id',
      'value': 'custom-image-id'
    },
    {
      'key': 'parallelcluster:s3_bucket',
      'value': 'parallelcluster-abcdef01234567890-v1-do-not-delete'
    },
    {
      'key': 'parallelcluster:s3_image_dir',
      'value': 'parallelcluster/3.2.1/images/custom-image-id-1234567890abcdef0'
    },
    {
      'key': 'parallelcluster:build_log',
      'value': 'arn:aws:logs:us-east-1:123456789012:log-group:/aws/imagebuilder/ParallelClusterImage-custom-image-id'
    },
    {
      'key': 'parallelcluster:build_config',
      'value': 's3://parallelcluster-abcdef01234567890-v1-do-not-delete/parallelcluster/3.2.1/images/custom-image-id-1234567890abcdef0/configs/image-config.yaml'
    }
  ],
  'image_build_logs_arn': 'arn:aws:logs:us-east-1:123456789012:log-group:/aws/imagebuilder/ParallelClusterImage-alinux2-image',
  'image_build_status': 'BUILD_IN_PROGRESS',
  'image_configuration': {
    'url': 'https://parallelcluster-abcdef01234567890-v1-do-not-delete.s3.amazonaws.com/parallelcluster/3.2.1/images/custom-image-id-1234567890abcdef0/configs/image-config.yaml?...'
  },
  'image_id': 'custom-image-id',
  'imagebuilder_image_status': 'PENDING',
  'region': 'us-east-1',
  'version': '3.2.1'
}
```

------