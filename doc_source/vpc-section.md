# `[vpc]` Section<a name="vpc-section"></a>

**Topics**
+ [`vpc_id`](#vpc-id)
+ [`master_subnet_id`](#master-subnet-id)
+ [`ssh_from`](#ssh-from)
+ [`additional_sg`](#additional-sg)
+ [`compute_subnet_id`](#compute-subnet-id)
+ [`compute_subnet_cidr`](#compute-subnet-cidr)
+ [`use_public_ips`](#use-public-ips)
+ [`vpc_security_group_id`](#vpc-security-group-id)

Specifies Amazon VPC configuration settings\.

The format is `[vpc <vpcname>]`\.

```
[vpc public]
vpc_id = vpc-xxxxxx
master_subnet_id = subnet-xxxxxx
```

## `vpc_id`<a name="vpc-id"></a>

Specifies the ID of the Amazon VPC in which to provision the cluster\.

```
vpc_id = vpc-xxxxxx
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

## `additional_sg`<a name="additional-sg"></a>

Provides an additional Amazon VPC security group Id for all instances\.

The default value is `NONE`\.

```
additional_sg = sg-xxxxxx
```

## `compute_subnet_id`<a name="compute-subnet-id"></a>

Specifies the ID of an existing subnet in which to provision the compute nodes\.

This cannot be the same as `master_subnet_id`, if you'd like both the master and compute instances to be in the same subnet, only specify `master_subnet_id`.

If the subnet is private, you must set up NAT for web access\.

```
compute_subnet_id = subnet-xxxxxx
```

## `compute_subnet_cidr`<a name="compute-subnet-cidr"></a>

Specifies a CIDR block\. Use this parameter if you want AWS ParallelCluster to create a compute subnet\.

```
compute_subnet_cidr = 10.0.100.0/24
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

## `vpc_security_group_id`<a name="vpc-security-group-id"></a>

Specifies the use of an existing security group for all instances\.

The default value is `NONE`\.

```
vpc_security_group_id = sg-xxxxxx
```
