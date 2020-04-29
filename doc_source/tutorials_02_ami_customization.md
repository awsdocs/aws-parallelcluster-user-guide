# Building a Custom AWS ParallelCluster AMI<a name="tutorials_02_ami_customization"></a>

**Important**  
Building a custom AMI is not the recommended approach for customizing AWS ParallelCluster\.  
After you build your own AMI, you no longer receive updates or bug fixes with future releases of AWS ParallelCluster\. Also, you must repeat the steps used to create your custom AMI with each new AWS ParallelCluster release\.

Before reading any further, take a look at the [Custom Bootstrap Actions](pre_post_install.md) section to determine if the modifications you want to make can be scripted and supported with future AWS ParallelCluster releases\.

While building a custom AMI is not ideal, there are scenarios in which building a custom AMI for AWS ParallelCluster is necessary\. This tutorial guides you through the process\.

**Note**  
Starting with AWS ParallelCluster 2\.6\.1, most of the install recipes are skipped by default when launching nodes to improve start up times\. To run all of the install recipes for better backwards compatibility at the expense of start up times, add `"skip_install_recipes" : "no"` to the `cluster` key in the [`extra_json`](cluster-definition.md#extra-json) setting\. For example:  

```
extra_json = { "cluster" : { "skip_install_recipes" : "no" } }
```

## How to Customize the AWS ParallelCluster AMI<a name="how-to-customize-the-aws-parallelcluster-ami"></a>

There are three ways to use a custom AWS ParallelCluster AMI\. Two methods require you to build a new AMI that is available under your AWS account\. The third method—Use a Custom AMI at Runtime—does not require you to build anything in advance\. Select the appropriate method based on your needs\.

**Topics**
+ [Modify an AWS ParallelCluster AMI](#modify-an-aws-parallelcluster-ami)
+ [Build a Custom AWS ParallelCluster AMI](#build-a-custom-aws-parallelcluster-ami)
+ [Use a Custom AMI at Runtime](#use-a-custom-ami-at-runtime)

### Modify an AWS ParallelCluster AMI<a name="modify-an-aws-parallelcluster-ami"></a>

This is the safest method, because the base AWS ParallelCluster AMI is often updated with new releases\. This AMI has all of the components required for AWS ParallelCluster to function as installed and configured\. You can start with this as the base\.

1. In the AMI list, find the AMI that corresponds to the Region you are using\. The AMI list to use must match the version of AWS ParallelCluster you are using\. Run `pcluster version` to verify the version\. For AWS ParallelCluster 2\.6\.1 go to [https://github\.com/aws/aws\-parallelcluster/blob/v2\.6\.1/amis\.txt](https://github.com/aws/aws-parallelcluster/blob/v2.6.1/amis.txt)\. To select another version, use the same link, choose the **Tag: 2\.6\.1** button, select the **Tags** tab, and then select the appropriate version\.

1. Within the Amazon EC2 console, choose **Launch Instance**\.

1. Navigate to **Community AMIs**, and enter the AMI id for your Region into the search box\.

1. Select the AMI, choose your instance type and properties, and launch your instance\.

1. Log into your instance using the OS user and your SSH key\.

1. Customize your instance as required\.

1. Run the following command to prepare your instance for AMI creation:

   ```
   sudo /usr/local/sbin/ami_cleanup.sh
   ```

1. Stop the instance\.

1. Create a new AMI from the instance\.

1. Enter the AMI id in the [custom\_ami](cluster-definition.md#custom-ami-section) field within your cluster configuration\.

### Build a Custom AWS ParallelCluster AMI<a name="build-a-custom-aws-parallelcluster-ami"></a>

If you have a customized AMI and software already in place, you can apply the changes needed by AWS ParallelCluster on top of it\.

1. Install the following tools in your local system, together with the AWS ParallelCluster CLI:
   + Packer: find the latest version for your OS from the [Packer website](https://www.packer.io/downloads.html), and install it\.
   + ChefDK: find the latest version for your OS from the [ChefDK website](https://downloads.chef.io/chefdk/), and install it\.

1. Verify that the `packer` and `berks` commands are available in your PATH after you have installed the tools in Step 1\.

1. Configure your AWS account credentials so that Packer can make calls to AWS API operations on your behalf\. The minimal set of required permissions necessary for Packer to work are documented in the [IAM Task or Instance Role](https://www.packer.io/docs/builders/amazon.html#iam-task-or-instance-role) section of the *Amazon AMI Builder* topic in the Packer documentation\.

1. Use the command `createami` in the AWS ParallelCluster CLI to build an AWS ParallelCluster AMI starting from the one that you provide as base:

   ```
   pcluster createami --ami-id <BASE AMI> --os <BASE OS AMI>
   ```
**Important**  
You cannot use an AWS ParallelCluster AMI as *<BASE AMI>* for the `createami` command\. If you do, the command fails\.

   For other parameters, consult the command help:

   ```
   pcluster createami -h
   ```

1. The command in Step 4 executes Packer, which does the following:

   1. Launches an instance using the base AMI provided\.

   1. Applies the AWS ParallelCluster cookbook to the instance, in order to install software and perform other necessary configuration tasks\.

   1. Stops the instance\.

   1. Creates an new AMI from the instance\.

   1. Terminates the instance after the AMI is created\.

   1. Outputs the new AMI ID string to use to create your cluster\.

1. To create your cluster, enter the AMI ID in the [custom\_ami](cluster-definition.md#custom-ami-section) field within your cluster configuration\.

**Note**  
The instance type used to build a custom AWS ParallelCluster AMI is a `t2.xlarge`\. It does not qualify for the AWS free tier\. You are charged for any instances that are created when building this AMI\.

### Use a Custom AMI at Runtime<a name="use-a-custom-ami-at-runtime"></a>

If you don't want to create anything in advance, you can use your AMI and create an AWS ParallelCluster from that\.

Note that with this method, the AWS ParallelCluster creation time is longer, because all software that is needed by AWS ParallelCluster at cluster creation time must be installed\. Also, scaling up for every new node takes more time\.
+ Enter the AMI id in the [custom\_ami](cluster-definition.md#custom-ami-section) field within your cluster configuration\.