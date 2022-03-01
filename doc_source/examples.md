# Examples<a name="examples"></a>

The following example configurations demonstrate AWS ParallelCluster configurations using Slurm, Torque, and AWS Batch schedulers\.

**Note**  
Starting with version 2\.11\.5, AWS ParallelCluster doesn't support the use of SGE or Torque schedulers\.

**Contents**
+ [Slurm Workload Manager \(`slurm`\)](#example.slurm)
+ [Son of Grid Engine \(`sge`\) and Torque Resource Manager \(`torque`\)](#example.torque)
+ [AWS Batch \(`awsbatch`\)](#example.awsbatch)

## Slurm Workload Manager \(`slurm`\)<a name="example.slurm"></a>

The following example launches a cluster with the `slurm` scheduler\. The example configuration launches 1 cluster with 2 job queues\. The first queue, `spot`, initially has 2 `t3.micro` Spot instances available\. It can scale up to a maximum of 10 instances, and scale down to a minimum of 1 instance when no jobs have been run for 10 minutes \(adjustable using the [`scaledown_idletime`](scaling-section.md#scaledown-idletime) setting\)\. The second queue, `ondemand`, starts with no instances and can scale up to a maximum of 5 `t3.micro` On\-Demand instances\.

```
[global]
update_check = true
sanity_check = true
cluster_template = slurm

[aws]
aws_region_name = <your AWS Region>

[vpc public]
master_subnet_id = <your subnet>
vpc_id = <your VPC>

[cluster slurm]
key_name = <your EC2 keypair name>
base_os = alinux2                   # optional, defaults to alinux2
scheduler = slurm
master_instance_type = t3.micro     # optional, defaults to t3.micro
vpc_settings = public
queue_settings = spot,ondemand

[queue spot]
compute_resource_settings = spot_i1
compute_type = spot                 # optional, defaults to ondemand

[compute_resource spot_i1]
instance_type = t3.micro
min_count = 1                       # optional, defaults to 0
initial_count = 2                   # optional, defaults to 0

[queue ondemand]
compute_resource_settings = ondemand_i1

[compute_resource ondemand_i1]
instance_type = t3.micro
max_count = 5                       # optional, defaults to 10
```

## Son of Grid Engine \(`sge`\) and Torque Resource Manager \(`torque`\)<a name="example.torque"></a>

**Note**  
This example only applies to AWS ParallelCluster versions up to and including version 2\.11\.4\. Starting with version 2\.11\.5, AWS ParallelCluster doesn't support the use of SGE or Torque schedulers\.

The following example launches a cluster with the `torque` or `sge` scheduler\. To use SGE, change `scheduler = torque` to `scheduler = sge`\. The example configuration allows a maximum of 5 concurrent nodes, and scales down to two when no jobs have run for 10 minutes\.

```
[global]
update_check = true
sanity_check = true
cluster_template = torque

[aws]
aws_region_name = <your AWS Region>

[vpc public]
master_subnet_id = <your subnet>
vpc_id = <your VPC>

[cluster torque]
key_name = <your EC2 keypair name>but they aren't eligible for future updates
base_os = alinux2                   # optional, defaults to alinux2
scheduler = torque                  # optional, defaults to sge
master_instance_type = t3.micro     # optional, defaults to t3.micro
vpc_settings = public
initial_queue_size = 2              # optional, defaults to 0
maintain_initial_size = true        # optional, defaults to false
max_queue_size = 5                  # optional, defaults to 10
```

**Note**  
Starting with version 2\.11\.5, AWS ParallelCluster doesn't support the use of SGE or Torque schedulers\. If you use these versions, you can continue using them, or troubleshooting support from the AWS service and AWS Support teams\.

## AWS Batch \(`awsbatch`\)<a name="example.awsbatch"></a>

The following example launches a cluster with the `awsbatch` scheduler\. It's set to select the better instance type based on your job resource needs\.

The example configuration allows a maximum of 40 concurrent vCPUs, and scales down to zero when no jobs have run for 10 minutes \(adjustable using the [`scaledown_idletime`](scaling-section.md#scaledown-idletime) setting\)\.

```
[global]
update_check = true
sanity_check = true
cluster_template = awsbatch

[aws]
aws_region_name = <your AWS Region>

[vpc public]
master_subnet_id = <your subnet>
vpc_id = <your VPC>

[cluster awsbatch]
scheduler = awsbatch
compute_instance_type = optimal # optional, defaults to optimal
min_vcpus = 0                   # optional, defaults to 0
desired_vcpus = 0               # optional, defaults to 4
max_vcpus = 40                  # optional, defaults to 20
base_os = alinux2               # optional, defaults to alinux2, controls the base_os of
                                # the head node and the docker image for the compute fleet
key_name = <your EC2 keypair name>
vpc_settings = public
```