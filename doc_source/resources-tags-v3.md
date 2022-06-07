# AWS ParallelCluster resources and tagging<a name="resources-tags-v3"></a>

With AWS ParallelCluster you can create tags to track and manage your AWS ParallelCluster resources\. You define the tags that you want AWS CloudFormation to create and propagate to all cluster resources in the [`Tags` section](Tags-v3.md) of the cluster configuration file\. You can also use tags that AWS ParallelCluster automatically generates to track and manage your resources\.

When you create a cluster, the cluster and its resources are tagged with the AWS ParallelCluster and AWS systems tags defined in this section\.

AWS ParallelCluster applies tags to the cluster instances, volumes, and resources\. To identify the cluster stack, AWS CloudFormation applies AWS system tags to the cluster instances\. To identify the cluster EC2 launch templates, EC2 applies system tags to the instances\. You can use these tags to view and manage your AWS ParallelCluster resources\.

You can't modify AWS system tags\. In order to avoid impacts to AWS ParallelCluster functionality, don't modify AWS ParallelCluster tags\.

The following is an example of an AWS system tag for an AWS ParallelCluster resource\. You can't modify them\.

```
"aws:cloudformation:stack-name"="clustername"
```

The following is an example of an AWS ParallelCluster tag applied to a resource\. Don't modify them\.

```
"parallelcluster:cluster-name"="clustername"
```

You can view these tags in the EC2 section of the AWS Management Console\.

**View tags**

1. Navigate the EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. To view all cluster tags, choose **Tags** in the navigation pane\.

1. To view cluster tags by instance, choose **Instances** in the navigation pane\.

1. Select a cluster instance\.

1. Choose the **Manage tags** tab in the instance details and view the tags\.

1. Choose the **Storage** tab in the instance details\.

1. Select the **Volume ID**\.

1. In **Volumes**, choose the volume\.

1. Choose the **Tags** tab in the volume details and view the tags\.


**AWS ParallelCluster head node instance tags**  

| Key | Tag value | 
| --- | --- | 
|  `parallelcluster:cluster-name`  |  `clustername`  | 
|  `Name`  |  `HeadNode`  | 
|  `aws:ec2launchtemplate:id`  |  `lt-1234567890abcdef0`  | 
|  `aws:ec2launchtemplate:version`  |  `1`  | 
|  `parallelcluster:node-type`  |  `HeadNode`  | 
|  `aws:cloudformation:stack-name`  |  `clustername`  | 
|  `aws:cloudformation:logical-id`  |  `HeadNode`  | 
|  `aws:cloudformation:stack-id`  |  `arn:aws:cloudformation:region-id:ACCOUNTID:stack/clustername/1234abcd-12ab-12ab-12ab-1234567890abcdef0`  | 
|  `parallelcluster:version`  |  `3.1.4`  | 


**AWS ParallelCluster head node root volume tags**  

| Tag key | Tag value | 
| --- | --- | 
|  `parallelcluster:cluster-name`  |  `clustername`  | 
|  `parallelcluster:node-type`  |  `HeadNode`  | 
|  `parallelcluster:version`  |  `3.1.4`  | 


**AWS ParallelCluster compute node instance tags**  

| Key | Tag value | 
| --- | --- | 
|  `parallelcluster:cluster-name`  |  `clustername`  | 
|  `parallelcluster:compute-resource-name`  |  `compute-resource-name`  | 
|  `aws:ec2launchtemplate:id`  |  `lt-1234567890abcdef0`  | 
|  `aws:ec2launchtemplate:version`  |  `1`  | 
|  `parallelcluster:node-type`  |  `Compute`  | 
|  `parallelcluster:queue-name`  |  `queue-name`  | 
|  `parallelcluster:version`  |  `3.1.4`  | 


**AWS ParallelCluster compute node root volume tags**  

| Tag key | Tag value | 
| --- | --- | 
|  `parallelcluster:cluster-name`  |  `clustername`  | 
|  `parallelcluster:compute-resource-name`  |  `compute-resource-name`  | 
|  `parallelcluster:node-type`  |  `Compute`  | 
|  `parallelcluster:queue-name`  |  `queue-name`  | 
|  `parallelcluster:version`  |  `3.1.4`  | 