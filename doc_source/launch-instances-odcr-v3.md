# Launch instances with ODCR \(On\-Demand Capacity Reservations\)<a name="launch-instances-odcr-v3"></a>

With [On\-Demand Capacity Reservations](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-capacity-reservations.html) \(ODCRs\), you can reserve capacity for your cluster Amazon EC2 instances in a specific Availability Zone for any duration\. This way you can create and manage Capacity Reservations independently from the billing accounts offered by [Savings Plans](https://aws.amazon.com/savingsplans/) or [regional Reserved Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/reserved-instances-scope.html)\.

You can configure `open` or `targeted` ODCRs\. *Open* ODCRs cover any instances that match the ODCR attributes \(instance type, platform, Availability Zone\)\. *Targeted* ODCRs must be explicitly defined in the AWS CLI ec2 [https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command\. To determine whether and ODCR is `open` or `targeted`, run the AWS CLI ec2 [https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html) command\.

You can also create an ODCR in a cluster placement group called a [CPG ODCR](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/cr-cpg.html) \(or CPG/ODCR\)\.

Multiple ODCRs can be grouped in a resource group\. This can be used in the `run-instances` override to target multiple ODCRs at the same time\. For more information on resource groups, see [What are resource groups?](https://docs.aws.amazon.com/ARG/latest/userguide/resource-groups.html) in the *Resource Groups and Tags User Guide*\.

## Using ODCR with AWS ParallelCluster<a name="odcr-parallelcuster-v3"></a>

Open ODCRs are automatically supported by any AWS ParallelCluster\. When using an open ODCR you don’t need to specify anything in AWS ParallelCluster\. Instances are automatically selected for the cluster\. You can specify an existing placement group or have AWS ParallelCluster create a new one\.

Support for `targeted` ODCRs was added AWS ParallelCluster 3\.1\.1\. We introduced a mechanism that overrides EC2 `RunInstances` parameters and passes information about the reservation to use for each configured compute resource in AWS ParallelCluster\. This mechanism is compatible with the use of `targeted` ODCRs\. For this case, you must specify the `run-instances` override configuration as described in the next sections\.

If you’re using a `targeted` ODCR, you can specify a placement group\. As with the previous case, you need to specify a `run-instances` override configuration\.

If AWS created a `targeted` ODCR for you, or you have a specific set of Reserved Instances, you can't specify a placement group because the rules configured by AWS might conflict with the placement group setting\. So if a placement group is required for your application, you need to use a [CPG ODCR](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/cr-cpg.html)\. In any case you need to specify the `run-instances` override configuration\.

If you’re using a CPG ODCR, you must specify the `run-instances` override configuration and you must specify the same placement group in the cluster configuration\.

## Using Reserved Instances with AWS ParallelCluster<a name="odcr-reserved-instances"></a>

Reserved instances [are different](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-capacity-reservations.html#capacity-reservations-differences) than Capacity Reservations \(ODCR\)\. There are [2 types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/reserved-instances-scope.html) of reserved instances\. A *regional* Reserved Instance doesn't reserve capacity\. A *zonal* Reserved Instance reserves capacity in the specified Availability Zone\.

If you have regional Reserved Instances there's no capacity reservation and you can get Insufficient Capacity Errors\. If you have zonal Reserved Instances you have capacity reservation but there are no `run-instances` API parameters you can use to specify them\.

Reserved instances are supported by any AWS ParallelCluster version\. You don't have to specify anything in AWS ParallelCluster and the instances are automatically selected\.

When using zonal Reserved Instances, you can avoid potential Insufficient Capacity Errors by omitting the placement group specification in the cluster configuration\.

## Using `RunInstances` customization in AWS ParallelCluster 3 for `targeted` On Demand Capacity Reservations \(ODCRs\)<a name="odcr-run-instances"></a>

EC2 `RunInstances` parameters can be overridden for each compute resource configured in a cluster queue\. To do so, create the `/opt/slurm/etc/pcluster/run_instances_overrides.json` file on the head node of the cluster with the following code snippet content where:
+ `${queue_name}`: is the name of the queue you want to apply overrides to\.
+ `${compute_resource_name}`: is the compute resource you want to apply overrides to\.
+ `${overrides}`: is an arbitrary JSON object containing the list of `RunInstances` overrides to use for the specific combination of queue and instance\-type\. The overrides syntax needs to follow the same specifications documented in a [run\_instances](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ec2.html#EC2.Client.run_instances) boto3 call\.

```
{
    "${queue_name}": {
        "${compute_resource_name}": {
            ${overrides}
        },
        ...
    },
    ...
}
```

For example, the following JSON configures the ODCR group `group_arn` to be used for `p4d.24xlarge` instances that are configured in queue `my-queue` and in compute resource `my-compute-resource`\.

```
{
    "my-queue": {
        "my-compute-resource": {
            "CapacityReservationSpecification": {
                "CapacityReservationTarget": {
                    "CapacityReservationResourceGroupArn": "group_arn"
                }
            }
        }
    }
}
```

Once such a JSON file is generated, the AWS ParallelCluster daemons responsible for cluster scaling automatically picks up the override configuration and starts using it for instance launches\. In order to confirm that the specified parameters are being used for instance provisioning you can look at the following log files:
+ `/var/log/parallelcluster/clustermgtd` \(in case of static capacity\)
+ `/var/log/parallelcluster/slurm_resume.log` \(in case of dynamic capacity\)

If the parameters are being properly used, you'll find a log entry containing:

```
Found RunInstances parameters override. Launching instances with: <parameters_list>
```

## Create a cluster with `targeted` On Demand Capacity Reservations \(ODCRs\)<a name="odcr-create-cluster"></a>

1. **Create a resource group, to group capacity\.**

   ```
   $ aws resource-groups create-group --name EC2CRGroup \ 
       --configuration '{"Type":"AWS::EC2::CapacityReservationPool"}' '{"Type":"AWS::ResourceGroups::Generic", "Parameters": [{"Name": "allowed-resource-types", "Values": ["AWS::EC2::CapacityReservation"]}]}'
   ```
**Note**  
A resource group doesn't support resources shared by other accounts\.  
If the target ODCR is shared by another account, you don't need to create a resource group\. Use `CapacityReservationId` instead of resource group in step 3:  

   ```
   #!/bin/bash
   set -e
   
   # Override run_instance attributes
   cat > /opt/slurm/etc/pcluster/run_instances_overrides.json << EOF
   {
       "my-queue": {
           "my-compute-resource": {
               "CapacityReservationSpecification": {
                   "CapacityReservationTarget": {
                       "CapacityReservationId": "cr-abcdef01234567890"
                   }
               }
           }
       }
   }
   EOF
   ```

   Add capacity reservations to the resource group\. Every time you create a new ODCR, add it to the Group Reservation\. Be sure to replace *`ACCOUNT_ID`* with your account ID, *`PLACEHOLDER_CAPACITY_RESERVATION`* with the ID of your capacity reservation and *`REGION_ID`* with your AWS region ID \(us\-east\-1 for example\)\.

   ```
   $ aws resource-groups group-resources --region REGION_ID --group EC2CRGroup \
       --resource-arns arn:aws:ec2:REGION_ID:ACCOUNT_ID:capacity-reservation/PLACEHOLDER_CAPACITY_RESERVATION
   ```

   Create a policy document on your local computer\. Be sure to replace *`ACCOUNT_ID`* with your account ID and *`REGION_ID`* with your AWS region ID \(us\-east\-1 for example\)\.

   ```
   cat > policy.json << EOF
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "RunInstancesInCapacityReservation",
               "Effect": "Allow",
               "Action": "ec2:RunInstances",
               "Resource": [
                   "arn:aws:ec2:REGION_ID:ACCOUNT_ID:capacity-reservation/*",
                   "arn:aws:resource-groups:REGION_ID:ACCOUNT_ID:group/*"
               ]
           }
       ]
   }
   EOF
   ```

1. **Create the IAM policy on your AWS account using the json file you created\.**

   ```
   $ aws iam create-policy --policy-name RunInstancesCapacityReservation --policy-document file://policy.json
   ```

1. **Create the following post install script locally on the instance with the name `postinstall.sh`\.**

   Be sure to replace `ACCOUNT_ID` with your account ID and `REGION_ID` with your AWS region ID \(us\-east\-1 for example\)\.

   ```
   #!/bin/bash
   set -e
   
   # Override run_instance attributes
   cat > /opt/slurm/etc/pcluster/run_instances_overrides.json << EOF
   {
       "my-queue": {
           "my-compute-resource": {
               "CapacityReservationSpecification": {
                   "CapacityReservationTarget": {
                       "CapacityReservationResourceGroupArn": "arn:aws:resource-groups:REGION_ID:ACCOUNT_ID:group/EC2CRGroup"
                   }
               }
           }
       }
   }
   EOF
   ```

   Upload the file to S3\. Be sure to replace *S3\_NAME\_BUCKET* with a unique bucket name\.

   ```
   $ aws s3 mb s3://S3_NAME_BUCKET
   aws s3 cp postinstall.sh s3://S3_NAME_BUCKET/postinstall.sh
   ```

1. **Create the local cluster configuration, replacing the placeholders with your own values\.**

   ```
   Region: REGION_ID
   Image:
     Os: alinux2
   HeadNode:
     InstanceType: c5.2xlarge
     Ssh:
       KeyName: YOUR_SSH_KEY
     Iam:
       S3Access:
         - BucketName: S3_NAME_BUCKET
       AdditionalIamPolicies:
         - Policy: arn:aws:iam::ACCOUNT_ID:policy/RunInstancesCapacityReservation
     ## This post-install script is executed after the node is configured.
     ## It is used to install scripts at boot time and specific configurations
     ## In the script below we are overriding the calls to RunInstance to force
     ## the provisioning of our my-queue partition to go through
     ## the On-Demand Capacity Reservation
     CustomActions:
       OnNodeConfigured:
         Script: s3://S3_NAME_BUCKET/postinstall.sh
     Networking:
       SubnetId: YOUR_PUBLIC_SUBNET_IN_TARGET_AZ
   
   Scheduling:
     Scheduler: slurm
     SlurmQueues:
       - Name: my-queue
         ComputeResources:
           - MinCount: 0
             MaxCount: 100
             InstanceType: p4d.24xlarge
             Name: my-compute-resource
             Efa:
               Enabled: true
         Networking:
           ## PlacementGroup:
           ##   Enabled: true ## Keep PG disabled if using targeted ODCR
           SubnetIds:
             - YOUR_PRIVATE_SUBNET_IN_TARGET_AZ
   ```

1. **Create the cluster\.**

   Be sure to replace *`cluster-config.yaml`* with the name of your config file, *`cluster-dl`* with the name you picked for your cluster, and *REGION\_ID* with your AWS region ID\.

   ```
   $ pcluster create-cluster --cluster-configuration cluster-config.yaml --cluster-name cluster-dl --region REGION_ID
   ```

   Once the cluster is created, the post\-install script is executed in the head node\. In the script we are creating the `run_instances_overrides.json` file and overriding the calls to `RunInstances` to force the provisioning of the partition to go through the On\-Demand Capacity Reservation\.

   The AWS ParallelCluster daemons responsible for cluster scaling will automatically pick up this configuration and start using it for instance launches\. In order to confirm that the specified parameters are being used for instance provisioning, you can look at the following log files:
   + `/var/log/parallelcluster/clustermgtd` \(in case of static capacity \- [`MinCount`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-ComputeResources-MinCount)` > 0`\)
   + `/var/log/parallelcluster/slurm_resume.log` \(in case of dynamic capacity\)

   If the parameters are being properly used you will find a log entry containing:

   ```
   Found RunInstances parameters override. Launching instances with: <parameters_list>
   ```

 **Updating `RunInstances` overrides** 

The generated JSON configuration can be updated at any time without stopping the compute fleet\. After the changes are applied any new instance launch will use the updated configuration\. In case you need to apply the updated configuration to running nodes you will have to recycle the nodes by forcing an instance termination and wait for AWS ParallelCluster to replace those nodes\. This can be achieved by either terminating the instance through EC2 or by setting the Slurm nodes in a `DOWN` or `DRAIN` state\.

Example to set Slurm node to `DOWN` or `DRAIN`:

```
$ scontrol update nodename=my-queue-dy-my-compute-resource-1 state=down reason=your_reason
scontrol update nodename=my-queue-dy-my-compute-resource-1 state=drain reason=your_reason
```