# Amazon CloudWatch dashboard<a name="cloudwatch-dashboard-v3"></a>

An Amazon CloudWatch dashboard is created when the cluster is created\. This makes it easier to monitor the nodes in your cluster, and to review the logs stored in Amazon CloudWatch Logs\. The name of the dashboard is `ClusterName-Region`\. *ClusterName* is the name of your cluster and *Region* is the AWS Region of the cluster\. You can access the dashboard in the console, or by opening `https://console.aws.amazon.com/cloudwatch/home?region=Region#dashboards:name=ClusterName-Region`\.

For more information about Amazon CloudWatch dashboards, see [Using Amazon CloudWatch dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html) in the *Amazon CloudWatch User Guide*\.

If you don’t want to create the Amazon CloudWatch dashboard, disable by setting [`Monitoring` section](Monitoring-v3.md) / [Dashboards](Monitoring-v3.md#yaml-Monitoring-Dashboards) / [CloudWatch](Monitoring-v3.md#yaml-Monitoring-Dashboard-CloudWatch) / [Enabled](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-ComputeResources-Efa-Enabled) to `false`\.