# listImageLogStreams<a name="list-image-log-streams"></a>

Retrieve the list of log streams that's associated with an image\.

**Topics**
+ [Request syntax](#list-image-log-streams-request)
+ [Request body](#list-image-log-streams-request-body)
+ [Response syntax](#list-image-log-streams-response)
+ [Response body](#list-image-log-streams-response-body)
+ [Example](#list-image-log-streams-example)

## Request syntax<a name="list-image-log-streams-request"></a>

```
GET /v3/images/custom/{imageId}/logstreams
{
  "nextToken": "string",
  "region": "string"
}
```

## Request body<a name="list-image-log-streams-request-body"></a>

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

## Response syntax<a name="list-image-log-streams-response"></a>

```
{
  "nextToken": "string",
  "logStreams": [
    {
      "logStreamName": "string",
      "creationTime": "2019-08-24T14:15:22Z",
      "firstEventTimestamp": "2019-08-24T14:15:22Z",
      "lastEventTimestamp": "2019-08-24T14:15:22Z",
      "lastIngestionTime": "2019-08-24T14:15:22Z",
      "uploadSequenceToken": "string",
      "logStreamArn": "string"
    }
  ]
}
```

## Response body<a name="list-image-log-streams-response-body"></a>

**logStreams**  
A list of log streams\.    
**creationTime**  
The time when the stream was created\.  
Type: datetime  
**firstEventTimestamp**  
The time of the first event in the stream\.  
Type: datetime  
**lastEventTimestamp**  
The time of the last event of the stream\. The lastEventTime value updates on an eventual consistency basis\. It typically updates in less than an hour from ingestion, but in rare situations might take longer\.  
Type: datetime  
**lastIngestionTime**  
The last ingestion time\.  
Type: datetime  
**logStreamArn**  
The Amazon Resource Name \(ARN\) of the log stream\.  
Type: string  
**logStreamName**  
The name of the log stream\.  
Type: string  
**uploadSequenceToken**  
The sequence token\.  
Type: string

**next\_token**  
A token that's used for paginated requests\.  
Type: string

## Example<a name="list-image-log-streams-example"></a>

------
#### [ Python ]

**Request**

```
$ list_image_log_streams(custom-image-id)
```

**200 Response**

```
{
  'log_streams': [
    {
      'creation_time': datetime.datetime(2022, 3, 29, 20, 29, 24, 875000, tzinfo=tzlocal()),
      'first_event_timestamp': datetime.datetime(2022, 3, 29, 20, 29, 24, 775000, tzinfo=tzlocal()),
      'last_event_timestamp': datetime.datetime(2022, 3, 29, 20, 38, 23, 944000, tzinfo=tzlocal()),
      'last_ingestion_time': datetime.datetime(2022, 3, 29, 20, 51, 56, 26000, tzinfo=tzlocal()),
      'log_stream_arn': 'arn:aws:logs:us-east-1:123456789012:log-group:/aws/imagebuilder/ParallelClusterImage-alinux2-image:log-stream:3.2.1/1',
      'log_stream_name': '3.2.1/1',
      'upload_sequence_token': '####'
    },
    ...
  ]
}
```

------