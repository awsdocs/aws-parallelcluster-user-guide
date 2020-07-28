# Setting up a custom AWS ParallelCluster cookbook<a name="custom_cookbook"></a>

**Important**  
The following are instructions for using a custom version of the AWS ParallelCluster cookbook recipes\. This is an advanced method of customizing AWS ParallelCluster, with potential issues that can be hard to debug\. The AWS ParallelCluster team highly recommends using the scripts in [Custom Bootstrap Actions](pre_post_install.md) for customization, because post\-install hooks are generally easier to debug and more portable across releases of AWS ParallelCluster\.

## Steps<a name="steps"></a>

1. Identify the AWS ParallelCluster Cookbook working directory where you have cloned the [AWS ParallelCluster cookbook](https://github.com/aws/aws-parallelcluster-cookbook) code\.

   ```
   _cookbookDir=<path to cookbook>
   ```

1. Detect the current version of the AWS ParallelCluster Cookbook\.

   ```
   _version=$(grep version ${_cookbookDir}/metadata.rb|awk '{print $2}'| tr -d \')
   ```

1. Create an archive of the AWS ParallelCluster Cookbook and calculate its md5\.

   ```
   cd "${_cookbookDir}"
   _stashName=$(git stash create)
   git archive --format tar --prefix="aws-parallelcluster-cookbook-${_version}/" "${_stashName:-HEAD}" | gzip > "aws-parallelcluster-cookbook-${_version}.tgz"
   md5sum "aws-parallelcluster-cookbook-${_version}.tgz" > "aws-parallelcluster-cookbook-${_version}.md5"
   ```

1. Create an Amazon S3 bucket and upload the archive, its md5, and its last modified date into the bucket\. Give public readable permission through a public\-read ACL\.

   ```
   _bucket=<the bucket name>
   aws s3 cp --acl public-read aws-parallelcluster-cookbook-${_version}.tgz s3://${_bucket}/cookbooks/aws-parallelcluster-cookbook-${_version}.tgz
   aws s3 cp --acl public-read aws-parallelcluster-cookbook-${_version}.md5 s3://${_bucket}/cookbooks/aws-parallelcluster-cookbook-${_version}.md5
   aws s3api head-object --bucket ${_bucket} --key cookbooks/aws-parallelcluster-cookbook-${_version}.tgz --output text --query LastModified > aws-parallelcluster-cookbook-${_version}.tgz.date
   aws s3 cp --acl public-read aws-parallelcluster-cookbook-${_version}.tgz.date s3://${_bucket}/cookbooks/aws-parallelcluster-cookbook-${_version}.tgz.date
   ```

1. Add the following variables to the AWS ParallelCluster configuration file, under the [`[cluster]` section](cluster-definition.md)\.

   ```
   custom_chef_cookbook = https://${_bucket}.s3.<the bucket region>.amazonaws.com/cookbooks/aws-parallelcluster-cookbook-${_version}.tgz
   extra_json = { "cluster" : { "skip_install_recipes" : "no" } }
   ```
**Note**  
Starting with AWS ParallelCluster 2\.6\.1, most of the install recipes are skipped by default when launching nodes to improve start up times\. To skip most of the install recipes for better start up times at the expense of backwards compatibility, remove `"skip_install_recipes" : "no"` from the `cluster` key in the [`extra_json`](cluster-definition.md#extra-json) setting\.