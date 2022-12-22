# getImageStackEvents<a name="get-image-stack-events"></a>

Retrieve the events that are associated with the stack for an image build\.

**Topics**
+ [Request syntax](#get-image-stack-events-request)
+ [Request body](#get-image-stack-events-request-body)
+ [Response syntax](#get-image-stack-events-response)
+ [Response body](#get-image-stack-events-response-body)
+ [Example](#get-image-stack-events-example)

## Request syntax<a name="get-image-stack-events-request"></a>

```
GET /v3/images/custom/{imageId}/stackevents
{
  "nextToken": "string",
  "region": "string"
}
```

## Request body<a name="get-image-stack-events-request-body"></a>

**imageId**  
The ID of the image\.  
Type: string  
Required: Yes

**nextToken**  
A token that's used for paginated requests\.  
Type: string  
Required: No

**region**  
The AWS Region that the image is in\.  
Type: string  
Required: No

## Response syntax<a name="get-image-stack-events-response"></a>

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

## Response body<a name="get-image-stack-events-response-body"></a>

**events**  
A list of filtered events\.    
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

## Example<a name="get-image-stack-events-example"></a>

------
#### [ Python ]

**Request**

```
$ get_image_stack_events(image_id)
```

**200 Response**

```
{
  'events': [
    {
      'event_id': 'ParallelClusterImage-CREATE_IN_PROGRESS-2022-03-30T23:26:33.499Z',
      'logical_resource_id': 'ParallelClusterImage',
      'physical_resource_id': 'arn:aws:imagebuilder:us-east-1:123456789012:image/parallelclusterimage-alinux2-image/3.2.1/1',
      'resource_properties': {
        "InfrastructureConfigurationArn":"arn:aws:imagebuilder:us-east-1:123456789012:infrastructure-configuration/parallelclusterimage-6accc570-b080-11ec-845e-0e2dc6386985",
        "ImageRecipeArn":"arn:aws:imagebuilder:us-east-1:123456789012:image-recipe/parallelclusterimage-alinux2-image/3.2.1",
        "DistributionConfigurationArn":"arn:aws:imagebuilder:us-east-1:123456789012:distribution-configuration/parallelclusterimage-6accc570-b080-11ec-845e-0e2dc6386985",
        "EnhancedImageMetadataEnabled":"false",
        "Tags": {
          "parallelcluster:image_name":"alinux2-image","parallelcluster:image_id":"alinux2-image"
        }
      },
      'resource_status': 'CREATE_IN_PROGRESS',
      'resource_status_reason': 'Resource creation Initiated',
      'resource_type': 'AWS::ImageBuilder::Image',
      'stack_id': 'arn:aws:cloudformation:us-east-1:123456789012:stack/alinux2-image/6accc570-b080-11ec-845e-0e2dc6386985',
      'stack_name': 'alinux2-image',
      'timestamp': datetime.datetime(2022, 3, 30, 23, 26, 33, 499000, tzinfo=tzlocal())
    },
    ...
  ]
}
```

------