# AWS ParallelCluster resources and tagging<a name="resources-tags"></a>

With AWS ParallelCluster you can create tags to track and manage your AWS ParallelCluster resources\. You define the tags that you want AWS CloudFormation to create and propagate to all cluster resources in the ``tags`` section of the cluster configuration file\. You can also use tags that AWS ParallelCluster automatically generates to track and manage your resources\.

When you create a cluster, the cluster and its resources are tagged with the AWS ParallelCluster and AWS systems tags defined in this section\.

AWS ParallelCluster applies tags to the cluster instances, volumes, and resources\. To identify the cluster stack, AWS CloudFormation applies AWS system tags to the cluster instances\. To identify the cluster EC2 launch templates, EC2 applies system tags to the instances\. You can use these tags to view and manage your AWS ParallelCluster resources\.

You can't modify AWS system tags\. In order to avoid impacts to AWS ParallelCluster functionality, don't modify AWS ParallelCluster tags\.

The following is an example of an AWS system tag for an AWS ParallelCluster resource\. You can't modify them\.

```
"aws:cloudformation:stack-name"="parallelcluster-clustername-MasterServerSubstack-ABCD1234EFGH"
```

The following are examples of AWS ParallelCluster tags applied to a resource\. Don't modify them\.

```
"aws-parallelcluster-node-type"="Master"
```

```
"Name"="Master"
```

```
"Version"="2.11.7"
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
|  `ClusterName`  |  `clustername`  | 
|  `Name`  |  `Master`  | 
|  `Application`  |  `parallelcluster-clustername`  | 
|  `aws:ec2launchtemplate:id`  |  `lt-1234567890abcdef0`  | 
|  `aws:ec2launchtemplate:version`  |  `1`  | 
|  `aws-parallelcluster-node-type`  |  `Master`  | 
|  `aws:cloudformation:stack-name`  |  `parallelcluster-clustername-MasterServerSubstack-ABCD1234EFGH`  | 
|  `aws:cloudformation:logical-id`  |  `MasterServer`  | 
|  `aws:cloudformation:stack-id`  |  `arn:aws:cloudformation:region-id:ACCOUNTID:stack/parallelcluster-clustername-MasterServerSubstack-ABCD1234EFGH/1234abcd-12ab-12ab-12ab-1234567890abcdef0`  | 
|  `Version`  |  `2.11.7`  | 


**AWS ParallelCluster head node root volume tags**  

| Tag key | Tag value | 
| --- | --- | 
|  `ClusterName`  |  `clustername`  | 
|  `Application`  |  `parallelcluster-clustername`  | 
|  `aws-parallelcluster-node-type`  |  `Master`  | 


**AWS ParallelCluster compute node instance tags**  

| Key | Tag value | 
| --- | --- | 
|  `ClusterName`  |  `clustername`  | 
|  `aws-parallelcluster-node-type`  |  `Compute`  | 
|  `aws:ec2launchtemplate:id`  |  `lt-1234567890abcdef0`  | 
|  `aws:ec2launchtemplate:version`  |  `1`  | 
|  `QueueName`  |  `queue-name`  | 
|  `Version`  |  `2.11.7`  | 


**AWS ParallelCluster compute node root volume tags**  

| Tag key | Tag value | 
| --- | --- | 
|  `ClusterName`  |  `clustername`  | 
|  `Application`  |  `parallelcluster-clustername`  | 
|  `aws-parallelcluster-node-type`  |  `Compute`  | 
|  `QueueName`  |  `queue-name`  | 
|  `Version`  |  `2.11.7`  | 