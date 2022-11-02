# getImageLogEvents<a name="get-image-log-events"></a>

Retrieve the events associated with an image build\.

**Topics**
+ [Request syntax](#get-image-log-events-request)
+ [Request body](#get-image-log-events-request-body)
+ [Response syntax](#get-image-log-events-response)
+ [Response body](#get-image-log-events-response-body)
+ [Example](#get-image-log-events-example)

## Request syntax<a name="get-image-log-events-request"></a>

```
GET /v3/images/custom/{imageId}/logstreams/{logStreamName}
{
  "endTime": datetime,
  "limit": float,
  "nextToken": "string",
  "region": "string",
  "startFromHead": boolean,
  "startTime": datetime
}
```

## Request body<a name="get-image-log-events-request-body"></a>

**imageId**  
The ID of the image\.  
Type: string  
Required: Yes

**logStreamName**  
The name of the logstream\.  
Type: string  
Required: Yes

**endTime**  
The end of the time range, expressed in ISO 8601 format\. Events with a timestamp equal to or later than this time are not included\.  
Type: datetime  
Format: `2021-01-01T20:00:00Z`  
Required: No

**limit**  
The maximum number of log events returned\. If you don't specify a value, the maximum is as many log events as can fit in a response size of 1 MB, up to 10,000 log events\.  
Type: float  
Required: No

**nextToken**  
A token to use for paginated requests\.  
Type: string  
Required: No

**region**  
The AWS Region that the action corresponds to\.  
Type: string  
Required: No

**startFromHead**  
If the value is true, return the earliest log events first\. If the value is false, return the latest log events first\. The default is `false`\.  
Type: bool  
Required: No

**startTime**  
The start of the time range, expressed in ISO 8601 format\. Events with a timestamp equal to this time or later than this time are included\.  
Type: datetime  
Format: `2021-01-01T20:00:00Z`  
Required: No

## Response syntax<a name="get-image-log-events-response"></a>

```
{
  "nextToken": "string",
  "prevToken": "string",
  "events": [
    {
      "timestamp": "2019-08-24T14:15:22Z",
      "message": "string"
    }
  ]
}
```

## Response body<a name="get-image-log-events-response-body"></a>

**events**  
A list of filtered events\.    
**message**  
The event message\.  
Type: string  
**timestamp**  
The event timestamp\.  
Type: datetime

**nextToken**  
A token to use for paginated requests\.  
Type: string

**prevToken**  
A token to use for paginated requests\.  
Type: string

## Example<a name="get-image-log-events-example"></a>

------
#### [ Python ]

**Request**

```
$ get_image_log_events(image_id, log_stream_name=3.2.1/1)
```

**200 Response**

```
"events": [
  {
    "message": "ExecuteBash: STARTED EXECUTION",
    "timestamp": 2022-04-05T15:51:20.228Z"
  },
  {
    "message": "ExecuteBash: Created temporary directory: /tmp/1234567890abcdef0",
    "timestamp": "2022-04-05T15:51:20.228Z"
  },
  ...
]
```

------