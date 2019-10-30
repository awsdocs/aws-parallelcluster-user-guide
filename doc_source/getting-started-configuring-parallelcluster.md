# Configuring AWS ParallelCluster<a name="getting-started-configuring-parallelcluster"></a>

After you install AWS ParallelCluster, complete the following configuration steps\.

First, set up your AWS credentials\. For more information, see [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) in the *AWS CLI user guide*\. 

```
$ aws configure
  AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [us-east-1]: us-east-1
Default output format [None]:
```

```
$ pcluster configure
```

The configure wizard prompts you for all of the information that's needed to create your cluster\.

Enter your cluster template name, which is the logical name of the template from which the cluster is created\.

```
Cluster Template [mycluster]:
```

Provide your AWS Access and Secret Keys\. Enter the keys for an IAM user that has administrative permissions\. The keys can also be read from your environment variables or the AWS CLI configuration\.

```
AWS Access Key ID []:
AWS Secret Access Key ID []:
```

From the list of valid AWS Region identifiers, choose the Region in which you want your cluster to run\.

```
Acceptable Values for AWS Region ID:
    eu-north-1
    ap-south-1
    eu-west-3
    eu-west-2
    eu-west-1
    ap-northeast-2
    ap-northeast-1
    sa-east-1
    ca-central-1
    ap-southeast-1
    ap-southeast-2
    eu-central-1
    us-east-1
    us-east-2
    us-west-1
    us-west-2
AWS Region ID []:
```

Choose a descriptive name for your VPC\. For example, use a name such as `production` or `test`\.

```
VPC Name [myvpc]:
```

Choose a key pair that already exists in Amazon EC2 in order to log into your master instance\. If you do not already have a key pair, see [EC2 Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Linux Instances*\.

```
Acceptable Values for Key Name:
    keypair1
    keypair-test
    production-key
Key Name []:
```

Choose the ID of the VPC into which you want your cluster launched\.

```
Acceptable Values for VPC ID:
    vpc-1kd24879
    vpc-blk4982d
VPC ID []:
```

Choose the subnet in which you want your master node to run\.

```
Acceptable Values for Master Subnet ID:
    subnet-9k284a6f
    subnet-1k01g357
    subnet-b921nv04
Master Subnet ID []:
```

When you have completed the preceding steps, a simple cluster launches into a VPC, using an existing subnet that supports public IP's \(the route table for the subnet is `0.0.0.0/0 => igw-xxxxxx`\)\. Note the following:
+ The VPC must have `DNS Resolution = yes` and `DNS Hostnames = yes`\.
+ The VPC should also have DHCP options with the correct `domain-name` for the region\. The default DHCP Option Set already specifies the required *AmazonProvidedDNS*\. If specifying more than one domain name server, see [DHCP Options Sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\.

When all settings contain valid values, you can launch the cluster by running the create command\.

```
$ pcluster create mycluster
```

After the cluster reaches the "CREATE\_COMPLETE" status, you can connect to it by using your normal SSH client settings\. For more details on connecting to Amazon EC2 instances, see the [EC2 User Guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-connect-to-instance-linux) in the *Amazon EC2 User Guide for Linux Instances*\.
