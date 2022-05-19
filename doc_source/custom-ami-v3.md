# AWS ParallelCluster AMI customization<a name="custom-ami-v3"></a>

Though building a custom AMI isn't ideal, there are scenarios where building a custom AMI for AWS ParallelCluster is necessary\. In this section, learn how AWS ParallelCluster supports customization of AWS ParallelCluster AMIs and what you need to consider when building them\.

There are two ways to build a custom AWS ParallelCluster AMI\.

1. Create a [build image configuration file](image-builder-configuration-file-v3.md) and then use the `pcluster` CLI to build the image with EC2 Image Builder\. This process is automated, repeatable and supports monitoring\. For more information, see the `pcluster` image commands\.

1. Create an instance from an AWS ParallelCluster AMI, log in to it, make manual modifications, and use Amazon EC2 to create a new AMI from the modified instance\. This process takes less time, however, it isn't automated or repeatable, and it doesn't support use of the `pcluster` CLI image monitoring commands\.

For more information on using these methods, see [Building a Custom AWS ParallelCluster AMI](building-custom-ami-v3.md)\.

## AWS ParallelCluster AMI customization considerations<a name="custom-ami-considerations-v3"></a>

Whether you choose the `pcluster` image commands or the manual method to create your customer image, we recommend that you perform preliminary validation tests and include provisions for monitoring the status of image creation\.

To build a custom AMI using `pcluster`, you create a [build image configuration file](image-builder-configuration-file-v3.md) with a `Build` and `Image` section that [EC2 Image Builder](https://docs.aws.amazon.com/imagebuilder/latest/userguide/what-is-image-builder.html) uses to build your custom image\. The `Build` section specifies what Image Builder needs to build the image, including the `ParentImage` \(base image\), and `Components`\. An [Image Builder component](https://docs.aws.amazon.com/imagebuilder/latest/userguide/toe-component-manager.html) defines a sequence of steps required to customize an instance prior to image creation or to test an instance that was launched by the created image\. For AWS ParallelCluster component examples, see [Custom AMIs](https://catalog.prod.workshops.aws/workshops/e2f40d13-8082-4718-909b-6cdc3155ae41/examples/custom-ami)\. The `Image` section specifies the image properties\.

When called from `pcluster build\-image` to create a custom image, Image Builder uses the build image configuration with the AWS ParallelCluster cookbook to bootstrap AWS ParallelCluster on your `ParentImage`\. It downloads components, runs build and validate phases, creates the AMI, launches an instance from the AMI, and runs tests\. When the process completes, it produces a new image or a stop message\.

### Perform custom component validation tests<a name="custom-ami-validation-v3"></a>

Before you include an Image Builder component in a configuration, test and validate it using one of the following methods\. Because the Image Builder process can take upwards of an hour to complete, testing the components beforehand can save you considerable time\.

**Script case**  
Test the script in a running instance, outside the build image process, and verify script is exiting with exit code 0\.

**Amazon Resource Name \(ARN\) case**  
Test the component document in a running instance, outside the build image process, according to the requirements described in [Component manager](https://docs.aws.amazon.com/imagebuilder/latest/userguide/toe-component-manager.html) in the *Image Builder User Guide*\.

**After successful validation add the component to your build image configuration**  
After you have verified that the custom component is working, add it to the [Build image configuration file](image-builder-configuration-file-v3.md)\.

### Monitor the Image Builder process with `pcluster` commands to aid in debugging<a name="custom-ami-monitor-v3"></a>

`describe\-image`  
Use this command to monitor the build image status\.

`list\-image\-log\-streams`  
Use this command to get IDs of log streams that you can use to retrieve log events with `get\-image\-log\-events`\.

`get\-image\-log\-events`  
Use this command to get the log stream of build image process events\.  
For example, you can tail build image events as shown in the following command\.  

```
$ watch -n 1 'pcluster get-image-log-events -i <image-id> \
              --log-stream-name <pcluster-version> \
              --query "events[*].message" | tail -n 50'
```

[get\-image\-stack\-events](pcluster.get-image-stack-events-v3.md)  
Use this command to retrieve image stack events for the stack created by Image Builder\.

`export\-image\-logs`  
Use this command save image logs\.

For more information on AWS ParallelCluster logs and Amazon CloudWatch, see [Amazon CloudWatch Logs Build image logs](cloudwatch-logs-v3.md#cloudwatch-logs-build-images) and [Amazon CloudWatch dashboard](cloudwatch-dashboard-v3.md)\.

### Other considerations<a name="custom-ami-other-v3"></a>

**New AWS ParallelCluster releases and custom AMIs**  
If you build and use a custom AMI, you must repeat the steps that you used to create your custom AMI with each new AWS ParallelCluster release\.

**Custom bootstrap actions**  
Review the [Custom Bootstrap Actions](custom-bootstrap-actions-v3.md) section to determine if the modifications you want to make can be scripted and supported with future AWS ParallelCluster releases\.