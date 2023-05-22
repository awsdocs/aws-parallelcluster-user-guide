# AWS ParallelCluster Python library API<a name="pc-py-library-v3"></a>

Starting with AWS ParallelCluster version 3\.5\.0, you can access AWS ParallelCluster with the AWS ParallelCluster Python library\. You can access the AWS ParallelCluster library in your `pcluster` environment or from within an AWS Lambda runtime\. Learn how to access the AWS ParallelCluster API by using the AWS ParallelCluster Python library\. The AWS ParallelCluster Python library offers the same functionality that the AWS ParallelCluster API delivers\.

The AWS ParallelCluster Python library operations and parameters mirror those of the API parameters when converted to `snake_case` with no capital letters\.

**Topics**
+ [AWS ParallelCluster Python library authorization](#pc-py-lib-auth)
+ [Install the AWS ParallelCluster Python library](#pc-py-lib-install)
+ [Cluster API operations](pc-py-lib-api-cluster.md)
+ [Compute fleet API operations](pc-py-lib-api-fleet.md)
+ [Cluster and stack log operations](pc-py-lib-api-logs-cluster-stack.md)
+ [Image API operations](pc-py-lib-api-image.md)
+ [Image and stack log operations](pc-py-lib-api-logs-image-stack.md)
+ [Example](pc-py-lib-api-examples.md)
+ [AWS Lambda for the AWS ParallelCluster Python library](#lambda-py-v3)

## AWS ParallelCluster Python library authorization<a name="pc-py-lib-auth"></a>

Specify credentials by using any of the standard ways that are valid for boto3\. For more information, see the [boto3 documentation](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/quickstart.html#configuration)\.

## Install the AWS ParallelCluster Python library<a name="pc-py-lib-install"></a>

1. Install `pcluster` CLI version 3\.5\.0 or later by following the instructions given in [Setting up AWS ParallelCluster](install-v3.md)\.

1. Import the `pcluster` module and start using the library, as shown in the following example:

   ```
   import pcluster.lib as pc
   pc.create_cluster(cluster_name="mycluster", cluster_configuration="config.yaml"
   ```

## AWS Lambda for the AWS ParallelCluster Python library<a name="lambda-py-v3"></a>

You can deploy a Lambda layer and runtime to access to the AWS ParallelCluster Python library\. We host AWS ParallelCluster zip files that you can use by entering the link to the zip file as described in the following steps\. Lambda uses the zip files to prepare the runtime environment to support access to the Python library\. The AWS ParallelCluster Python library is added with AWS ParallelCluster version 3\.5\.0\. You can only use the library for versions 3\.5\.0 and later\.

The hosted zip file URL is in the format: `s3://aws-region-id-aws-parallelcluster/parallelcluster/3.6.0/layers/aws-parallelcluster/lambda-layer.zip`

### Get started accessing the AWS ParallelCluster Python library with AWS Lambda<a name="lambda-py-get-started-v3"></a>

**Create a Lambda layer**

1. Log in to the AWS Management Console and navigate to the AWS Lambda console\.

1. In the navigation pane, select **Layers**, then **Create layer**\.

1. Enter a name for your layer and select **Upload a file from Amazon S3**\.

1. Enter the URL to the zip file: s3://*aws\-region\-id*\-aws\-parallelcluster/parallelcluster/*3\.6\.0*/layers/aws\-parallelcluster/lambda\-layer\.zip\.

1. For **Compatible architectures**, choose the **x86\_64** architecture\.

1. For **Compatible runtimes**, choose the **Python 3\.9** runtime\.

1. Choose **Create**\.

**Use your Lambda layer**

1. In the Lambda console navigation pane, select **Functions**, then **Create function**\.

1. Enter a name for your function\.

1. For **Runtime**, choose the **Python 3\.9** runtime\.

1. For **Architecture**, choose the **x86\_64** architecture\.

1. Choose **Create function**\.

1. After the function is created, choose **Layers** and select **Add a layer**\.

1. Select **Custom layers** and choose the layer you created in previous steps\.

1. Choose the layer version\.

1. Choose **Add**\.

1. Your Lambda needs permissions to manage clusters created with AWS ParallelCluster\. Create a Lambda role with the permissions listed in [Base AWS ParallelCluster` pcluster` user policy](iam-roles-in-parallelcluster-v3.md#iam-roles-in-parallelcluster-v3-base-user-policy)\.

You can now access AWS ParallelCluster from the Python library as described in [AWS ParallelCluster Python library API](#pc-py-library-v3)\.