# Using `pcluster update`<a name="using-pcluster-update"></a>

Starting with AWS ParallelCluster version 2\.8\.0, [`pcluster update`](pcluster.update.md) analyzes the settings used to create the current cluster and the settings in the configuration file for issues\. If any issues are discovered, they are reported, and the steps to take to fix the issues are displayed\. For example, if the [`compute_instance_type`](cluster-definition.md#compute-instance-type) setting is changed to a different instance type, the compute fleet must be stopped before an update can proceed\. This issue is reported when it is discovered\. If no blocking issues are reported, you are prompted whether you want to apply the changes\.

The documentation for each setting defines the update policy for that setting\.

**Update policy: These settings can be changed during an update\.****Update policy: This setting can be changed during an update\.**  
These settings can be changed, and the cluster can be updated using [`pcluster update`](pcluster.update.md)\.

**Update policy: If this setting is changed, the update is not allowed\.**  
These settings can't be changed if the existing cluster hasn't been deleted\. Either the change must be reverted or the cluster must be deleted \(using [`pcluster delete`](pcluster.delete.md)\), and then a new cluster created \(using [`pcluster create`](pluster.create.md)\) in the old cluster's place\.

**Update policy: This setting is not analyzed during an update\.**  
These settings can be changed, and the cluster updated using [`pcluster update`](pcluster.update.md)\.

**Update policy: The compute fleet must be stopped for this setting to be changed for an update\.**  
These settings cannot be changed while the compute fleet exists\. Either the change must be reverted or the compute fleet must be stopped \(using [`pcluster stop`](pcluster.stop.md)\), updated \(using [`pcluster update`](pcluster.update.md)\), and then a new compute fleet created \(using [`pcluster start`](pcluster.start.md)\)\.

**Update policy: This setting can't be decreased during an update\.**  
These settings can be changed, but they cannot be decreased\. If these settings must be decreased, it is necessary to delete the cluster \(using [`pcluster delete`](pcluster.delete.md)\), and create a new cluster \(using [`pcluster create`](pluster.create.md)\)\.

This example demonstrates a [`pcluster update`](pcluster.update.md) with some changes that block the update\.

```
$ pcluster update
  Validating configuration file /home/username/.parallelcluster/config...
Retrieving configuration from CloudFormation for cluster test-1...
Found Changes:

#   section/parameter         old value                 new value               
--  ------------------------  ------------------------  ------------------------
    [cluster default]
01* compute_instance_type     t2.micro                  c4.xlarge
02* ebs_settings              ebs2                      -

    [vpc default]
03  additional_sg             sg-0cd61884c4ad16341       sg-0cd61884c4ad11234

    [ebs ebs2]
04* shared_dir                shared                     my/very/very/long/sha...

Validating configuration update...
The requested update cannot be performed. Line numbers with an asterisk indicate 
updates requiring additional actions. Please look at the details below:

#01
Compute fleet must be empty to update "compute_instance_type"
How to fix:
Make sure that there are no jobs running, then run the following command: 
  pcluster stop -c $CONFIG_FILE $CLUSTER_NAME

#02
Cannot add/remove EBS Sections
How to fix:
Revert "ebs_settings" value to "ebs2"

#04
Cannot change the mount dir of an existing EBS volume
How to fix:
Revert "my/very/very/long/shared/dir" to "shared"

In case you want to override these checks and proceed with the update please
use the --force flag. Note that the cluster could end up in an unrecoverable
state.

Update aborted.
```