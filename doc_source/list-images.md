# listImages<a name="list-images"></a>

Retrieve the list of existing custom images\.

**Topics**
+ [Request syntax](#list-images-request)
+ [Request body](#list-images-request-body)
+ [Response syntax](#list-images-response)
+ [Response body](#list-images-response-body)
+ [Example](#list-images-example)

## Request syntax<a name="list-images-request"></a>

```
GET /images/custom
{
  "imageStatus": "string",
  "nextToken": "string",
  "region": "string"
}
```

## Request body<a name="list-images-request-body"></a>

**imageStatus**  
Filter images by the status provided\.  
Type: string  
Valid values: `AVAILABLE | PENDING | FAILED`  
Required: Yes

**nextToken**  
A token that's used for paginated requests\.  
Type: string  
Required: No

**region**  
The AWS Region that images are in\.  
Type: string  
Required: No

## Response syntax<a name="list-images-response"></a>

```
{
  "nextToken": "string",
  "images": [
    {
      "imageId": "string",
      "ec2AmiInfo": {
        "amiId": "string"
      },
      "region": "string",
      "version": "string",
      "cloudformationStackArn": "string",
      "imageBuildStatus": "BUILD_IN_PROGRESS",
      "cloudformationStackStatus": "CREATE_IN_PROGRESS"
    }
  ]
}
```

## Response body<a name="list-images-response-body"></a>

**images**  
A list of images\.    
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
Valid values: `BUILD_IN_PROGRESS | BUILD_FAILED | BUILD_COMPLETE | DELETE_IN_PROGRESS | DELETE_FAILED | DELETE_COMPLETE`  
Type: string  
**imageId**  
The ID of the image\.  
Type: string  
**region**  
The AWS Region that the image is created in\.  
Type: string  
**version**  
The AWS ParallelCluster version that's used to build the image\.  
Type: string

**nextToken**  
A token that's used for paginated requests\.  
Type: string

## Example<a name="list-images-example"></a>

------
#### [ Python ]

**Request**

```
$ list_images("AVAILABLE")
```

**200 Response**

```
{
  'images': [
    {
      'ec2_ami_info': {
        'ami_id': 'ami-abcdef01234567890'
      },
      'image_build_status': 'BUILD_COMPLETE',
      'image_id': 'custom-image',
      'region': 'us-east-1',
      'version': '3.2.1'
    }
  ]
}
```

------