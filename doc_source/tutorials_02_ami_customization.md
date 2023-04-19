# Building a Custom AWS ParallelCluster AMI<a name="tutorials_02_ami_customization"></a>

**Important**  
We don't recommend building a custom AMI as an approach for customizing AWS ParallelCluster\.  
This is because, after you build your own AMI, you no longer receive updates or bug fixes with future releases of AWS ParallelCluster\. Moreover, if you build a custom AMI, you must repeat the steps that you used to create your custom AMI with each new AWS ParallelCluster release\.

Before reading any further, we recommend that you first check out the [Custom Bootstrap Actions](pre_post_install.md) section to determine if the modifications you want to make can be scripted and supported with future AWS ParallelCluster releases\.

Even though building a custom AMI isn't ideal \(because of the reasons mentioned earlier\), there are still scenarios where building a custom AMI for AWS ParallelCluster is necessary\. This tutorial guides you through the process of building a custom AMI for these scenarios\.

**Note**  
Starting with AWS ParallelCluster version 2\.6\.1, most of the install recipes are skipped by default when launching nodes\. This is to improve startup times\. To run all of the install recipes for better backwards compatibility at the expense of startup times, add `"skip_install_recipes" : "no"` to the `cluster` key in the [`extra_json`](cluster-definition.md#extra-json) setting\. For example:  

```
extra_json = { "cluster" : { "skip_install_recipes" : "no" } }
```

**Prerequisites**
+ AWS ParallelCluster [is installed](install.md)\.
+ The AWS CLI [is installed and configured\.](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
+ You have an [EC2 key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)\.
+ You have an IAM role with the [ permissions](iam.md#example-parallelcluser-policies) required to run the [`pcluster`](pcluster.md) CLI\.

## How to Customize the AWS ParallelCluster AMI<a name="how-to-customize-the-aws-parallelcluster-ami"></a>

There are three ways to use a custom AWS ParallelCluster AMI described in the next sections\. Two of these three methods require you to build a new AMI that's available under your AWS account\. The third method \(Use a Custom AMI at Runtime\) doesn't require that you build anything in advance, but does add risk to the deployment\. Choose the method that best fits your needs\.

## Modify an AMI<a name="modify-an-aws-parallelcluster-ami"></a>

This is the safest and most recommended method\. Because the base AWS ParallelCluster AMI is often updated with new releases, this AMI has all of the components required for AWS ParallelCluster to function when it's installed and configured\. You can start with this as the base\.

------
#### [ New EC2 console ]

1. In the AWS ParallelCluster AMI list, find the AMI that corresponds to the specific AWS Region that you use\. The AMI list that you choose must match the version of AWS ParallelCluster that you use\. Run `pcluster version` to verify the version\. For AWS ParallelCluster version 2\.11\.9, go to [https://github\.com/aws/aws\-parallelcluster/blob/v2\.11\.9/amis\.txt](https://github.com/aws/aws-parallelcluster/blob/v2.11.9/amis.txt)\. To select another version, use the same link, choose the **Tag: 2\.11\.9** button, select the **Tags** tab, and then select the appropriate version\.

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the **Amazon EC2 Dashboard**, choose **Launch instance**\.

1. In **Application and OS images**, choose **Browse more AMIs**, navigate to **Community AMIs**, and enter the AWS ParallelCluster AMI ID for your AWS Region into the search box\.

1. **Select** the AMI, choose your **Instance type** and properties, select your **Key pair**, and **Launch instance**\.

1. Log into your instance using the OS user and your SSH key\. For more information, navigate to **Instances**, select the new instance, and **Connect**\.

1. Customize your instance as required\.

1. Run the following command to prepare your instance for AMI creation:

   ```
   sudo /usr/local/sbin/ami_cleanup.sh
   ```

1. Navigate to **Instances**, choose the new instance, select **Instance state**, and **Stop instance**\.

1. Create a new AMI from the instance using the EC2 console or AWS CLI [create\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-image.html)\.

**From the EC2 console**

   1. Choose **Instances** in the navigation pane\.

   1. Choose the instance you created and modified\.

   1. In **Actions**, choose **Image and templates**, and then **Create image**\.

   1. Choose **Create Image**\.

1. Enter the new AMI id in the [custom\_ami](cluster-definition.md#custom-ami-section) field in your cluster configuration\.

------
#### [ Old EC2 console ]

1. In the AWS ParallelCluster AMI list, find the AMI that corresponds to the specific AWS Region that you use\. The AMI list that you choose must match the version of AWS ParallelCluster that you use\. Run `pcluster version` to verify the version\. For AWS ParallelCluster version 2\.11\.9, go to [https://github\.com/aws/aws\-parallelcluster/blob/v2\.11\.9/amis\.txt](https://github.com/aws/aws-parallelcluster/blob/v2.11.9/amis.txt)\. To select another version, use the same link, choose the **Tag: 2\.11\.9** button, select the **Tags** tab, and then select the appropriate version\.

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the **Amazon EC2 Dashboard**, choose **Launch instance**\.

1. Choose **Community AMIs**, search for the AWS ParallelCluster AMI ID, and **Select** it\.

1. Choose your instance type and select **Next: Configure Instance Details**, or **Review and Launch** to launch your instance\.

1. Choose **Launch**, select your **Key pair**, and **Launch Instances**\.

1. Log into your instance using the OS user and your SSH key\. For more information, navigate to **Instances**, select the new instance, and **Connect**\.

1. Customize your instance as required\.

1. Run the following command to prepare your instance for AMI creation:

   ```
   sudo /usr/local/sbin/ami_cleanup.sh
   ```

1. Navigate to **Instances**, choose the new instance, select **Instance State**, and **Stop**

1. Create a new AMI from the instance using the EC2 console or AWS CLI [create\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-image.html)\.

**From the EC2 console**

   1. Choose **Instances** in the navigation pane\.

   1. Choose the instance you created and modified\.

   1. In **Actions**, choose **Image**, and then **Create Image**\.

   1. Choose **Create Image**\.

1. Enter the new AMI id in the [custom\_ami](cluster-definition.md#custom-ami-section) field in your cluster configuration\.

------

## Build a Custom AWS ParallelCluster AMI<a name="build-a-custom-aws-parallelcluster-ami"></a>

If you have a customized AMI and software already in place, you can apply the changes needed by AWS ParallelCluster on top of it\.

1. Install the following in your local system, together with the AWS ParallelCluster CLI:
   + Packer: find the latest version for your OS from the [Packer website](https://developer.hashicorp.com/packer/downloads), and install it\. The version must be at least 1\.4\.0, but the latest version is recommended\. Verify that the `packer` command is available in your PATH\.
**Note**  
Before AWS ParallelCluster version 2\.8\.0, [Berkshelf](https://github.com/berkshelf/berkshelf) \(which is installed by using `gem install berkshelf`\) was required to use `pcluster createami`\.

1. Configure your AWS account credentials so that Packer can make calls to AWS API operations on your behalf\. The minimal set of required permissions necessary for Packer to work are documented in the [IAM Task or Instance Role](https://developer.hashicorp.com/packer/plugins/builders/amazon#iam-task-or-instance-role) section of the *Amazon AMI Builder* topic in the Packer documentation\.

1. Use the command `createami` in the AWS ParallelCluster CLI to build an AWS ParallelCluster AMI starting from the one that you provide as base:

   ```
   pcluster createami --ami-id <BASE_AMI> --os <BASE_AMI_OS>
   ```
**Important**  
You shouldn't use an AWS ParallelCluster AMI from a running cluster as *<BASE\_AMI>* for the `createami` command\. Otherwise, the command fails\.

   For other parameters, see [`pcluster createami`](pcluster.createami.md)\.

1. The command in Step 4 runs Packer, which specifically does the following:

   1. Launches an instance using the base AMI provided\.

   1. Applies the AWS ParallelCluster cookbook to the instance to install relevant software and perform other necessary configuration tasks\.

   1. Stops the instance\.

   1. Creates an new AMI from the instance\.

   1. Terminates the instance after the AMI is created\.

   1. Outputs the new AMI ID string to use to create your cluster\.

1. To create your cluster, enter the AMI ID in the [custom\_ami](cluster-definition.md#custom-ami-section) field within your cluster configuration\.

**Note**  
The instance type used to build a custom AWS ParallelCluster AMI is `t2.xlarge`\. This instance type doesn't qualify for the AWS free tier, so you're charged for any instances that are created when you build this AMI\.

## Use a Custom AMI at Runtime<a name="use-a-custom-ami-at-runtime"></a>

**Warning**  
To avoid the risk of using an AMI that's not compatible with AWS ParallelCluster, we recommend that you avoid using this method\.  
When compute nodes are launched with potentially untested AMIs at runtime, incompatibilities with the runtime installation of AWS ParallelCluster's required software might cause AWS ParallelCluster to stop working\.

If you don't want to create anything in advance, you can use your AMI and create an AWS ParallelCluster from that AMI\.

With this method, it takes longer for the AWS ParallelCluster to be created because all the software that's needed by AWS ParallelCluster when the cluster is created must be installed\. Moreover, scaling up also takes a longer time\.
+ Enter the AMI id in the [custom\_ami](cluster-definition.md#custom-ami-section) field within your cluster configuration\.