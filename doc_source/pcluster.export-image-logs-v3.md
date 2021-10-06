# `pcluster export-image-logs`<a name="pcluster.export-image-logs-v3"></a>

Export the logs of the image builder stack to a local `tar.gz` archive by passing through an Amazon S3 Bucket\.

```
pcluster export-image-logs [-h] [--debug] [--region REGION]
                           [--output-file OUTPUT_FILE]
                           [--bucket-prefix BUCKET_PREFIX]
                           [--keep-s3-objects KEEP_S3_OBJECTS]
                           [--start-time START_TIME]
                           [--end-time END_TIME] --image-id IMAGE_ID
                           --bucket BUCKET
```

## Named arguments<a name="pcluster-v3.export-image-logs.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster export-image-logs`\.

`--debug`  
Enables debug logging\.

`--region REGION`  
Specifies the AWS Region to use\. The Region must be specified, either using the `AWS_DEFAULT_REGION` environment variable or the `--region` parameter\.

`--output-file OUTPUT_FILE`  
Specifies the file path to save the log archive to\. If this is provided, then the logs are saved locally\. Otherwise they are uploaded to Amazon S3 with the URL returned in the output\. Default is to upload to Amazon S3\.

`--bucket-prefix BUCKET_PREFIX`  
Specifies prefix for the path under which exported logs data will be stored in the Amazon S3 bucket\.

`--keep-s3-objects KEEP_S3_OBJECTS`  
If `true`, the the exported objects exports to Amazon S3 are kept\. \(Defaults to `false`\.\)

`--start-time START_TIME`  
Specifies the start of the time range, expressed in ISO 8601 format \(`YYYY-MM-DDThh:mm:ssZ`, for example `2021-01-01T20:00:00Z`\)\. Log events with a timestamp equal to this time or later than this time are included\. If not specified, the default is the time the cluster was created\.

`--end-time END_TIME`  
Specifies the end of the time range to collect log events, expressed in ISO 8601 format \(`YYYY-MM-DDThh:mm:ssZ`, for example `2021-01-01T20:00:00Z`'\)\. Events with a timestamp equal to or later than this time are not included\. Time elements \(e\.g\. minutes and seconds\) may be omitted\. The default value is the current time\.

`--image-id IMAGE_ID`  
The image ID whose logs will be exported\.

`--bucket BUCKET`  
Specifies the Amazon S3 bucket to export image build logs to\. It must be in the same Region as the image\.