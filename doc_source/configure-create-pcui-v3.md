# Configure and create a cluster with the AWS ParallelCluster UI<a name="configure-create-pcui-v3"></a>

The AWS ParallelCluster UI is a web\-based user interface that mirrors the AWS ParallelCluster `pcluster` CLI, while providing a console\-like experience\. You install and access the AWS ParallelCluster UI in your AWS account\. When you run it, the AWS ParallelCluster UI accesses an instance of the AWS ParallelCluster API hosted on Amazon API Gateway in your AWS account\.

In this section, we guide you through configuring and creating a cluster by using the AWS ParallelCluster UI\.

**Prerequisites:**
+ Access to a running instance of AWS ParallelCluster UI\. For more information, see [Installing the AWS ParallelCluster UI](install-pcui-v3.md)\.

**Configure and create a cluster**

1. In the AWS ParallelCluster UI **Clusters** view, choose **Create cluster**\.

1. For **Cluster name**, enter a name for your cluster\.

1. Keep **Interface** selected, and choose **Next**\.

1. In **Cluster**, choose a **VPC** with a public subnet for your cluster, and choose **Next**\.

1. In **Head node**, choose **Add SSM session**, and choose **Next**\.

1. In **Storage**, keep the defaults and choose **Next**\.

1. In **Queues**, choose **1** for **Static nodes**\.

1. For **Instance type**, remove the selected default instance type, choose **t2\.micro**, and choose **Next**\.

1. In **Cluster configuration**, review the cluster configuration YAML and choose **Dry run** to validate it\.

1. Choose **Create** to create your cluster, based on the validated configuration\.

1. After a few seconds, the AWS ParallelCluster UI automatically navigates you back to **Clusters**, where you can monitor the cluster create status and **Stack events**\.

1. Choose **Instances** to see the list of EC2 instances and status\.

1. Choose **Details** to see cluster details, such as the version, status, and a link to the CloudFormation stack that creates the cluster\.

1. In **Details**, after cluster creation completes, choose **View** to view or download the cluster configuration YAML file\.

1. After cluster creation completes, choose **Shell** to access the cluster head node\.

1. To clean up, in the **Clusters** view, select the cluster, and choose **Delete**\.