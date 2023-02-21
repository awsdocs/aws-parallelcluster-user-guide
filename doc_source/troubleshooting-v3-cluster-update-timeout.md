# Troubleshooting a cluster update timeout when `cfn-hup` isn't running<a name="troubleshooting-v3-cluster-update-timeout"></a>

The `cfn-hup` helper is a daemon that detects changes in resource metadata and runs user\-specified actions when a change is detected\. This is how you make configuration updates on your running Amazon EC2 instances through the `UpdateStack` API action\.

Currently the `cfn-hup` daemon is launched by the `supervisord`\. But after launch, the `cfn-hup` process is detached from `supervisord` control\. If the `cfn-hup` demon is killed by an external actor, it's not restarted automatically\. If `cfn-hup` isn't running, during a cluster update, the CloudFormation stack starts the update process as expected but the update procedure isn't activated on the head node and the stack eventually goes into timeout\. From the cluster logs `/var/log/chef-client`, you can see that the update recipe is never invoked\.

**Check and restart `cfn-hup` in case of failures**

1. On the head node, check if `cfn-hup` is running:

   ```
   $ ps aux | grep cfn-hup
   ```

1. Check `cfn-hup` log `/var/log/cfn-hup.log` and `/var/log/supervisord.log` on the head node\.

1. If `cfn-hup` isn't running, try restarting it by running:

   ```
   $ sudo /opt/parallelcluster/pyenv/versions/cookbook_virtualenv/bin/supervisorctl start cfn-hup
   ```