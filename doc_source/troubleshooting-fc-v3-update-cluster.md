# Trying to update a cluster<a name="troubleshooting-fc-v3-update-cluster"></a>

## `pcluster update-cluster` command fails to run locally<a name="update-cluster-failure-cli-v3"></a>

Check the `~/.parallelcluster/pcluster-cli.log` in your local file system for failure details\.

## Seeing `clusterStatus` is `UPDATE_FAILED` with `pcluster describe-cluster` command<a name="update-cluster-failure-v3"></a>

If the cluster stack update rolled back, check the `/var/log/chef-client.logs` file for error details\.

Check to see if your issue is mentioned in [GitHub Known Issues](https://github.com/aws/aws-parallelcluster/wiki) at AWS ParallelCluster on GitHub\.

## The cluster update timed out<a name="update-cluster-failure-timeout-v3"></a>

This could be an issue related to `cfn-hup` not running\. If the `cfn-hup` demon is terminated by an external cause, it's not restarted automatically\. If `cfn-hup` isn't running, during a cluster update, the CloudFormation stack starts the update process as expected, but the update procedure isn't activated on the head node and the stack deployment eventually times out\. For more information, see [Troubleshooting a cluster update timeout when `cfn-hup` isn't running](troubleshooting-v3-cluster-update-timeout.md) to troubleshoot and recover from the issue\.