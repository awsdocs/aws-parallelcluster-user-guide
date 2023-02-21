# Troubleshooting issues in NICE DCV<a name="troubleshooting-v3-nice-dcv"></a>

**Topics**
+ [Logs for NICE DCV](#troubleshooting-v3-nice-dcv-logs)
+ [Ubuntu NICE DCV issues](#troubleshooting-v3-nice-dcv-modules)

## Logs for NICE DCV<a name="troubleshooting-v3-nice-dcv-logs"></a>

The logs for NICE DCV are written to files in the `/var/log/dcv/` directory\. Reviewing these logs can help to troubleshoot issues\.

The instance type should have at least 1\.7 gibibytes \(GiB\) of RAM to run NICE DCV\. Nano and micro instance types don't have enough memory to run NICE DCV\.

AWS ParallelCluster creates NICE DCV log streams in log groups\. You can view these logs in the CloudWatch console **Custom Dashboards** or **Log groups**\. For more information, see [Integration with Amazon CloudWatch Logs](cloudwatch-logs-v3.md) and [Amazon CloudWatch dashboard](cloudwatch-dashboard-v3.md)\.

## Ubuntu NICE DCV issues<a name="troubleshooting-v3-nice-dcv-modules"></a>

When running Gnome Terminal over a NICE DCV session on Ubuntu, you might not automatically have access to the user environment that AWS ParallelCluster makes available through the login shell\. The user environment provides environment modules such as openmpi or intelmpi, and other user settings\.

Gnome Terminal's default settings prevent the shell from starting as a login shell\. This means that shell profiles aren't automatically sourced and the AWS ParallelCluster user environment isn't loaded\.

To properly source the shell profile and access the AWS ParallelCluster user environment, do one of the following:
+ 

**Change the default terminal settings:**

  1. Choose the **Edit** menu in the Gnome terminal\.

  1. Select **Preferences**, then **Profiles**\.

  1. Choose **Command** and select **Run Command as login shell**\.

  1. Open a new terminal\.
+ **Use the command line to source the available profiles:**

  ```
  $ source /etc/profile && source $HOME/.bashrc
  ```