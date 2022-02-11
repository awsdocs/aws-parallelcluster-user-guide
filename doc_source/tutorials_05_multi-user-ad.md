# Integrate Active Directory over LDAP<a name="tutorials_05_multi-user-ad"></a>

In this tutorial, you create a multiple user environment\. This environment includes an AWS ParallelCluster that's integrated with an AWS Managed Microsoft AD \(Active Directory\) at `corp.pcluster.com`\. It also includes a `ReadOnly` user and a login user\. You can use either the automated path or the manual path to create the VPC, an Active Directory \(AD\), and EC2 instances needed for AD over LDAP integration\. After you create these resources, you proceed to configure and create your cluster integrated with your AD\. After the cluster is created, you log in as the user you created\. For more information about the configuration that you create in this tutorial, see [Multiple user access to clusters](multi-user-v3.md) and the [DirectoryService configuration section](DirectoryService-v3.md)\.

In this tutorial, the cluster that you create communicates with the Active Directory \(AD\) over Lightweight Directory Access Protocol \(LDAP\) to authenticate users that were defined in the AD\. LDAP *doesn't* provide encryption\. To ensure secure transmission of potentially sensitive information, we strongly recommend that you use LDAP over TLS/SSL \(abbreviated LDAPS\) for clusters integrated with ADs\. For more information, see [Enable server\-side LDAPS using AWS Managed Microsoft AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_ldap_server_side.html) in the AWS Directory Service *Administration Guide*\.

The focus of this tutorial is the process of how to create an environment that supports multiple user access to clusters\. This tutorial doesn't focus on how you create and use an AWS Directory Service AD itself\. The steps you take to set up an AWS Managed Microsoft AD in this tutorial are provided for testing purposes only\. They *aren't* provided to replace the official documentation and best practices you can find at [AWS Managed Microsoft AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_microsoft_ad.html) and [Simple AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_simple_ad.html) in the *AWS Directory Service Administration Guide*\.

## Step 1: Create the AD infrastructure<a name="tutorials_05_multi-user-ad-step1"></a>

Choose the *Automated* tab to create the Active Directory \(AD\) infrastructure with an AWS CloudFormation quick create template\.

Choose the *Manual* tab to manually create the AD infrastructure\.

------
#### [ Automated ]

1. Sign in to the AWS Management Console

