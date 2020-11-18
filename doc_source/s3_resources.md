# Working with Amazon S3<a name="s3_resources"></a>

You can access Amazon S3 from within AWS ParallelCluster\. You control the access to Amazon S3 through the [`s3_read_resource`](cluster-definition.md#s3-read-resource) and [`s3_read_write_resource`](cluster-definition.md#s3-read-write-resource) parameters in the AWS ParallelCluster configuration\.

```
# Specify Amazon S3 resource which AWS ParallelCluster nodes will be granted read-only access
# (defaults to NONE)
s3_read_resource = NONE
# Specify Amazon S3 resource which AWS ParallelCluster nodes will be granted read-write access
# (defaults to NONE)
s3_read_write_resource = NONE
```

Both parameters accept either `*` or a valid Amazon S3 ARN\. For information about specifying Amazon S3 ARNs, see [Amazon S3 ARN format](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-s3) in the *AWS General Reference*

## Examples<a name="examples"></a>

The following example gives you read access to any object in the Amazon S3 bucket *my\_corporate\_bucket*\.

```
s3_read_resource = arn:aws:s3:::my_corporate_bucket/*
```

This following example gives you read access to the bucket, but does **not** let you read items from the bucket\.

```
s3_read_resource = arn:aws:s3:::my_corporate_bucket
```

This last example gives you read access to the bucket and to the items stored in the bucket\.

```
s3_read_resource = arn:aws:s3:::my_corporate_bucket*
```