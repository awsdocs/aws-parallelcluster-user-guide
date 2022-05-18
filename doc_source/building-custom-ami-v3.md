# Building a Custom AWS ParallelCluster AMI<a name="building-custom-ami-v3"></a>

**Important**  
If you build a custom AMI, you must repeat the steps that you used to create your custom AMI with each new AWS ParallelCluster release\.

Before reading any further, we recommend that you first review the [Custom Bootstrap Actions](custom-bootstrap-actions-v3.md) section to determine if the modifications you want to make can be scripted and supported with future AWS ParallelCluster releases\.

Even though building a custom AMI isn't ideal, there are scenarios where building a custom AMI for AWS ParallelCluster is necessary\. This tutorial guides you through the process of building a custom AMI for these scenarios\.

## How to Customize the AWS ParallelCluster AMI<a name="how-to-customize-the-aws-parallelcluster-ami"></a>

There are two ways to build a custom AWS ParallelCluster AMI\. One of these two methods is to build a new AMI using the AWS ParallelCluster CLI\. Another method requires you to make manual modifications to build a new AMI that's available under your AWS account\.

**Topics**
+ [Build a Custom AWS ParallelCluster AMI](#build-a-custom-aws-parallelcluster-ami)
+ [Modify an AWS ParallelCluster AMI](#modify-an-aws-parallelcluster-ami)

### Build a Custom AWS ParallelCluster AMI<a name="build-a-custom-aws-parallelcluster-ami"></a>

If you have a customized AMI and software already in place, you can apply the changes needed by AWS ParallelCluster on top of it\. AWS ParallelCluster relies on the EC2 Image Builder service to build customized AMIs\. For more information, see the [Image Builder User Guide](https://docs.aws.amazon.com/imagebuilder/latest/userguide/what-is-image-builder.html)\.

Key points:
+ The process takes about 1 hour\. This time can vary if there are additional `Build` / `Components` to be installed at build time\.
+ The AMI is tagged with the versions of the main components, such as the kernel, scheduler, and [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) driver\. A subset of the component versions are also reported in the AMI description\.
+ Starting from AWS ParallelCluster 3\.0\.0, a new set of CLI commands can be used to manage the life\-cycle of images, including `build-image`, `list-images`, `describe-image` and `delete-image`\.
+ This method is repeatable\. You can re\-run it to keep AMIs updated, such as OS updates, and then use them when you update an existing cluster\.

Steps:

1. Configure your AWS account credentials so that the AWS ParallelCluster client can make calls to AWS API operations on your behalf\. The set of required permissions are documented in [AWS Identity and Access Management roles in AWS ParallelCluster 3\.x](iam-roles-in-parallelcluster-v3.md)\.

1. Create a basic *build image* configuration file, by specifying the `InstanceType` to be used to build the image and the `ParentImage` to be used as the starting point for AMI creation\. For more information on optional build parameters, see [Image Configuration](image-builder-configuration-file-v3.md)\.

   ```
   Build:
     InstanceType: <BUILD_INSTANCE_TYPE>
     ParentImage: <BASE_AMI_ID>
   ```

1. Use the CLI command `pcluster build\-image` to build an AWS ParallelCluster AMI starting from the AMI that you provide as the base:

   ```
   $ pcluster build-image --image-id IMAGE_ID --image-configuration IMAGE_CONFIG.yaml --region REGION
        {
     "image": {
       "imageId": "IMAGE_ID",
       "imageBuildStatus": "BUILD_IN_PROGRESS",
       "cloudformationStackStatus": "CREATE_IN_PROGRESS",
       "cloudformationStackArn": "arn:aws:cloudformation:us-east-1:123456789012:stack/IMAGE_ID/abcd1234-ef56-gh78-ij90-1234abcd5678",
       "region": "us-east-1",
       "version": "3.1.4"
     }
   }
   ```
**Warning**  
`pcluster build-image` uses the default VPC\. If the default VPC has been deleted, perhaps by using AWS Control Tower or AWS Landing Zone, the subnet ID must be specified in the image configuration file\. For more information, see [SubnetId](HeadNode-v3.md#yaml-HeadNode-Networking-SubnetId)\.

   For other parameters, see the `pcluster build\-image` command\. The previous command does the following:
   + Creates a CloudFormation stack based on the image configuration\. The stack includes all of the EC2 Image Builder resources required for the build\.
   + Among the created resources are the official Image Builder AWS ParallelCluster components on top of which custom Image Builder components can be added\. To learn how to create custom components, run through the [Custom AMIs examples](https://catalog.prod.workshops.aws/workshops/e2f40d13-8082-4718-909b-6cdc3155ae41/examples/custom-ami) in the *HPC for Public Sector Customers Workshop*\.
   + EC2 Image Builder launches a build instance, applies the AWS ParallelCluster cookbook, installs the AWS ParallelCluster software stack, and performs necessary configuration tasks\. The AWS ParallelCluster cookbook is used to build and bootstrap AWS ParallelCluster\.
   + The instance is stopped and a new AMI is created from it\.
   + Another instance is launched from the newly created AMI\. During the test phase, EC2 Image Builder runs tests that are defined in the Image Builder components\.
   + If the build is successful, the stack is deleted\. If the build fails, the stack is retained and available for inspection\.

1. You can monitor the status of the build process by running the following command\. After the build completes, you can run it to retrieve the AMI ID given in the response\.

   ```
   $ pcluster describe-image --image-id IMAGE_ID --region REGION
        
   # BEFORE COMPLETE
   {
     "imageConfiguration": {
       "url": "https://parallelcluster-1234abcd5678efgh-v1-do-not-delete.s3.amazonaws.com/parallelcluster/3.1.4/images/IMAGE_ID-abcd1234efgh5678/configs/image-config.yaml?...",
     },
     "imageId": "IMAGE_ID",
     "imagebuilderImageStatus": "BUILDING",
     "imageBuildStatus": "BUILD_IN_PROGRESS",
     "cloudformationStackStatus": "CREATE_IN_PROGRESS",
     "cloudformationStackArn": "arn:aws:cloudformation:us-east-1:123456789012:stack/IMAGE_ID/abcd1234-ef56-gh78-ij90-1234abcd5678",
     "region": "us-east-1",
     "version": "3.1.4",
     "cloudformationStackTags": [
       {
         "value": "3.1.4",
         "key": "parallelcluster:version"
       },
       {
         "value": "IMAGE_ID",
         "key": "parallelcluster:image_name"
       },
       ...
     ],
     "imageBuildLogsArn": "arn:aws:logs:us-east-1:123456789012:log-group:/aws/imagebuilder/ParallelClusterImage-IMAGE_ID",
     "cloudformationStackCreationTime": "2022-04-05T21:36:26.176Z"
   }
   
   # AFTER COMPLETE
   {
     "imageConfiguration": {
       "url": "https://parallelcluster-1234abcd5678efgh-v1-do-not-delete.s3.us-east-1.amazonaws.com/parallelcluster/3.1.4/images/IMAGE_ID-abcd1234efgh5678/configs/image-config.yaml?Signature=..."
     },
     "imageId": "IMAGE_ID",
     "imageBuildStatus": "BUILD_COMPLETE",
     "region": "us-east-1",
     "ec2AmiInfo": {
         "amiName": "IMAGE_ID 2022-04-05T21-39-24.020Z",
         "amiId": "ami-1234stuv5678wxyz",
         "description": "AWS ParallelCluster AMI for alinux2, kernel-4.14.238-182.422.amzn2.x86_64, lustre-2.10.8-5.amzn2.x86_64, efa-1.13.0-1.amzn2.x86_64, dcv-2021.1.10598-1.el7.x86_64, slurm-20-11-8-1",
         "state": "AVAILABLE",
         "tags": [
          {
            "value": "2021.3.11591-1.el7.x86_64",
            "key": "parallelcluster:dcv_version"
          },
          ...
         ],
       "architecture": "x86_64"
     },
     "version": "3.1.4"      
   }
   ```

1. To create your cluster, enter the AMI ID in the `CustomAmi` field in your cluster configuration\.

**Troubleshooting and monitoring AMI creation process**

Image creation takes about 1 hour\. You can monitor the status of the build process by running the `pcluster describe\-image` command or log retrieval commands\.

```
$ pcluster describe-image --image-id IMAGE_ID --region REGION
```

As previously mentioned, the `build-image` command creates a CloudFormation stack with all the EC2 resources required to build the image, and launches the EC2 Image Builder process\.

After running the `build-image` command, it's possible to retrieve CloudFormation stack events by using `pcluster get\-image\-stack\-events`\. You can filter results with the `--query` parameter to see the latest events\. For more information, see [Filtering AWS CLI output](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-filter.html) in the *AWS Command Line Interface User Guide*\.

```
$ pcluster get-image-stack-events --image-id IMAGE_ID --region REGION --query "events[0]"
{
  "eventId": "ParallelClusterImage-CREATE_IN_PROGRESS-2022-04-05T21:39:24.725Z",
  "physicalResourceId": "arn:aws:imagebuilder:us-east-1:123456789012:image/parallelclusterimage-IMAGE_ID/3.1.4/1",
  "resourceStatus": "CREATE_IN_PROGRESS",
  "resourceStatusReason": "Resource creation Initiated",
  "resourceProperties": "{\"InfrastructureConfigurationArn\":\"arn:aws:imagebuilder:us-east-1:123456789012:infrastructure-configuration/parallelclusterimage-abcd1234-ef56-gh78-ij90-1234abcd5678\",\"ImageRecipeArn\":\"arn:aws:imagebuilder:us-east-1:123456789012:image-recipe/parallelclusterimage-IMAGE_ID/3.1.4\",\"DistributionConfigurationArn\":\"arn:aws:imagebuilder:us-east-1:123456789012:distribution-configuration/parallelclusterimage-abcd1234-ef56-gh78-ij90-1234abcd5678\",\"Tags\":{\"parallelcluster:image_name\":\"IMAGE_ID\",\"parallelcluster:image_id\":\"IMAGE_ID\"}}",
  "stackId": "arn:aws:cloudformation:us-east-1:123456789012:stack/IMAGE_ID/abcd1234-ef56-gh78-ij90-1234abcd5678",
  "stackName": "IMAGE_ID",
  "logicalResourceId": "ParallelClusterImage",
  "resourceType": "AWS::ImageBuilder::Image",
  "timestamp": "2022-04-05T21:39:24.725Z"
}
```

After about 15 minutes the stack events appear in the log event entry related to Image Builder creation\. You can now list image log streams and monitor the Image Builder steps by using `pcluster list\-image\-log\-streams` and `pcluster get\-image\-log\-events` commands\.

```
$ pcluster list-image-log-streams --image-id IMAGE_ID --region REGION \
     --query 'logStreams[*].logStreamName'

  "3.1.4/1"
]

$ pcluster get-image-log-events --image-id IMAGE_ID --region REGION \
 --log-stream-name 3.1.4/1 --limit 3
{
  "nextToken": "f/36295977202298886557255241372854078762600452615936671762",
  "prevToken": "b/36295977196879805474012299949460899222346900769983430672",
  "events": [
    {
      "message": "ExecuteBash: FINISHED EXECUTION",
      "timestamp": "2022-04-05T22:13:26.633Z"
    },
    {
      "message": "Document arn:aws:imagebuilder:us-east-1:123456789012:component/parallelclusterimage-test-abcd1234-ef56-gh78-ij90-1234abcd5678/3.1.4/1",
      "timestamp": "2022-04-05T22:13:26.741Z"
    },
    {
      "message": "TOE has completed execution successfully",
      "timestamp": "2022-04-05T22:13:26.819Z"
    }
  ]
}
```

Continue to check with the `describe-image` command until you see the `BUILD_COMPLETE` status\.

```
$ pcluster describe-image --image-id IMAGE_ID --region REGION
{
  "imageConfiguration": {
    "url": "https://parallelcluster-1234abcd5678efgh-v1-do-not-delete.s3.us-east-1.amazonaws.com/parallelcluster/3.1.4/images/IMAGE_ID-abcd1234efgh5678/configs/image-config.yaml?Signature=..."
  },
  "imageId": "IMAGE_ID",
  "imageBuildStatus": "BUILD_COMPLETE",
  "region": "us-east-1",
  "ec2AmiInfo": {
      "amiName": "IMAGE_ID 2022-04-05T21-39-24.020Z",
      "amiId": "ami-1234stuv5678wxyz",
      "description": "AWS ParallelCluster AMI for alinux2, kernel-4.14.238-182.422.amzn2.x86_64, lustre-2.10.8-5.amzn2.x86_64, efa-1.13.0-1.amzn2.x86_64, dcv-2021.1.10598-1.el7.x86_64, slurm-20-11-8-1",
      "state": "AVAILABLE",
      "tags": [
       {
         "value": "2021.3.11591-1.el7.x86_64",
         "key": "parallelcluster:dcv_version"
       },
       ...
      ],
    "architecture": "x86_64"
  },
  "version": "3.1.4"      
}
```

If you need to debug an AMI creation issue, you can create an archive of the image logs as described in following steps\.

It's possible to archive the logs in an Amazon S3 bucket or in a local file \(depending on the `--output` parameter\)\.

```
$ pcluster export-image-logs --image-id IMAGE_ID --region REGION \
 --bucket BUCKET_NAME --bucket-prefix BUCKET_FOLDER
{
  "url": "https://BUCKET_NAME.s3.us-east-1.amazonaws.com/BUCKET-FOLDER/IMAGE_ID-logs-202209071136.tar.gz?AWSAccessKeyId=..."
}

$ pcluster export-image-logs --image-id IMAGE_ID \
 --region REGION --bucket BUCKET_NAME --bucket-prefix BUCKET_FOLDER --output-file /tmp/archive.tar.gz
{
  "path": "/tmp/archive.tar.gz"
}
```

The archive contains the CloudWatch Logs Streams related to the Image Builder process and AWS CloudFormation stack events\. The command might take several minutes to run\.

 **Managing Custom AMIs** 

Starting from AWS ParallelCluster 3\.0\.0, a new set of commands has been added in the CLI to build, monitor, and manage the image lifecycle\. For more information about the commands, see [pcluster commands](pcluster-v3.md#pcluster.subcommands-v3)\.

### Modify an AWS ParallelCluster AMI<a name="modify-an-aws-parallelcluster-ami"></a>

This method consists on modifying an official AWS ParallelCluster AMI by adding customization on top of it\. The base AWS ParallelCluster AMIs are updated with new releases\. These AMIs have all of the components required for AWS ParallelCluster to function when it's installed and configured\. You can start with one of these as your base\.

Key points:
+ This method is faster than the `build-image` command, however, it is a manual process and not automatically repeatable\.
+ With this method you don't have access to the log retrieval and image lifecycle management commands available through the CLI\.

Steps:

1. Find the AMI that corresponds to the specific region that you use\. To find it you can use the `pcluster list\-official\-images` command with the `--region` parameter to select the specific region and `--os` and `--architecture` parameters to filter for the desired AMI with the OS and architecture that you want to use\. From the output you can retrieve the EC2 Image ID\.

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Images**, and then **AMIs**\. Search for the retrieved EC2 Image ID, choose **Actions**, and then **Launch**\.

1. Choose your instance type and properties, and launch your instance\.

1. Log into your instance using the OS user and your SSH key\.

1. Manually customize your instance to meet your requirements\.

1. Run the following command to prepare your instance for AMI creation:

   ```
   sudo /usr/local/sbin/ami_cleanup.sh
   ```

1. Stop the instance\.

1. Create a new AMI from the instance using the EC2 console or AWS CLI [create\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-image.html)\.

**From the EC2 console**

   1. Choose **Instances** in the navigation pane\.

   1. Choose the instance you created and modified\.

   1. Choose **Image and templates** and then **Create image**\.

1. Enter the new AMI ID in the `CustomAmi` field in your cluster configuration and create a cluster\.