# Building a Custom AWS ParallelCluster AMI<a name="building-custom-ami-v3"></a>

**Important**  
If you build a custom AMI, you must repeat the steps that you used to create your custom AMI with each new AWS ParallelCluster release\.

Before reading any further, we recommend that you first check out the [Custom Bootstrap Actions](custom-bootstrap-actions-v3.md) section to determine if the modifications you want to make can be scripted and supported with future AWS ParallelCluster releases\.

Even though building a custom AMI isn't ideal, there are still scenarios where building a custom AMI for AWS ParallelCluster is necessary\. This tutorial guides you through the process of building a custom AMI for these scenarios\.

## How to Customize the AWS ParallelCluster AMI<a name="how-to-customize-the-aws-parallelcluster-ami"></a>

There are two ways to build a custom AWS ParallelCluster AMI\. One of these two methods is to build a new AMI using the AWS ParallelCluster CLI\. Another method requires you to build a new AMI that's available under your AWS account manually\.

**Topics**
+ [Build a Custom AWS ParallelCluster AMI](#build-a-custom-aws-parallelcluster-ami)
+ [Modify an AWS ParallelCluster AMI](#modify-an-aws-parallelcluster-ami)

### Build a Custom AWS ParallelCluster AMI<a name="build-a-custom-aws-parallelcluster-ami"></a>

If you have a customized AMI and software already in place, you can apply the changes needed by AWS ParallelCluster on top of it\. AWS ParallelCluster relies on the EC2 Image Builder service to build customized AMIs\. For more information, see [Image Builder User Guide](https://docs.aws.amazon.com/imagebuilder/latest/userguide/what-is-image-builder.html)

Key points:
+ The process will take about 1 hour\. This time can vary if there are additional [`Build > Components`](Build-v3.md#Build-v3-Components)to be installed at build time\.
+ The AMI will be tagged with the version of the main components, e\.g\. kernel, scheduler, EFA driver, etc\., and a subset of them will be also reported in the AMI description\.
+ Starting from AWS ParallelCluster 3\.0\.0 a new set of CLI commands can be used manage the whole life\-cycle of the images, with `build-image`, `list-images`, `describe-image` and `delete-image` commands\.
+ This method is repeatable, you can re\-execute it to keep AMIs updated \(e\.g\. OS updates\) and use the new images during an update of existing cluster\.

Steps:

1. Configure your AWS account credentials so that AWS ParallelCluster client can make calls to AWS API operations on your behalf\. The set of required permissions are documented in the [AWS Identity and Access Management roles in AWS ParallelCluster 3\.x](iam-roles-in-parallelcluster-v3.md)\.

1. Create a basic image\-builder configuration file, by specifying `InstanceType` to be used to build the Image and the `ParentImage` to be used as starting point for AMI creation \(see [Image Configuration](image-builder-configuration-file-v3.md) for other optional build parameters\)\.

   ```
   Build:
     InstanceType: <BUILD_INSTANCE_TYPE>
     ParentImage: <BASE_AMI_ID>
   ```

1. Use the command [`pcluster build-image`](pcluster.build-image-v3.md) to build an AWS ParallelCluster AMI starting from the one that you provide as base:

   ```
   pcluster build-image --image-id IMAGE_ID --image-configuration IMAGE_CONFIG.yaml --region REGION
   ```
**Warning**  
`pcluster build-image` uses the default VPC\. If the default VPC has been deleted, perhaps by using AWS Control Tower or AWS Landing Zone, then the subnet ID must be specified in the image configuration file\. For more information, see [SubnetId](HeadNode-v3.md#yaml-HeadNode-Networking-SubnetId)\.

   For other parameters, see pcluster build\-image command\. The previous command will do the following:
   + Create a CloudFormation stack based on the image configuration\. The stack will create all the EC2 ImageBuilder resources required for the build\.
   + Among the created resources, there will be the official ImageBuilder AWS ParallelCluster components on top of which custom ImageBuilder components can be specified\.
   + The EC2 ImageBuilder launches a build instance where to apply the AWS ParallelCluster cookbook and install the AWS ParallelCluster software stack and perform the necessary configuration tasks\.
   + The instance will be stopped and a new AMI will be created from it\.
   + Another instances will be launched from the newly created AMI\. During the test phase, EC2 ImageBuilder will runs tests eventually defined in the ImageBuilder components
   + If the build is successful, the stack will be deleted\. If build fails, the stack will be retained to be available for inspection\.

1. You can monitor the status of the build process executing the following command and retrieve the AMI id once the build complete\.

   ```
   pcluster describe-image --image-id IMAGE_ID --region REGION
   ```

1. To create your cluster, enter the AMI ID in the [`CustomAmi`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-Image-CustomAmi) field within your cluster configuration\.

**Troubleshooting and monitoring AMI creation process**

Image creation should take about 1 hour\. You can monitor the status of the build process executing the [`pcluster describe-image`](pcluster.describe-image-v3.md) command or follow the progresses with the logs retrieval commands\.

```
pcluster describe-image --image-id IMAGE_ID --region REGION
```

As previously mentioned, the `build-image` command creates a AWS CloudFormation stack with all the EC2 resources required to build the image and launches an EC2 Image Builder process\.

Right after the execution of the `build-image` command, it is possible to retrieve CloudFormation stack events by using [`pcluster get-image-stack-events`](pcluster.get-image-stack-events-v3.md) \(you can filter results with the `--query` parameter to see the latest events\. For more information about filtering, see [Filtering AWS CLI output](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-filter.html) in the *AWS Command Line Interface User Guide*\.

```
$ pcluster get-image-stack-events --image-id IMAGE_ID --region REGION --query "events[0]"
{
  "eventId": "ParallelClusterImage-CREATE_IN_PROGRESS-2021-07-28T08:41:46.626Z",
  "physicalResourceId": "arn:aws:imagebuilder:eu-west-1:xxx:image/parallelclusterimage-myimage/3.1.2/1",
  "resourceStatus": "CREATE_IN_PROGRESS",
  "resourceStatusReason": "Resource creation Initiated",
  "resourceProperties": "{\"InfrastructureConfigurationArn\":\"arn:aws:imagebuilder:eu-west-1:xxx:infrastructure-configuration/parallelclusterimage-3cbb8760-ef7f-11eb-a4c0-02abee05253b\",\"ImageRecipeArn\":\"arn:aws:imagebuilder:eu-west-1:xxx:image-recipe/parallelclusterimage-myimage/3.1.2\",\"DistributionConfigurationArn\":\"arn:aws:imagebuilder:eu-west-1:xxx:distribution-configuration/parallelclusterimage-3cbb8760-ef7f-11eb-a4c0-02abee05253b\",\"Tags\":{\"parallelcluster:image_name\":\"myimage\",\"parallelcluster:image_id\":\"myimage\"}}",
  "stackId": "arn:aws:cloudformation:eu-west-1:xxx:stack/alinux2-image/3cbb8760-ef7f-11eb-a4c0-02abee05253b",
  "stackName": "myimage",
  "logicalResourceId": "ParallelClusterImage",
  "resourceType": "AWS::ImageBuilder::Image",
  "timestamp": "2021-07-28T08:41:46.626Z"
}
```

After about 15 minutes the stack events will appear the log event entry related to Image Builder process creation\. From this moment it is possible to list image log streams and monitor the Image Builder steps by using [`pcluster list-image-log-streams`](pcluster.list-image-log-streams-v3.md) and [`pcluster get-image-log-events`](pcluster.get-image-log-events-v3.md) commands\.

```
$ pcluster list-image-log-streams --image-id IMAGE_ID --region REGION \
     --query 'logStreams[*].logStreamName'
[
  "3.1.2/1"
]

$ pcluster get-image-log-events --image-id IMAGE_ID --region REGION \
 --log-stream-name 3.1.2/1 --limit 3
{
  "nextToken": "f/36295977202298886557255241372854078762600452615936671762",
  "prevToken": "b/36295977196879805474012299949460899222346900769983430672",
  "events": [
    {
      "message": "ExecuteBash: FINISHED EXECUTION",
      "timestamp": "2021-07-29T16:11:47.429Z"
    },
    {
      "message": "Document arn:aws:imagebuilder:eu-west-1:xxx:component/parallelclusterimage-test-c6200fa0-f072-11eb-b9ab-063c2565c5c1/3.1.2/1",
      "timestamp": "2021-07-29T16:11:47.570Z"
    },
    {
      "message": "TOE has completed execution successfully",
      "timestamp": "2021-07-29T16:11:47.672Z"
    }
  ]
}
```

Continue to check with `describe-image` command until you’ll see the `BUILD_COMPLETE` status\.

```
$ pcluster describe-image --image-id IMAGE_ID --region REGION
{
  "imageConfiguration": {
    "url": "https://parallelcluster-ae5a9f1a011e9d83-v1-do-not-delete.s3.eu-west-1.amazonaws.com/parallelcluster/3.1.2/images/alinux2-0729-r13st0t82cuwjvko/configs/image-config.yaml?Signature=..."
  },
  "imageId": "alinux2-0729",
  "imagebuilderImageStatus": "AVAILABLE",
  "imageBuildStatus": "BUILD_COMPLETE",
  ...
  "ec2AmiInfo": {
      "amiName": "alinux2-0729 2021-07-29T13-45-08.518Z",
      "amiId": "ami-05fc2f48ba82e643b",
      "description": "AWS ParallelCluster AMI for alinux2, kernel-4.14.238-182.422.amzn2.x86_64, lustre-2.10.8-5.amzn2.x86_64, efa-1.13.0-1.amzn2.x86_64, dcv-2021.1.10598-1.el7.x86_64, slurm-20-11-8-1",
      "state": "AVAILABLE",
}
```

If you need help to debug the AMI creation issue, you can create an archive of Image logs\. See the following steps to start this process\.

It is possible to archive the logs in S3 or in a local file \(depending on the `--output` parameter\)\.

```
$ pcluster export-image-logs --image-id IMAGE_ID --region REGION \
 --bucket BUCKET_NAME --bucket-prefix BUCKET_FOLDER
{
  "url": "https://bucketname.s3.eu-west-1.amazonaws.com/BUCKET-FOLDER/myimage-logs-202109071136.tar.gz?AWSAccessKeyId=..."
}

$ pcluster export-image-logs --image-id IMAGE_ID \
 --region REGION --bucket BUCKET_NAME --bucket-prefix BUCKET_FOLDER --output-file /tmp/archive.tar.gz
{
  "path": "/tmp/archive.tar.gz"
}
```

The archive contains the CloudWatch Logs Streams related to the Image Builder process and AWS CloudFormation stack events\. Please note the command execution may take some minutes\.

 **Managing Custom AMIs** 

Starting from AWS ParallelCluster 3\.0\.0 a new set of commands have been added in the CLI to build, monitor and manage Image lifecycle\. Please take a look at [pcluster commands](pcluster-v3.md#pcluster.subcommands-v3) for more information about Image related commands\.

### Modify an AWS ParallelCluster AMI<a name="modify-an-aws-parallelcluster-ami"></a>

This method consists on modifying an official AWS ParallelCluster AMIs by adding customization on top of it\. The base AWS ParallelCluster AMIs are updated with new releases, this AMI has all of the components required for AWS ParallelCluster to function when it's installed and configured\. You can start with this as the base\.

Key points:
+ This method is faster than the `build-image` command but it is a manual process and not repeatable\.
+ With this method you’ll lose all the advantages of the logs retrieval and image lifecycle management commands available through the CLI\.

Steps:

1. Find the AMI that corresponds to the specific region that you use\. To find it you can use the command [`pcluster list-official-images`](pcluster.list-official-images-v3.md) command with the `--region` parameter to select the specific region and `--os` and `--architecture` parameters to filter out the desired AMI with OS and architecture you want to use\. From the output you can retrieve the EC2 Image Id\.

1. Within the Amazon EC2/Images/AMIs console, search for the retrieved Ec2 Image Id, click **Actions** and choose **Launch**\.

1. Choose your instance type and properties, and launch your instance\.

1. Log into your instance using the OS user and your SSH key\.

1. Customize your instance as required\.

1. Run the following command to prepare your instance for AMI creation:

   ```
   sudo /usr/local/sbin/ami_cleanup.sh
   ```

1. Stop the instance\.

1. Create a new AMI from the instance\.

1. Enter the new AMI id in the [`CustomAmi`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-Image-CustomAmi) field within your cluster configuration\.