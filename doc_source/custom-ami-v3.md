# AWS ParallelCluster AMI customization<a name="custom-ami-v3"></a>

There are scenarios where building a custom AMI for AWS ParallelCluster is necessary\. This section covers what to consider when building a custom AWS ParallelCluster AMI\.

You can build a custom AWS ParallelCluster AMI using one of the following methods:

1. Create a [build image configuration file](image-builder-configuration-file-v3.md), and then use the `pcluster` CLI to build the image with EC2 Image Builder\. This process is automated, repeatable, and supports monitoring\. For more information, see the [`pcluster`](pcluster-v3.md) image commands\.

1. Create an instance from an AWS ParallelCluster AMI, then log in to it and make manual modifications\. Last, use Amazon EC2 to create a new AMI from the modified instance\. This process takes less time\. However, it isn't automated or repeatable, and it doesn't support use of the `pcluster` CLI image monitoring commands\.

For more information about these methods, see [Building a custom AWS ParallelCluster AMI](building-custom-ami-v3.md)\.

## AWS ParallelCluster AMI customization considerations<a name="custom-ami-considerations-v3"></a>

No matter how you create your custom image, we recommend that you perform preliminary validation tests and include provisions to monitor the status of the image being created\.

To build a custom AMI using `pcluster`, you create a [build image configuration file](image-builder-configuration-file-v3.md) with a [`Build`](Build-v3.md) and [`Image`](Image-v3.md#Image-v3.properties) section that [EC2 Image Builder](https://docs.aws.amazon.com/imagebuilder/latest/userguide/what-is-image-builder.html) uses to build your custom image\. The `Build` section specifies what Image Builder needs to build the image\. This includes the [`ParentImage`](Build-v3.md#yaml-build-image-Build-ParentImage) \(base image\), and [`Components`](Build-v3.md#Build-v3-Components)\. An [Image Builder component](https://docs.aws.amazon.com/imagebuilder/latest/userguide/toe-component-manager.html) defines a sequence of steps that are required to customize an instance before an image is created or to test an instance that was launched by the created image\. For AWS ParallelCluster component examples, see [Custom AMIs](https://catalog.prod.workshops.aws/workshops/e2f40d13-8082-4718-909b-6cdc3155ae41/examples/custom-ami)\. The `Image` section specifies the image properties\.

When called from pcluster [`build-image`](pcluster.build-image-v3.md) to create a custom image, Image Builder uses the build image configuration with the AWS ParallelCluster cookbook to bootstrap AWS ParallelCluster on your [`ParentImage`](Build-v3.md#yaml-build-image-Build-ParentImage)\. Image Builder downloads components, runs build and validate phases, creates the AMI, launches an instance from the AMI, and runs tests\. When the process completes, Image Builder then produces a new image or a stop message\.

## Perform custom component validation tests<a name="custom-ami-validation-v3"></a>

Before you include an Image Builder component in a configuration, test and validate it using one of the following methods\. Because the Image Builder process can take up to 1 hour, we recommend that you test the components beforehand\. This can save you a considerable amount of time\.

**Script case**  
Test the script in a running instance, outside the build image process, and verify that the script exits with exit code 0\.

**Amazon Resource Name \(ARN\) case**  
Test the component document in a running instance, outside the build image process\. For a list of requirements, see [Component manager](https://docs.aws.amazon.com/imagebuilder/latest/userguide/toe-component-manager.html) in the *Image Builder User Guide*\.

**After successful validation, add the component to your build image configuration**  
After you verified that the custom component is working, add it to the [Build image configuration file](image-builder-configuration-file-v3.md)\.

## Monitor the Image Builder process with `pcluster` commands to aid in debugging<a name="custom-ami-monitor-v3"></a>

[`describe-image`](pcluster.describe-image-v3.md)

Use this command to monitor the build image status\.

[`list-image-log-streams`](pcluster.list-image-log-streams-v3.md)

Use this command to get the IDs of log streams that you can use to retrieve log events with [`get-image-log-events`](pcluster.get-image-log-events-v3.md)\.

[`get-image-log-events`](pcluster.get-image-log-events-v3.md)

Use this command to get the log stream of build image process events\.

For example, you can tail build image events using the following command\.

```
$ watch -n 1 'pcluster get-image-log-events -i <image-id> \
        --log-stream-name/1 <pcluster-version> \
        --query "events[*].message" | tail -n 50'
```

[`get-image-stack-events`](pcluster.get-image-stack-events-v3.md)

Use this command to retrieve image stack events for the stack that Image Builder creates\.

[`export-image-logs`](pcluster.export-image-logs-v3.md)

Use this command save image logs\.

For more information about AWS ParallelCluster logs and Amazon CloudWatch, see [Amazon CloudWatch Logs build image logs](cloudwatch-logs-v3.md#cloudwatch-logs-build-images) and [Amazon CloudWatch dashboard](cloudwatch-dashboard-v3.md)\.

## Other considerations<a name="custom-ami-other-v3"></a>

**New AWS ParallelCluster releases and custom AMIs**  
If you build and use a custom AMI, you must repeat the steps that you used to create your custom AMI with each new AWS ParallelCluster release\.

**Custom bootstrap actions**  
Review the [Custom bootstrap actions](custom-bootstrap-actions-v3.md) section to determine if the modifications you want to make can be scripted and supported with future AWS ParallelCluster releases\.

**Using custom AMIs**  
You can specify custom AMIs in the cluster configuration in the [`Image`](Image-v3.md) / [`CustomAmi`](Image-v3.md#yaml-Image-CustomAmi) and [`Scheduling`](Scheduling-v3.md) / [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / \- [`Name`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-Name) / [`Image`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-Image) / [`CustomAmi`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-Image-CustomAmi) sections\.  
To troubleshoot custom AMI validation warnings, see [Troubleshooting custom AMI issues](troubleshooting-v3-custom-amis.md)\.