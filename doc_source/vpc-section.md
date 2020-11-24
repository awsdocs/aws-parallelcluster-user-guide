# `[vpc]` section<a name="vpc-section"></a>

**Topics**
+ [`additional_sg`](#additional-sg)
+ [`compute_subnet_cidr`](#compute-subnet-cidr)
+ [`compute_subnet_id`](#compute-subnet-id)
+ [`master_subnet_id`](#master-subnet-id)
+ [`ssh_from`](#ssh-from)
+ [`use_public_ips`](#use-public-ips)
+ [`vpc_id`](#vpc-id)
+ [`vpc_security_group_id`](#vpc-security-group-id)

Specifies Amazon VPC configuration settings\.

The format is `[vpc vpc-name]`\. *vpc\-name* must start with a letter, contain no more than 30 characters, and only contain letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

```
[vpc public]
vpc_id = vpc-xxxxxx
master_subnet_id = subnet-xxxxxx
```

## `additional_sg`<a name="additional-sg"></a>

**\(Optional\)** Provides an additional Amazon VPC security group Id for all instances\.

The default value is `NONE`\.

```
additional_sg = sg-xxxxxx
```

## `compute_subnet_cidr`<a name="compute-subnet-cidr"></a>

**\(Optional\)** Specifies a Classless Inter\-Domain Routing \(CIDR\) block\. Use this parameter if you want AWS ParallelCluster to create a compute subnet\.

```
compute_subnet_cidr = 10.0.100.0/24
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `compute_subnet_id`<a name="compute-subnet-id"></a>

**\(Optional\)** Specifies the ID of an existing subnet in which to provision the compute nodes\.

If not specified, [`compute_subnet_id`](#compute-subnet-id) uses the value of [`master_subnet_id`](#master-subnet-id)\.

If the subnet is private, you must set up NAT for web access\.

```
compute_subnet_id = subnet-xxxxxx
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `master_subnet_id`<a name="master-subnet-id"></a>

**\(Required\)** Specifies the ID of an existing subnet in which to provision the head node\.

```
master_subnet_id = subnet-xxxxxx
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `ssh_from`<a name="ssh-from"></a>

**\(Optional\)** Specifies a CIDR\-formatted IP range to allow SSH access from\.

This parameter is used only when AWS ParallelCluster creates the security group\.

The default value is `0.0.0.0/0`\.

```
ssh_from = 0.0.0.0/0
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `use_public_ips`<a name="use-public-ips"></a>

**\(Optional\)** Defines whether to assign public IP addresses to compute instances\.

If set to `true`, an Elastic IP address is associated to the head node\.

If set to `false`, the head node has a public IP \(or not\) according to the value of the "Auto\-assign Public IP" subnet configuration parameter\.

For examples, see [networking configuration](networking.md)\.

The default value is `true`\.

```
use_public_ips = true
```

**Important**  
By default, all AWS accounts are limited to five \(5\) Elastic IP addresses for each Region\. For more information, see [Elastic IP address limit](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html#using-instance-addressing-limit) in *Amazon EC2 User Guide for Linux Instances*\.

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)

## `vpc_id`<a name="vpc-id"></a>

**\(Required\)** Specifies the ID of the Amazon VPC in which to provision the cluster\.

```
vpc_id = vpc-xxxxxx
```

[Update policy: If this setting is changed, the update is not allowed.](using-pcluster-update.md#update-policy-fail)

## `vpc_security_group_id`<a name="vpc-security-group-id"></a>

**\(Optional\)** Specifies the use of an existing security group for all instances\.

The default value is `NONE`\.

```
vpc_security_group_id = sg-xxxxxx
```

The security group created by AWS ParallelCluster allows SSH access using port 22 from the addresses specified in the [`ssh_from`](#ssh-from) setting, or all IPv4 addresses \(`0.0.0.0/0`\) if the [`ssh_from`](#ssh-from) setting isn't specified\. If NICE DCV is enabled, then the security group allows access to NICE DCV using port 8443 \(or whatever the [`port`](dcv-section.md#dcv-section-port) setting specifies\) from the addresses specified in the [`access_from`](dcv-section.md#dcv-section-access-from) setting, or all IPv4 addresses \(`0.0.0.0/0`\) if the [`access_from`](dcv-section.md#dcv-section-access-from) setting isn't specified\.

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)