1. Open the link, [CloudFormation Quick Create \(region us\-east\-1\)](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=pcluster-ad&templateURL=https://pcluster-310.s3.amazonaws.com/ad-integration.yaml), to create the following resources in the CloudFormation console:
   + A VPC with two subnets and routing for public access \(if no VPC is specified\)\.
   + An AWS Managed Microsoft AD\.
   + An EC2 instance that's joined to the AD that you can use to manage the directory\.

1. In the **Quick create stack** page **Parameters** section, create the passwords for the following parameters:
   + **AdminPassword**
   + **ReadOnlyPassword**
   + **UserPassword**

   For **Keypair**, enter the name of an EC2 key pair\.

   Make note of the passwords\. You use them later on in this tutorial\.

1. Acknowledge each of the access capabilities at the bottom of the page by checking the boxes\.

1. Choose **Create stack**\.

1. After the CloudFormation stack has reached the `CREATE_COMPLETE` state, choose the **Outputs** tab of the stack\. Make a note of the output resource names and IDs because you will need to use them in later steps\. The outputs provide the information that's needed to create the cluster:  
![\[A diagram that shows the created stack outputs in the AWS Management Console.\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/ad-cfn.png)

1. Continue at [Step 2: Create the cluster](#tutorials_05_multi-user-ad-step2)\.

------
#### [ Manual ]

1. 

**Create the AD, add users, and create an AWS Secrets Manager secret\.**

   Create a VPC for the directory service with two subnets in different Availability Zones and an AWS Managed Microsoft AD\.
**Note**  
The directory and domain name is `corp.pcluster.com`\. The short name is `CORP`\.
Change the `Admin` password in the script\.
The AD takes at least 15 minutes to create\.

   You can use the following Python script to create the VPC, subnets and AD resources in your local AWS Region\. Save this file as `ad.py` and run it\.

   ```
   import boto3
   import time
   from pprint import pprint
   
   vpc_name = "PclusterVPC"
   ad_domain = "corp.pcluster.com"
   admin_password = "asdfASDF1234"
   
   ec2 = boto3.client("ec2")
   ds = boto3.client("ds")
   region = boto3.Session().region_name
   
   # Create the VPC, Subnets, IGW, Routes
   vpc = ec2.create_vpc(CidrBlock="10.0.0.0/16")["Vpc"]
   vpc_id = vpc["VpcId"]
   ec2.create_tags(Resources=[vpc_id], Tags=[{"Key": "Name", "Value": vpc_name}])
   subnet1 = ec2.create_subnet(VpcId=vpc_id, CidrBlock="10.0.0.0/17", AvailabilityZone=f"{region}a")["Subnet"]
   subnet1_id = subnet1["SubnetId"]
   ec2.create_tags(Resources=[subnet1_id], Tags=[{"Key": "Name", "Value": f"{vpc_name}/subnet1"}])
   ec2.modify_subnet_attribute(SubnetId=subnet1_id, MapPublicIpOnLaunch={"Value": True})
   subnet2 = ec2.create_subnet(VpcId=vpc_id, CidrBlock="10.0.128.0/17", AvailabilityZone=f"{region}b")["Subnet"]
   subnet2_id = subnet2["SubnetId"]
   ec2.create_tags(Resources=[subnet2_id], Tags=[{"Key": "Name", "Value": f"{vpc_name}/subnet2"}])
   ec2.modify_subnet_attribute(SubnetId=subnet2_id, MapPublicIpOnLaunch={"Value": True})
   igw = ec2.create_internet_gateway()["InternetGateway"]
   ec2.attach_internet_gateway(InternetGatewayId=igw["InternetGatewayId"], VpcId=vpc_id)
   route_table = ec2.describe_route_tables(Filters=[{"Name": "vpc-id", "Values": [vpc_id]}])["RouteTables"][0]
   ec2.create_route(RouteTableId=route_table["RouteTableId"], DestinationCidrBlock="0.0.0.0/0", GatewayId=igw["InternetGatewayId"])
   ec2.modify_vpc_attribute(VpcId=vpc_id, EnableDnsSupport={"Value": True})
   ec2.modify_vpc_attribute(VpcId=vpc_id, EnableDnsHostnames={"Value": True})
   
   # Create the Active Directory
   ad = ds.create_microsoft_ad(
       Name=ad_domain,
       Password=admin_password,
       Description="ParallelCluster AD",
       VpcSettings={"VpcId": vpc_id, "SubnetIds": [subnet1_id, subnet2_id]},
       Edition="Standard",
   )
   directory_id = ad["DirectoryId"]
   
   # Wait for completion
   print("Waiting for the directory to be created...")
   directories = ds.describe_directories(DirectoryIds=[directory_id])["DirectoryDescriptions"]
   directory = directories[0]
   while directory["Stage"] in {"Requested", "Creating"}:
       time.sleep(3)
       directories = ds.describe_directories(DirectoryIds=[directory_id])["DirectoryDescriptions"]
       directory = directories[0]
       
   dns_ip_addrs = directory["DnsIpAddrs"]
   
   pprint({"directory_id": directory_id,
           "vpc_id": vpc_id,
           "subnet1_id": subnet1_id,
           "subnet2_id": subnet2_id,
           "dns_ip_addrs": dns_ip_addrs})
   ```

   The following is example output from python script\.

   ```
   {
     "directory_id": "d-abcdef01234567890",
     "dns_ip_addrs": ["192.0.2.254", "203.0.113.237"],
     "subnet1_id": "subnet-021345abcdef6789",
     "subnet2_id": "subnet-1234567890abcdef0",
     "vpc_id": "vpc-021345abcdef6789"
   }
   ```

   Make a note of the output resource names and IDs\. You use them in later steps\.

1. 

**Create an EC2 instance, join it to your AD, and create users\.**

   Prepare to create users in your directory and store the password for a `ReadOnlyUser`\.

   1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

   1. In the **EC2 Dashboard**, choose **Launch Instance**\.

   1. Select a recent Amazon Linux 2 AMI, t2\.micro instance type\.

**In **Configure Instance Details**, choose the following values:**
      + **Network**: **PclusterVPC**
      + **Auto\-assign Public IP**: **Enable**
      + **Domain join directory**: **corp\.pcluster\.com**
      + **IAM Role**: Create or select one using having the AWS Secrets Manager permissions suggested by the helper\.
      + Choose **Review and Launch** to launch the instance\.
      + Make note of the InstanceId \(for example, i\-1234567890abcdef0\) and wait for the instance to finish launching\.

   1. After the instance has launched, use the following commands to connect to the instance:

      ```
      $ INSTANCE_ID="i-1234567890abcdef0"
      ```

      ```
      $ PUBLIC_IP=$(aws ec2 describe-instances \
       --instance-ids $INSTANCE_ID \
       --query "Reservations[0].Instances[0].PublicIpAddress" \
       --output text)
      ```

      ```
      $ ssh -i ~/.ssh/keys/keypair.pem ec2-user@$PUBLIC_IP
      ```

      Install necessary software and join the realm\.

      ```
      $ sudo yum -y install sssd realmd oddjob oddjob-mkhomedir adcli samba-common samba-common-tools krb5-workstation openldap-clients policycoreutils-python
      ```

      Replace with your admin password:

      ```
      $ ADMIN_PW="asdfASDF1234"
      ```

      ```
      $ echo $ADMIN_PW | sudo realm join -U Admin corp.pcluster.com
      ```

      Output:

      ```
      Password for Admin:
      ```

      If the above has succeeded, add user\(s\) to the AD with the following command:

      ```
      $ echo $ADMIN_PW | adcli create-user -x -U Admin --domain=corp.pcluster.com --display-name=ReadOnlyUser ReadOnlyUser
      ```

      ```
      $ echo $ADMIN_PW | adcli create-user -x -U Admin --domain=corp.pcluster.com --display-name=user000 user000
      ```

      Verify the users have been created:

      The directory\_id is an output of the python script\.

      ```
      $ DIRECTORY_IP="192.0.2.254"
      ```

      ```
      $ ldapsearch -x -h $DIRECTORY_IP -D Admin -w $ADMIN_PW -b "cn=ReadOnlyUser,ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com"
      ```

      ```
      $ ldapsearch -x -h $DIRECTORY_IP -D Admin -w $ADMIN_PW -b "cn=user000,ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com"
      ```

      At this point you can disconnect from the instance \(and terminate it\)\.

      When you create a user with the `ad-cli`, the user is disabled by default\.

      Reset and activate the user passwords from your local machine:
**Note**  
`ro-p@ssw0rd` below is the password of `ReadOnlyUser`, retrieved via secrets manager \(explained below\)\.
`user-p@ssw0rd` is the password of a cluster user, provided when you connect \(`ssh`\) to the cluster\.

      The `directory-id` is an output of the python script\.

      ```
      $ DIRECTORY_ID="d-abcdef01234567890"
      ```

      ```
      $ aws ds reset-user-password \
      --directory-id $DIRECTORY_ID \
      --user-name "ReadOnlyUser" \
      --new-password "ro-p@ssw0rd" \
      --region "region-id"
      ```

      ```
      $ aws ds reset-user-password \
      --directory-id $DIRECTORY_ID \
      --user-name "user000" \
      --new-password "user-p@ssw0rd" \
      --region "region-id"
      ```

1. **Add the password to a Secrets Manager secret\.**

   Now that you have created a `ReadOnlyUser` and set the password, store it in a secret that AWS ParallelCluster uses for validating logins\.

   Use Secrets Manager to create a new secret to hold the password for the `ReadOnlyUser` as the value\. The secret value format must be text only, not JSON\. Make note of the secret ARN for future steps\.

   ```
   $ aws secretsmanager create-secret --name "ADSecretPassword" \
   --secret-string "ro-p@ssw0rd" \
   --query ARN \
   --output text
   ```

   Output:

   ```
   arn:aws:secretsmanager:region-id:123456789012:secret:ADSecretPassword-1234
   ```

1. Continue at [Step 2: Create the cluster](#tutorials_05_multi-user-ad-step2)\.

------

## Step 2: Create the cluster<a name="tutorials_05_multi-user-ad-step2"></a>

The environment is set up to create a cluster that can authenticate users against the AD\.

Create a simple cluster configuration and provide the settings relevant to connecting to the AD\. For more information, see the [`DirectoryService` section](DirectoryService-v3.md)\.

Save the example cluster configuration as a `config.yaml` file:

**Note**  
`KeyName`: One of your EC2 keypairs\.
`SubnetId / SubnetIds`: One of the subnet IDs provided in the output of the CloudFormation quick create stack \(automated tutorial\) or python script \(manual tutorial\)\.
`Region`: The Region where you created the AD infrastructure\.
`DomainAddr`: This IP address is one of the DNS addresses of your AD service\.
`PasswordSecretArn`: The ARN of the secret that contains the password for the `DomainReadOnlyUser`\.

```
HeadNode:
  InstanceType: t2.micro
  Ssh:
    KeyName: keypair
  Networking:
    SubnetId: subnet-abcdef01234567890
Scheduling:
  Scheduler: slurm
  SlurmQueues:
    - Name: queue0
      ComputeResources:
        - Name: queue0-t2-micro
          MinCount: 0
          MaxCount: 4
          InstanceType: t2.micro
      Networking:
        SubnetIds:
          - subnet-abcdef01234567890
Region: region-id
Image:
  Os: alinux2
DirectoryService:
  DomainName: dc=corp,dc=pcluster,dc=com
  DomainAddr: ldap://192.0.2.254,ldap://203.0.113.237
  PasswordSecretArn: arn:aws:secretsmanager:region-id:123456789012:secret:ADSecretPassword-AAAAA
  DomainReadOnlyUser: cn=ReadOnlyUser,ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com
  AdditionalSssdConfigs:
    ldap_auth_disable_tls_never_use_in_production: True
```

Create your cluster with the following command:

```
$ pcluster create-cluster --cluster-name "ad-cluster" --cluster-configuration "./config.yaml"
```

Output:

```
{
  "cluster": {
    "clusterName": "ad-cluster",
    "cloudformationStackStatus": "CREATE_IN_PROGRESS",
    "cloudformationStackArn": "arn:aws:cloudformation:region-id:123456789012:stack/ad-cluster/1234567-abcd-0123-def0-abcdef0123456",
    "region": "region-id",
    "version": "3.1.1",
    "clusterStatus": "CREATE_IN_PROGRESS"
  },
  "validationMessages": [
    {
      "level": "WARNING",
      "type": "DomainAddrValidator",
      "message": "The use of the ldaps protocol is strongly encouraged for security reasons."
    },
    {
      "level": "WARNING",
      "type": "LdapTlsReqCertValidator",
      "message": "For security reasons it's recommended to use hard or demand"
    }
  ]
}
```

## Step 3: Connect to the cluster as a user<a name="tutorials_05_multi-user-ad-step3"></a>

You can determine the status of the cluster with the following commands:

```
$ pcluster describe-cluster -n ad-cluster --region "region-id" --query "clusterStatus"
```

Output:

```
"CREATE_IN_PROGRESS" / "CREATE_COMPLETE"
```

When the status reaches `"CREATE_COMPLETE"`, log in with the created user name and password:

```
$ HEAD_NODE_IP=$(pcluster describe-cluster -n "ad-cluster" --region "region-id" --query headNode.publicIpAddress | xargs echo)
```

```
$ ssh user000@$HEAD_NODE_IP
```

You can log in without the password by providing the SSH key that was created for the new user at `/home/user000@HEAD_NODE_IP/.ssh/id_rsa`\.

If the `ssh` command succeeded, you have successfully connected to the cluster as a user that is authenticated using AD\.

## Step 4: Clean up<a name="tutorials_05_multi-user-ad-step4"></a>

1. From your local machine, delete the cluster\.

   ```
   $ pcluster delete-cluster --cluster-name "ad-cluster" --region "region-id"
   ```

   Output:

   ```
   {
     "cluster": {
       "clusterName": "ad-cluster",
       "cloudformationStackStatus": "DELETE_IN_PROGRESS",
       "cloudformationStackArn": "arn:aws:cloudformation:region-id:123456789012:stack/ad-cluster/1234567-abcd-0123-def0-abcdef0123456",
       "region": "region-id",
       "version": "3.1.1",
       "clusterStatus": "DELETE_IN_PROGRESS"
     }
   }
   ```

1. Check the status of the cluster delete\.

   ```
   $ pcluster describe-cluster --cluster-name "ad-cluster" --region "region-id" --query "clusterStatus"
   ```

   Output:

   ```
   "DELETE_IN_PROGRESS"
   ```

   After the cluster delete is complete, proceed to the next step\.

3\. Delete the directory resources\.

------
#### [ Automated ]

**Delete the Active Directory**

1. From [https://console\.aws\.amazon\.com/cloudformation/](https://console.aws.amazon.com/cloudformation/)

1. Choose **Stacks** in the navigation pane\.

1. From the list of stacks, choose the AD stack, for example `pcluster-ad`\.

1. Choose **Delete**\.

------
#### [ Manual ]

1. 

**Delete the EC2 instance\.**

   1. From [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/), choose **Instances** in the navigation pane\.

   1. From the list of instances, choose the instance that you created to add users to the directory\.

   1. Choose **Instance state**, then **Terminate**\.

1. 

**Delete the Active Directory resources\.**

   Get the following resource IDs from the output of the python script `ad.py`:
   + AD ID
   + AD subnet IDs
   + AD VPC ID

   1. 

**Use `boto3` in a Python 3 environment to delete the resources\.**

      Start with imports:

      ```
      $ import boto3
      ds = boto3.client("ds")
      ec2 = boto3.client("ec2")
      secret = boto3.client("secretsmanager")
      region = boto3.Session().region_name
      ```

   1. 

**Delete the directory\.**

      ```
      $ ds.delete_directory(DirectoryId="d-abcdef0123456789")
      ```

      Output:

      ```
      {
      	"DirectoryId": "d-abcdef01234567890",
      	"ResponseMetadata": {
      		"RequestId": "1234567-abcd-0123-def0-abcdef0123456",
      		"HTTPStatusCode": 200,
      		"HTTPHeaders": {
      			"x-amzn-requestid": "1234567-abcd-0123-def0-abcdef0123456",
      			"content-type": "application/x-amz-json-1.1",
      			"content-length": "30",
      			"date": "Mon, 17 Jan 2022 17:25:48 GMT"
      		},
      		"RetryAttempts": 0
      	}
      }
      ```

   1. 

**Verify that the directory was deleted\.**

      This can take up to 30 minutes\.

      ```
      $ ds.describe_directories(DirectoryIds = ["d-abcdef0123456789"])
      ```

      Output:

      ```
      {
      	"DirectoryDescriptions": [],
      	"ResponseMetadata": {
      		"RequestId": "1234567-abcd-0123-def0-abcdef0123456",
      		"HTTPStatusCode": 200,
      		"HTTPHeaders": {
      			"x-amzn-requestid": "1234567-abcd-0123-def0-abcdef0123456",
      			"content-type": "application/x-amz-json-1.1",
      			"content-length": "28",
      			"date": "Sun, 23 Jan 2022 17:05:07 GMT"
      		},
      		"RetryAttempts": 0
      	}
      }
      ```

   1. 

**Delete the VPC non\-default security group\.**

      Get the security group ID:

      ```
      $ ec2.describe_security_groups(Filters=[{"Name":"vpc-id","Values":["vpc-021345abcdef6789"]}])
      ```

      Output:

      ```
      {
      	"SecurityGroups": [{
      		"Description": "default VPC security group",
      		"GroupName": "default", 
      		"IpPermissions": [{
      			"IpProtocol": "-1", 
      			"IpRanges": [], 
      			"Ipv6Ranges": [], 
      			"PrefixListIds": [], 
      			"UserIdGroupPairs": [{
      				"GroupId": "sg-abcdef01234567890",
      				"UserId": "123456789012"
      			}]
      		}], 
      		"OwnerId": "123456789012",
      		"GroupId": "sg-abcdef01234567890",
      		"IpPermissionsEgress": [{
      			"IpProtocol": "-1",
      			"IpRanges": [{
      				"CidrIp": "0.0.0.0/0"
      			}],
      			"Ipv6Ranges": [],
      			"PrefixListIds": [],
      			"UserIdGroupPairs": []
      		}],
      		"VpcId": "vpc-021345abcdef6789"
      	},
      	{
      		"Description": "launch-wizard-2 created 2022-01-23T08:05:48.031-08:00",
      		"GroupName": "launch-wizard-2",
      		"IpPermissions": [{
      			"FromPort": 22, "IpProtocol": "tcp",
      			"IpRanges": [{
      				"CidrIp": "0.0.0.0/0"
      			}],
      			"Ipv6Ranges": [],
      			"PrefixListIds": [],
      			"ToPort": 22,
      			"UserIdGroupPairs": []
      		}],
      		"OwnerId": "123456789012",
      		"GroupId": "sg-021345abcdef6789",
      		"IpPermissionsEgress": [{
      			"IpProtocol": "-1",
      			"IpRanges": [{
      				"CidrIp": "0.0.0.0/0"
      			}],
      			"Ipv6Ranges": [],
      			"PrefixListIds": [],
      			"UserIdGroupPairs": []
      		}],
      		"VpcId": "vpc-021345abcdef6789"
      	}],
      	"ResponseMetadata": {
      		"RequestId": "1234567-abcd-0123-def0-abcdef0123456",
      		"HTTPStatusCode": 200,
      		"HTTPHeaders": {
      			"x-amzn-requestid": "1234567-abcd-0123-def0-abcdef0123456",
      			"cache-control": "no-cache, no-store",
      			"strict-transport-security": "max-age=31536000; includeSubDomains",
      			"vary": "accept-encoding",
      			"content-type": "text/xml;charset=UTF-8",
      			"content-length": "2806",
      			"date": "Sun, 23 Jan 2022 17:06:39 GMT",
      			"server": "AmazonEC2"
      		},
      		"RetryAttempts": 0
      	}
      }
      ```

      Delete the security group\.

      ```
      $ ec2.delete_security_group(GroupId="sg-021345abcdef6789")
      ```

      Output:

      ```
      {
      	"ResponseMetadata":
      	{
      		"RequestId": "06691c7a-f504-4aed-97c4-91abd848b34d",
      		"HTTPStatusCode": 200, 
      		"HTTPHeaders": {
      			"x-amzn-requestid": "06691c7a-f504-4aed-97c4-91abd848b34d",
      			"cache-control": "no-cache, no-store",
      			"strict-transport-security": "max-age=31536000; includeSubDomains",
      			"content-type": "text/xml;charset=UTF-8",
      			"content-length": "239",
      			"date": "Sun, 23 Jan 2022 17:39:42 GMT",
      			"server": "AmazonEC2"
      		},
      		"RetryAttempts": 0
      	}
      }
      ```

   1. 

**Delete the subnets if they're no longer being used\.**

      ```
      $ ec2.delete_subnet("SubnetId"="subnet-1234567890abcdef")
      ```

      Output:

      ```
      {
      	"ResponseMetadata": {
      		"RequestId": "1234567-abcd-0123-def0-abcdef0123456",
      		"HTTPStatusCode": 200,
      		"HTTPHeaders": {
      			"x-amzn-requestid": "1234567-abcd-0123-def0-abcdef0123456",
      			"cache-control": "no-cache, no-store",
      			"strict-transport-security": "max-age=31536000; includeSubDomains",
      			"content-type": "text/xml;charset=UTF-8",
      			"content-length": "225",
      			"date": "Mon, 17 Jan 2022 18:10:08 GMT",
      			"server": "AmazonEC2"
      		},
      		"RetryAttempts": 0
      	}
      }
      ```

      ```
      $ ec2.delete_subnet("SubnetId"="subnet-021345abcdef6789")
      ```

      Output:

      ```
      {
      	"ResponseMetadata": {
      		"RequestId": "1234567-abcd-0123-def0-abcdef0123456",
      		"HTTPStatusCode": 200,
      		"HTTPHeaders": {
      			"x-amzn-requestid": "1234567-abcd-0123-def0-abcdef0123456",
      			"cache-control": "no-cache, no-store",
      			"strict-transport-security": "max-age=31536000; includeSubDomains",
      			"content-type": "text/xml;charset=UTF-8",
      			"content-length": "225",
      			"date": "Mon, 17 Jan 2022 18:10:08 GMT",
      			"server": "AmazonEC2"
      		},
      		"RetryAttempts": 0
      	}
      }
      ```

   1. 

**Detach and delete the internet gateway if it's no longer being used\.**

      Get the internet gateway ID:

      ```
      $ ec2.describe_internet_gateways("Filters"=[{"Name":"attachment.vpc-id","Values":["vpc-021345abcdef6789"]}])
      ```

      Output:

      ```
      {
      	"InternetGateways": [{
      		"Attachments": [{
      			"State": "available", 
      			"VpcId": "vpc-021345abcdef6789"
      		}], 
      		"InternetGatewayId": "igw-abcdef01234567890", 
      		"OwnerId": "123456789012", 
      		"Tags": []
      	}], 
      	"ResponseMetadata": {
      		"RequestId": "1234567-abcd-0123-def0-abcdef0123456", 
      		"HTTPStatusCode": 200, "HTTPHeaders": {
      			"x-amzn-requestid": "1234567-abcd-0123-def0-abcdef0123456", 
      			"cache-control": "no-cache, no-store", 
      			"strict-transport-security": "max-age=31536000; includeSubDomains", 
      			"content-type": "text/xml;charset=UTF-8", 
      			"content-length": "649", 
      			"date": "Sun, 23 Jan 2022 17:55:22 GMT", 
      			"server": "AmazonEC2"
      		},
      		"RetryAttempts": 0
      	}
      }
      ```

      Detach the internet gateway\.

      ```
      $ ec2.detach_internet_gateway(InternetGatewayId="igw-1234567890abcdef", VpcId="vpc-021345abcdef6789")
      ```

      Output:

      ```
      {
      	"ResponseMetadata": {
      		"RequestId": "1234567-abcd-0123-def0-abcdef0123456",
      		"HTTPStatusCode": 200,
      		"HTTPHeaders": {
      			"x-amzn-requestid": "1234567-abcd-0123-def0-abcdef0123456",
      			"cache-control": "no-cache, no-store",
      			"strict-transport-security": "max-age=31536000; includeSubDomains",
      			"content-type": "text/xml;charset=UTF-8",
      			"content-length": "243",
      			"date": "Mon, 17 Jan 2022 18:39:39 GMT",
      			"server": "AmazonEC2"
      		},
      		"RetryAttempts": 0
      	}
      }
      ```

      Delete the internet gateway\.

      ```
      $ ec2.delete_internet_gateway(InternetGatewayId="igw-1234567890abcdef")
      ```

      Output:

      ```
      {
      	"ResponseMetadata":	{
      		"RequestId": "1234567-abcd-0123-def0-abcdef0123456",
      		"HTTPStatusCode": 200,
      		"HTTPHeaders": {
      			"x-amzn-requestid": "1234567-abcd-0123-def0-abcdef0123456",
      			"cache-control": "no-cache, no-store",
      			"strict-transport-security": "max-age=31536000; includeSubDomains",
      			"content-type": "text/xml;charset=UTF-8",
      			"content-length": "243",
      			"date": "Mon, 17 Jan 2022 18:54:10 GMT",
      			"server": "AmazonEC2"
      		},
      		"RetryAttempts": 0
      	}
      }
      ```

   1. 

**Delete the VPC if it's no longer being used\.**

      ```
      $ ec2.delete_vpc("VpcId"="vpc-021345abcdef6789")
      ```

      Output:

      ```
      {
      	"ResponseMetadata": {
      		"RequestId": "1234567-abcd-0123-def0-abcdef0123456",
      		"HTTPStatusCode": 200,
      		"HTTPHeaders": {
      			"x-amzn-requestid": "1234567-abcd-0123-def0-abcdef0123456",
      			"cache-control": "no-cache, no-store",
      			"strict-transport-security": "max-age=31536000; includeSubDomains",
      			"content-type": "text/xml;charset=UTF-8",
      			"content-length": "219",
      			"date": "Mon, 17 Jan 2022 19:14:30 GMT",
      			"server": "AmazonEC2"
      		},
      		"RetryAttempts": 0
      	}
      }
      ```

   1. 

**Delete the Secrets Manager secret if it's no longer being used\.**

      ```
      $ secret.delete_secret("SecretId"="arn:aws:secretsmanager:region-id:123456789012:secret:ADSecretPassword-AAAAA")
      ```

      Output:

      ```
      {
      	"ARN": "arn:aws:secretsmanager:region-id:123456789012:secret:ADSecretPassword-123ABC",
      	"Name": "ADSecretPassword",
      	"DeletionDate": "datetime.datetime(2022, 2, 20, 15, 8, 39, 452000, tzinfo=tzlocal())",
      	"ResponseMetadata": {
      		"RequestId": "1234567-abcd-0123-def0-abcdef0123456",
      		"HTTPStatusCode": 200,
      		"HTTPHeaders": {
      			"date": "Fri, 21 Jan 2022 15:08:39 GMT",
      			"content-type": "application/x-amz-json-1.1",
      			"content-length": "134",
      			"connection": "keep-alive",
      			"x-amzn-requestid": "1234567-abcd-0123-def0-abcdef0123456"
      		}, 
      		"RetryAttempts": 0
      	}
      }
      ```

------