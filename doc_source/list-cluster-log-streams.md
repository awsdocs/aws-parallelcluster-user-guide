# listClusterLogStreams<a name="list-cluster-log-streams"></a>

Retrieve the list of log streams that are associated with a cluster\.

**Topics**
+ [Request syntax](#list-cluster-log-streams-request)
+ [Request body](#list-cluster-log-streams-request-body)
+ [Response syntax](#list-cluster-log-streams-response)
+ [Response body](#list-cluster-log-streams-response-body)
+ [Example](#list-cluster-log-streams-example)

## Request syntax<a name="list-cluster-log-streams-request"></a>

```
GET /v3/clusters/{clusterName}/logstreams
{
  "filters": [ "string" ],
  "nextToken": "string",
  "region": "string"
}
```

## Request body<a name="list-cluster-log-streams-request-body"></a>

**clusterName**  
The name of the cluster\.  
Type: string  
Required: Yes

**filters**  
Filter the log streams\.  
Accepted filters are:  
+ private\-dns\-name: The short form of the private DNS name of the instance \(e\.g\. ip\-10\-0\-0\-101\)\.
+ node\-type: Valid value: `HeadNode`\.
Type: Array of strings unique  
Format: `Name=a,Values=1 Name=b,Values=2,3`  
Required: No

**nextToken**  
A token that's used for paginated requests\.  
Type: string  
Required: No

**region**  
The AWS Region that the cluster is in\.  
Type: string  
Required: No

## Response syntax<a name="list-cluster-log-streams-response"></a>

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

## Response body<a name="list-cluster-log-streams-response-body"></a>

**logStreams**  
A list of log streams\.    
**creationTime**  
The time when the stream was created\.  
Type: datetime  
**firstEventTimestamp**  
The time of the first event of the stream\.  
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
Name of the log stream\.  
Type: string  
**uploadSequenceToken**  
The sequence token\.  
Type: string

**nextToken**  
A token that's used for paginated requests\.  
Type: string

## Example<a name="list-cluster-log-streams-example"></a>

------
#### [ Python ]

**Request**

```
$ list_cluster_log_streams(cluster_name_3x)
```

**200 Response**

```
{
  'log_streams': [
      {
        'creation_time': datetime.datetime(2022, 3, 30, 14, 7, 34, 354000, tzinfo=tzlocal()),
        'first_event_timestamp': datetime.datetime(2022, 3, 30, 14, 6, 41, 444000, tzinfo=tzlocal()),
        'last_event_timestamp': datetime.datetime(2022, 3, 30, 14, 25, 55, 462000, tzinfo=tzlocal()),
        'last_ingestion_time': datetime.datetime(2022, 3, 30, 14, 49, 50, 62000, tzinfo=tzlocal()),
        'log_stream_arn': 'arn:aws:logs:us-east-1:123456789012:log-group:/aws/parallelcluster/cluster_name_3x:log-stream:ip-192-0-2-26.i-abcdef01234567890.cfn-init',
        'log_stream_name': 'ip-192-0-2-26.i-abcdef01234567890.cfn-init',
         ...
        'upload_sequence_token': '####'
      },
      ...
   ]
}
```

------