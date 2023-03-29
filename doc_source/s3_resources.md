# Working with Amazon S3<a name="s3_resources"></a>

To provide cluster resources permission to access to Amazon S3 buckets, specify the bucket ARNs in the [`s3_read_resource`](cluster-definition.md#s3-read-resource) and [`s3_read_write_resource`](cluster-definition.md#s3-read-write-resource) parameters in the AWS ParallelCluster configuration\. For more information about controlling access with AWS ParallelCluster, see [AWS Identity and Access Management roles in AWS ParallelCluster](iam.md)\.

```
# Specify Amazon S3 resource which AWS ParallelCluster nodes will be granted read-only access
# (no default)
s3_read_resource = arn:aws:s3:::my_corporate_bucket*
# Specify Amazon S3 resource which AWS ParallelCluster nodes will be granted read-write access
# (no default)
s3_read_write_resource = arn:aws:s3:::my_corporate_bucket/*
```

Both parameters accept either `*` or a valid Amazon S3 ARN\. For information about specifying Amazon S3 ARNs, see [Amazon S3 ARN format](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-s3) in the *AWS General Reference*\.

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