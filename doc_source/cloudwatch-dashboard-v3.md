# Amazon CloudWatch dashboard<a name="cloudwatch-dashboard-v3"></a>

An Amazon CloudWatch dashboard is created when the cluster is created\. This makes it easier to monitor the nodes in your cluster, and to view the logs stored in Amazon CloudWatch Logs\. The name of the dashboard is `ClusterName-Region`\. *ClusterName* is the name of your cluster and *Region* is the AWS Region of the cluster\. You can access the dashboard in the console, or by opening `https://console.aws.amazon.com/cloudwatch/home?region=Region#dashboards:name=ClusterName-Region`\.

The following image shows and example CloudWatch dashboard for a cluster\.

 ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/CW-dashboard.png) 

The first section of the dashboard displays graphs of the Head Node EC2 metrics\. If your cluster has shared storage, the next section shows shared storage metrics\. The final section lists Head Node Logs grouped by AWS ParallelCluster's logs, Scheduler's logs, NICE DCV integration logs, and System's logs\.

For more information about Amazon CloudWatch dashboards, see [Using Amazon CloudWatch dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html) in the *Amazon CloudWatch User Guide*\.

If you don’t want to create the Amazon CloudWatch dashboard, disable by setting [`Monitoring`](Monitoring-v3.md) / [`Dashboards`](Monitoring-v3.md#yaml-Monitoring-Dashboards) / [`CloudWatch`](Monitoring-v3.md#yaml-Monitoring-Dashboard-CloudWatch) / [`Enabled`](Monitoring-v3.md#yaml-Monitoring-Dashboard-CloudWatch-Enabled) to `false`\.