# `pcluster get-image-log-events`<a name="pcluster.get-image-log-events-v3"></a>

Retrieve the events associated with an image build\.

```
pcluster get-image-log-events [-h] --image-id IMAGE_ID
                              --log-stream-name LOG_STREAM_NAME
                              [--region REGION]
                              [--next-token NEXT_TOKEN]
                              [--start-from-head START_FROM_HEAD]
                              [--limit LIMIT] [--start-time START_TIME]
                              [--end-time END_TIME] [--debug]
                              [--query QUERY]
```

## Named arguments<a name="pcluster-v3.get-image-log-events.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster get-image-log-events`\.

`--image-id, -i IMAGE_ID`
Specifies the Id of the image\.

`--log-stream-name LOG_STREAM_NAME`  
Specifies the name of the log stream\.

`--region, -r REGION`  
Specifies the AWS Region to use\. The Region must be specified, using the `AWS_DEFAULT_REGION` environment variable, the `region` setting in the `[default]` section of the `~/.aws/config` file, or the `--region` parameter\.

`--next-token NEXT_TOKEN`  
Specifies the token to use for paginated requests\.

`--start-from-head START_FROM_HEAD`  
If the value is `true`, the earliest log events are returned first\. If the value is `false`, the most recent log events are returned first\. \(Defaults to `false`\.\)

`--limit LIMIT`  
Specifies the maximum number of log events returned\. If a value is not specified, the maximum is as many log events as can fit in a response size of 1 MB, up to 10,000 log events\.

`--start-time START_TIME`  
Specifies the start of the time range, expressed in ISO 8601 format \(`YYYY-MM-DDThh:mm:ssZ`, for example `2021-01-01T20:00:00Z`\)\. Events with a timestamp equal to or later than this time are included\.

`--end-time END_TIME`  
Specifies the end of the time range, expressed in ISO 8601 format \(`YYYY-MM-DDThh:mm:ssZ`, for example `2021-01-01T20:00:00Z`\)\. Events with a timestamp equal to or later than this time are not included\.

`--debug`  
Enables debug logging\.

`--query QUERY`  
Specifies the JMESPath query to perform on the output\.
