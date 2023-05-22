# Amazon CloudWatch dashboard<a name="cloudwatch-dashboard-v3"></a>

An Amazon CloudWatch dashboard is created when a cluster is created\. This makes it easier to monitor the nodes in your cluster, and to view the logs stored in Amazon CloudWatch Logs\. The name of the dashboard is `ClusterName-Region`\. *ClusterName* is the name of your cluster and *Region* is the AWS Region the cluster is in\. You can access the dashboard in the console, or by opening `https://console.aws.amazon.com/cloudwatch/home?region=Region#dashboards:name=ClusterName-Region`\.

The following image shows an example CloudWatch dashboard for a cluster\.

 ![\[Dashboard graphs of the status of cluster resources.\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/CW-dashboard.png) 

**Head Node Instance Metrics**

The first section of the dashboard displays graphs of the head node EC2 metrics\.

If your cluster has shared storage, the next section shows shared storage metrics\.

**Cluster Health Metrics**

If your cluster uses Slurm for scheduling, the cluster health metric graphs show real\-time cluster compute node errors\. For more information, see [Troubleshooting cluster health metrics](troubleshooting-v3-cluster-health-metrics.md)\. Cluster health metrics are added to the dashboard starting with AWS ParallelCluster version 3\.6\.0\.

**Head Node Logs**

The final section lists head node logs grouped by AWS ParallelCluster's logs, Scheduler's logs, NICE DCV integration logs, and System's logs\.

For more information about Amazon CloudWatch dashboards, see [Using Amazon CloudWatch dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html) in the *Amazon CloudWatch User Guide*\.

If you don’t want to create the Amazon CloudWatch dashboard, you can turn it off by setting [`Monitoring`](Monitoring-v3.md) / [`Dashboards`](Monitoring-v3.md#yaml-Monitoring-Dashboards) / [`CloudWatch`](Monitoring-v3.md#yaml-Monitoring-Dashboard-CloudWatch) / [`Enabled`](Monitoring-v3.md#yaml-Monitoring-Dashboard-CloudWatch-Enabled) to `false`\.

**Note**  
If you disable the creation of the Amazon CloudWatch dashboard, you also disable the Amazon CloudWatch `disk_used_percent` and `memory_used_percent` alarms for your cluster\. For more information, see [Amazon CloudWatch alarms for cluster metrics](cloudwatch-alarms-v3.md)\.  
The `disk_used_percent` and `memory_used_percent` alarms are added starting with AWS ParallelCluster version 3\.6\.