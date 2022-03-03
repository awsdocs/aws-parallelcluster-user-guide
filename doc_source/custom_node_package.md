# Setting up a custom AWS ParallelCluster node package<a name="custom_node_package"></a>

**Warning**  
The following are instructions for using a custom version of the AWS ParallelCluster node package\. This is an advanced method of customizing AWS ParallelCluster, with potential issues that can be hard to debug\. The AWS ParallelCluster team highly recommends using the scripts in [Custom Bootstrap Actions](pre_post_install.md) for customization, because post\-install hooks are generally easier to debug and more portable across releases of AWS ParallelCluster\.

## Steps<a name="steps"></a>

1. Identify the AWS ParallelCluster node working directory where you have cloned the AWS ParallelCluster node code\.

   ```
   _nodeDir=<path to node package>
   ```

1. Detect the current version of the AWS ParallelCluster node\.

   ```
   _version=$(grep "version = \"" ${_nodeDir}/setup.py |awk '{print $3}' | tr -d \")
   ```

1. Create an archive of the AWS ParallelCluster Node\.

   ```
   cd "${_nodeDir}"
   _stashName=$(git stash create)
   git archive --format tar --prefix="aws-parallelcluster-node-${_version}/" "${_stashName:-HEAD}" | gzip > "aws-parallelcluster-node-${_version}.tgz"
   ```

1. Create an Amazon S3 bucket and upload the archive into the bucket\. Give public readable permission through a public\-read ACL\.

   ```
   _bucket=<the bucket name>
   aws s3 cp --acl public-read aws-parallelcluster-node-${_version}.tgz s3://${_bucket}/node/aws-parallelcluster-node-${_version}.tgz
   ```

1. Add the following variable to the AWS ParallelCluster configuration file, under the [`[cluster]` section](cluster-definition.md)\.

   ```
   extra_json = { "cluster" : { "custom_node_package" : "https://${_bucket}.s3.<the bucket region>.amazonaws.com/node/aws-parallelcluster-node-${_version}.tgz", "skip_install_recipes" : "no" } }
   ```
**Note**  
Starting with AWS ParallelCluster version 2\.6\.1, most of the install recipes are skipped by default when launching nodes to improve startup times\. To skip most of the install recipes for better startup times at the expense of backwards compatibility, remove `"skip_install_recipes" : "no"` from the `cluster` key in the [`extra_json`](cluster-definition.md#extra-json) setting\.