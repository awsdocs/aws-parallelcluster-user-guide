# Monitoring AWS ParallelCluster and logs<a name="monitoring-overview"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of AWS ParallelCluster and your other AWS solutions\. AWS provides the following monitoring tools to watch AWS ParallelCluster, report when something is wrong, and take automatic actions when appropriate:
+ *Amazon CloudWatch* monitors your AWS resources and the applications you run on AWS in real time\. You can collect and track metrics, create customized dashboards, and set alarms that notify you or take actions when a specified metric reaches a threshold that you specify\. For example, you can have CloudWatch track CPU usage or other metrics of your Amazon EC2 instances and automatically launch new instances when needed\. For more information, see the [Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\.
+ *Amazon CloudWatch Logs* enables you to monitor, store, and access your log files from Amazon EC2 instances, CloudTrail, and other sources\. CloudWatch Logs can monitor information in the log files and notify you when certain thresholds are met\. You can also archive your log data in highly durable storage\. For more information, see the [Amazon CloudWatch Logs User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/)\.
+ *AWS CloudTrail* captures API calls and related events made by or on behalf of your AWS account and delivers the log files to an Amazon S3 bucket that you specify\. You can identify which users and accounts called AWS, the source IP address from which the calls were made, and when the calls occurred\. For more information, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.
+ *Amazon EventBridge* is a serverless event bus service that makes it easy to connect your applications with data from a variety of sources\. EventBridge delivers a stream of real\-time data from your own applications, Software\-as\-a\-Service \(SaaS\) applications, and AWS services and routes that data to targets such as Lambda\. This enables you to monitor events that happen in services, and build event\-driven architectures\. For more information, see the [Amazon EventBridge User Guide](https://docs.aws.amazon.com/eventbridge/latest/userguide/)\.

**Topics**
+ [Integration with Amazon CloudWatch Logs](cloudwatch-logs-v3.md)
+ [Amazon CloudWatch dashboard](cloudwatch-dashboard-v3.md)
+ [Amazon CloudWatch alarms for cluster metrics](cloudwatch-alarms-v3.md)
+ [AWS ParallelCluster configured log rotation](log-rotation-v3.md)
+ [`pcluster` CLI logs](troubleshooting-v3-pc-cli-logs.md)
+ [EC2 console output logs](console-logs-v3.md)
+ [Retrieve AWS ParallelCluster UI and AWS ParallelCluster runtime logs](troubleshooting-v3-get-runtime-logs.md)
+ [Retrieving and preserving logs](troubleshooting-v3-get-logs.md)