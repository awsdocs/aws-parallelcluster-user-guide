# `[vpc]` Section<a name="vpc-section"></a>

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

The format is `[vpc <vpcname>]`\.

```
[vpc public]
vpc_id = vpc-xxxxxx
master_subnet_id = subnet-xxxxxx
```

## `additional_sg`<a name="additional-sg"></a>

Provides an additional Amazon VPC security group Id for all instances\.

The default value is `NONE`\.

```
additional_sg = sg-xxxxxx
```

## `compute_subnet_cidr`<a name="compute-subnet-cidr"></a>

Specifies a CIDR block\. Use this parameter if you want AWS ParallelCluster to create a compute subnet\.

```
compute_subnet_cidr = 10.0.100.0/24
```

## `compute_subnet_id`<a name="compute-subnet-id"></a>

Specifies the ID of an existing subnet in which to provision the compute nodes\.

If not specified, `compute_subnet_id` uses the value of `master_subnet_id`\.

If the subnet is private, you must set up NAT for web access\.

```
compute_subnet_id = subnet-xxxxxx
```

## `master_subnet_id`<a name="master-subnet-id"></a>

Specifies the ID of an existing subnet in which to provision the master node\.

```
master_subnet_id = subnet-xxxxxx
```

## `ssh_from`<a name="ssh-from"></a>

Specifies a CIDR\-formatted IP range to allow SSH access from\.

This parameter is used only when AWS ParallelCluster creates the security group\.

The default value is `0.0.0.0/0`\.

```
ssh_from = 0.0.0.0/0
```

## `use_public_ips`<a name="use-public-ips"></a>

Defines whether to assign public IP addresses to compute instances\.

If set to `true`, an Elastic IP is associated to the master instance\.

If set to `false`, the master instance has a public IP \(or not\) according to the value of the "Auto\-assign Public IP" subnet configuration parameter\.

For examples, see [networking configuration](networking.md)\.

The default value is `true`\.

```
use_public_ips = true
```

## `vpc_id`<a name="vpc-id"></a>

Specifies the ID of the Amazon VPC in which to provision the cluster\.

```
vpc_id = vpc-xxxxxx
```

## `vpc_security_group_id`<a name="vpc-security-group-id"></a>

Specifies the use of an existing security group for all instances\.

The default value is `NONE`\.

```
vpc_security_group_id = sg-xxxxxx
```

The security group created by AWS ParallelCluster allows SSH access using port 22 from the addresses specified in the [`ssh_from`](#ssh-from) setting, or all IPv4 addresses \(`0.0.0.0/0`\) if the [`ssh_from`](#ssh-from) setting is not specified\. If NICE DCV is enabled then the security group allows access to NICE DCV using port 8443 \(or whatever the [`port`](dcv-section.md#dcv-section-port) setting specifies\) from the addresses specified in the [`access_from`](dcv-section.md#dcv-section-access-from) setting, or all IPv4 addresses \(`0.0.0.0/0`\) if the [`access_from`](dcv-section.md#dcv-section-access-from) setting is not specified\.