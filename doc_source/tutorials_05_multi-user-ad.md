# Integrating Active Directory<a name="tutorials_05_multi-user-ad"></a>

In this tutorial, you create a multiple user environment\. This environment includes an AWS ParallelCluster that's integrated with an AWS Managed Microsoft AD \(Active Directory\) at `corp.pcluster.com`\. It configures an `Admin` user to manage the directory, a `ReadOnly` user to read the directory, and a `user000` user to log into the cluster\. You can use either the automated path or the manual path to create the networking resources, an Active Directory \(AD\), and the EC2 instance you use to configure the AD\. Regardless of the path, the infrastructure that you create is pre\-configured to integrate AWS ParallelCluster using any of the following methods:
+ LDAPS with certificate verification \(recommended as the most secure option\)
+ LDAPS without certificate verification
+ LDAP

LDAP by itself *doesn't* provide encryption\. To ensure secure transmission of potentially sensitive information, we strongly recommend that you use LDAPS \(LDAP over TLS/SSL\) for clusters integrated with ADs\. For more information, see [Enable server\-side LDAPS using AWS Managed Microsoft AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_ldap_server_side.html) in the AWS Directory Service *Administration Guide*\.

After you create these resources, you proceed to configure and create your cluster integrated with your AD\. After the cluster is created, you log in as the user you created\. For more information about the configuration that you create in this tutorial, see [Multiple user access to clusters](multi-user-v3.md) and the [`DirectoryService`](DirectoryService-v3.md) configuration section\.

The focus of this tutorial is the process of how to create an environment that supports multiple user access to clusters\. This tutorial doesn't focus on how you create and use an AWS Directory Service AD itself\. The steps you take to set up an AWS Managed Microsoft AD in this tutorial are provided for testing purposes only\. They *aren't* provided to replace the official documentation and best practices you can find at [AWS Managed Microsoft AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_microsoft_ad.html) and [Simple AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_simple_ad.html) in the *AWS Directory Service Administration Guide*\.

**Prerequisites**
+ AWS ParallelCluster [is installed](install-v3-parallelcluster.md)\.
+ The AWS CLI [is installed and configured\.](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
+ You have an [EC2 key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)\.
+ You have an IAM role with the [permissions](iam-roles-in-parallelcluster-v3.md#iam-roles-in-parallelcluster-v3-example-user-policies) required to run the [`pcluster`](pcluster-v3.md) CLI\.

As you go through the tutorial, replace `inputs highlighted in red`, such as `region-id` and `d-abcdef01234567890`, with your values for names and IDs\. Replace `0123456789012` with your AWS account number\.

## Step 1: Create the AD infrastructure<a name="tutorials_05_multi-user-ad-step1"></a>

Choose the *Automated* tab to create the Active Directory \(AD\) infrastructure with an AWS CloudFormation quick create template\.

Choose the *Manual* tab to manually create the AD infrastructure\.

### Automated<a name="tutorials_05_multi-user-ad-step1-automated"></a>

1. Sign in to the AWS Management Console

1. Open the link, [CloudFormation Quick Create \(region us\-east\-1\)](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=pcluster-ad&templateURL=https://us-east-1-aws-parallelcluster.s3.amazonaws.com/templates/1-click/ad-integration.yaml), to create the following resources in the CloudFormation console:
   + A VPC with two subnets and routing for public access \(if no VPC is specified\)\.
   + An AWS Managed Microsoft AD\.
   + An EC2 instance that's joined to the AD that you can use to manage the directory\.

1. In the **Quick create stack** page **Parameters** section, enter passwords for the following parameters:
   + **AdminPassword**
   + **ReadOnlyPassword**
   + **UserPassword**

   For **Keypair**, enter the name of an EC2 key pair\.

   Make note of the passwords\. You use them later on in this tutorial\.

1. Acknowledge each of the access capabilities at the bottom of the page by checking the boxes\.

1. Choose **Create stack**\.

1. After the CloudFormation stack has reached the `CREATE_COMPLETE` state, choose the **Outputs** tab of the stack\. Make a note of the output resource names and IDs because you will need to use them in later steps\. The outputs provide the information that's needed to create the cluster:  
![\[A diagram that shows the created stack outputs in the AWS Management Console.\]](http://docs.aws.amazon.com/parallelcluster/latest/ug/images/ad-cfn.png)

1. To complete the exercises [\(Optional\) Step 2: Manage AD users and groups](#tutorials_05_multi-user-ad-step2), you need the directory ID\. Choose **Resources** and scroll down to make note of the directory ID\.

1. Continue at [\(Optional\) Step 2: Manage AD users and groups](#tutorials_05_multi-user-ad-step2) or [Step 3: Create the cluster](#tutorials_05_multi-user-ad-step3)\.

### Manual<a name="tutorials_05_multi-user-ad-step1-manual"></a>

Create a VPC for the directory service with two subnets in different Availability Zones and an AWS Managed Microsoft AD\.

#### Create the AD<a name="tutorials_05_multi-user-ad-step1-manual-ad"></a>

**Note**  
The directory and domain name is `corp.pcluster.com`\. The short name is `CORP`\.
Change the `Admin` password in the script\.
The AD takes at least 15 minutes to create\.

Use the following Python script to create the VPC, subnets and AD resources in your local AWS Region\. Save this file as `ad.py` and run it\.

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
time.sleep(30)
ec2.create_tags(Resources=[vpc_id], Tags=[{"Key": "Name", "Value": vpc_name}])
subnet1 = ec2.create_subnet(VpcId=vpc_id, CidrBlock="10.0.0.0/17", AvailabilityZone=f"{region}a")["Subnet"]
subnet1_id = subnet1["SubnetId"]
time.sleep(30)
ec2.create_tags(Resources=[subnet1_id], Tags=[{"Key": "Name", "Value": f"{vpc_name}/subnet1"}])
ec2.modify_subnet_attribute(SubnetId=subnet1_id, MapPublicIpOnLaunch={"Value": True})
subnet2 = ec2.create_subnet(VpcId=vpc_id, CidrBlock="10.0.128.0/17", AvailabilityZone=f"{region}b")["Subnet"]
subnet2_id = subnet2["SubnetId"]
time.sleep(30)
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

After the script completes, continue to the next step\.

#### Create an EC2 instance<a name="tutorials_05_multi-user-ad-step1-manual-instance"></a>

------
#### [ New EC2 console ]

1. Sign in to the AWS Management Console\.

1. If you don't have a role with the policies listed in step 4 attached, open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\. Otherwise, skip to step 5\.

1. Create the `ResetUserPassword` policy, replacing the red highlighted content with your AWS Region ID, Account ID, and the directory ID from the output of the script you ran to create the AD\.

   ResetUserPassword

   ```
   {
          "Statement": [
           {
               "Action": [
                   "ds:ResetUserPassword"
               ],
               "Resource": "arn:aws:ds:region-id:123456789012:directory/d-abcdef01234567890",
               "Effect": "Allow"
           }
       ]
   }
   ```

1. Create an IAM role with the following policies attached\.
   + AWS managed policy [AmazonSSMManagedInstanceCore](https://docs.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore)
   + AWS managed policy [AmazonSSMDirectoryServiceAccess](https://docs.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/AmazonSSMDirectoryServiceAccess)
   + ResetUserPassword policy

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the **EC2 Dashboard**, choose **Launch Instance**\.

1. In **Application and OS Images**, select a recent Amazon Linux 2 AMI\.

1. For **Instance type**, choose t2\.micro\.

1. For **Key pair**, choose a key pair\.

1. For **Network settings**, choose **Edit**\.

1. For **VPC**, select the directory VPC\.

1. Scroll down and select **Advanced details**\.

1. In **Advanced details**, **Domain join directory**, choose **corp\.pcluster\.com**\.

1. For **IAM Instance profile**, choose the role you created in step 1 or a role with policies listed in step 4 attached\.

1. In **Summary** choose **Launch instance**\.

1. Make note of the Instance ID \(for example, i\-1234567890abcdef0\) and wait for the instance to finish launching\.

1. After the instance has launched, continue to the next step\.

------
#### [ Old EC2 console ]

1. Sign in to the AWS Management Console\.

1. If you don't have a role with the policies listed in step 4 attached, open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\. Otherwise, skip to step 5\.

1. Create the `ResetUserPassword` policy, replacing the red highlighted content with your AWS Region ID, Account ID, and the directory ID from the output of the script you ran to create the AD\.

   ResetUserPassword

   ```
   {
          "Statement": [
           {
               "Action": [
                   "ds:ResetUserPassword"
               ],
               "Resource": "arn:aws:ds:region-id:123456789012:directory/d-abcdef01234567890",
               "Effect": "Allow"
           }
       ]
   }
   ```

1. Create an IAM role with the following policies attached\.
   + AWS managed policy [AmazonSSMManagedInstanceCore](https://docs.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore)
   + AWS managed policy [AmazonSSMDirectoryServiceAccess](https://docs.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/AmazonSSMDirectoryServiceAccess)
   + ResetUserPassword policy

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the **EC2 Dashboard**, choose **Launch instance**\.

1. In **Choose AMI**, **Select** a recent Amazon Linux 2 AMI\.

1. For **Instance type**, choose **t2\.micro**\.

1. Choose **Next: Configure Instance Details**\.

1. For **Network**, choose the directory VPC\.

1. For **Domain join directory**, choose the directory\.

1. For **IAM role**, choose the role you created in step 1 or a role with policies listed in step 4 attached\.

1. Choose **Review and Launch**, **Launch**\.

1. Select a key pair\.

1. Choose **Launch Instances**\.

1. Make note of the Instance ID \(for example, i\-1234567890abcdef0\) and wait for the instance to finish launching\.

1. After the instance has launched, continue to the next step\.

------

#### Join your instance to the AD<a name="tutorials_05_multi-user-ad-step1-manual-join"></a>

1. 

**Connect to your instance and join the AD realm as `admin`**

   Run the following commands to connect to the instance:

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

1. 

**Install necessary software and join the realm\.**

   ```
   $ sudo yum -y install sssd realmd oddjob oddjob-mkhomedir adcli samba-common samba-common-tools krb5-workstation openldap-clients policycoreutils-python
   ```

1. 

**Replace the Admin password with your `admin` password:**

   ```
   $ ADMIN_PW="asdfASDF1234"
   ```

   ```
   $ echo $ADMIN_PW | sudo realm join -U Admin corp.pcluster.com
   Password for Admin:
   ```

   If the above has succeeded, you are joined to the realm and can proceed to the next step\.

#### Add users to the AD<a name="tutorials_05_multi-user-ad-step1-manual-join-add-users"></a>

1. 

**Create the ReadOnlyUser and an additional user\.**

   In this step, you use [adcli](https://www.mankier.com/package/adcli) and [openldap\-clients](https://www.mankier.com/package/openldap-clients) tools that you installed in a preceding step\.

   ```
   $ echo $ADMIN_PW | adcli create-user -x -U Admin --domain=corp.pcluster.com --display-name=ReadOnlyUser ReadOnlyUser
   ```

   ```
   $ echo $ADMIN_PW | adcli create-user -x -U Admin --domain=corp.pcluster.com --display-name=user000 user000
   ```

1. **Verify the users have been created:**

   The directory DNS IP addresses are outputs of the python script\.

   ```
   $ DIRECTORY_IP="192.0.2.254"
   ```

   ```
   $ ldapsearch -x -h $DIRECTORY_IP -D Admin -w $ADMIN_PW -b "cn=ReadOnlyUser,ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com"
   ```

   ```
   $ ldapsearch -x -h $DIRECTORY_IP -D Admin -w $ADMIN_PW -b "cn=user000,ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com"
   ```

   When you create a user with the `ad-cli`, the user is disabled by default\.

1. 

****Reset and activate the user passwords from your local machine:****

   Log out of your EC2 instance\.
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
   --region region_id \
   --secret-string "ro-p@ssw0rd" \
   --query ARN \
   --output text
   arn:aws:secretsmanager:region-id:123456789012:secret:ADSecretPassword-1234
   ```

#### LDAPS with certificate verification \(recommended\) setup<a name="tutorials_05_multi-user-ad-step1-manual-ldaps"></a>

Make a note of resource IDs\. You'll use them in steps later on\.

1. 

**Generate domain certificate, locally:**

   ```
   $ PRIVATE_KEY="corp-pcluster-com.key"
   CERTIFICATE="corp-pcluster-com.crt"
   printf ".\n.\n.\n.\n.\ncorp.pcluster.com\n.\n" | openssl req -x509 -sha256 -nodes -newkey rsa:2048 -keyout $PRIVATE_KEY -days 365 -out $CERTIFICATE
   ```

1. 

**Store the certificate to Secrets Manager in order to make it retrievable from within the cluster later on:**

   ```
   $ aws secretsmanager create-secret --name pcluster-cert \
     --secret-string file://$CERTIFICATE \
     --region region-id
   {
     "ARN": "arn:aws:secretsmanager:region-id:123456789012:secret:plcuster-cert-123abc",
     "Name": "pcluster-cert",
     "VersionId": "14866070-092a-4d5a-bcdd-9219d0566b9c"
   }
   ```

1. Add the following policy to the IAM role you created to join the EC2 instance to the AD domain\.

   PutDomainCertificateSecrets

   ```
   {
       "Statement": [
           {
               "Action": [
                   "secretsmanager:PutSecretValue"
               ],
               "Resource": [
                   "arn:aws:secretsmanager:region-id:123456789012:secret:plcuster-cert-123abc",
               ],
               "Effect": "Allow"
           }
       ]
   }
   ```

1. 

**Import the certificate to ACM:**

   ```
   $ aws acm import-certificate --certificate fileb://$CERTIFICATE \
     --private-key fileb://$PRIVATE_KEY \
     --region region-id
   {
     "CertificateArn": "arn:aws:acm:region-id:123456789012:certificate/343db133-490f-4077-b8d4-3da5bfd89e72"
   }
   ```

1. 

**Create and the Load Balancer that will be put in front of the Active Directory endpoints:**

   ```
   $ aws elbv2 create-load-balancer --name CorpPclusterCom-NLB \
     --type network \
     --scheme internal \
     --subnets subnet-1234567890abcdef0 subnet-021345abcdef6789 \
     --region region-id
   {
     "LoadBalancers": [
       {
         "LoadBalancerArn": "arn:aws:elasticloadbalancing:region-id:123456789012:loadbalancer/net/CorpPclusterCom-NLB/3afe296bf4ba80d4",
         "DNSName": "CorpPclusterCom-NLB-3afe296bf4ba80d4.elb.region-id.amazonaws.com",
         "CanonicalHostedZoneId": "Z2IFOLAFXWLO4F",
         "CreatedTime": "2022-05-05T12:56:55.988000+00:00",
         "LoadBalancerName": "CorpPclusterCom-NLB",
         "Scheme": "internal",
         "VpcId": "vpc-021345abcdef6789",
         "State": {
           "Code": "provisioning"
          },
          "Type": "network",
          "AvailabilityZones": [
            {
              "ZoneName": "region-idb",
              "SubnetId": "subnet-021345abcdef6789",
              "LoadBalancerAddresses": []
            },
            {
              "ZoneName": "region-ida",
              "SubnetId": "subnet-1234567890abcdef0",
              "LoadBalancerAddresses": []
            }
          ],
          "IpAddressType": "ipv4"
       }   
     ]
   }
   ```

1. 

**Create the Target Group targeting the Active Directory endpoints:**

   ```
   $ aws elbv2 create-target-group --name CorpPclusterCom-Targets --protocol TCP \
     --port 389 \
     --target-type ip \
     --vpc-id vpc-021345abcdef6789 \
     --region region-id
   {
     "TargetGroups": [
       {
         "TargetGroupArn": "arn:aws:elasticloadbalancing:region-id:123456789012:targetgroup/CorpPclusterCom-Targets/44577c583b695e81",
         "TargetGroupName": "CorpPclusterCom-Targets",
         "Protocol": "TCP",
         "Port": 389,
         "VpcId": "vpc-021345abcdef6789",
         "HealthCheckProtocol": "TCP",
         "HealthCheckPort": "traffic-port",
         "HealthCheckEnabled": true,
         "HealthCheckIntervalSeconds": 30,
         "HealthCheckTimeoutSeconds": 10,
         "HealthyThresholdCount": 3,
         "UnhealthyThresholdCount": 3,
         "TargetType": "ip",
         "IpAddressType": "ipv4"
       }
     ]
   }
   ```

1. 

**Register the Active Directory endpoints into the Target Group:**

   ```
   $ aws elbv2 register-targets --target-group-arn arn:aws:elasticloadbalancing:region-id:123456789012:targetgroup/CorpPclusterCom-Targets/44577c583b695e81 \
     --targets Id=192.0.2.254,Port=389 Id=203.0.113.237,Port=389 \
     --region region-id
   ```

1. 

**Create the LB Listener with the certificate:**

   ```
   $ aws elbv2 create-listener --load-balancer-arn arn:aws:elasticloadbalancing:region-id:123456789012:loadbalancer/net/CorpPclusterCom-NLB/3afe296bf4ba80d4 \
     --protocol TLS \
     --port 636 \
     --default-actions Type=forward,TargetGroupArn=arn:aws:elasticloadbalancing:region-id:123456789012:targetgroup/CorpPclusterCom-Targets/44577c583b695e81 \
     --ssl-policy ELBSecurityPolicy-TLS-1-2-2017-01 \
     --certificates CertificateArn=arn:aws:acm:region-id:123456789012:certificate/343db133-490f-4077-b8d4-3da5bfd89e72 \
     --region region-id
     "Listeners": [
     {
       "ListenerArn": "arn:aws:elasticloadbalancing:region-id:123456789012:listener/net/CorpPclusterCom-NLB/3afe296bf4ba80d4/a8f9d97318743d4b",
       "LoadBalancerArn": "arn:aws:elasticloadbalancing:region-id:123456789012:loadbalancer/net/CorpPclusterCom-NLB/3afe296bf4ba80d4",
       "Port": 636,
       "Protocol": "TLS",
       "Certificates": [
         {
           "CertificateArn": "arn:aws:acm:region-id:123456789012:certificate/343db133-490f-4077-b8d4-3da5bfd89e72"
          }
        ],
        "SslPolicy": "ELBSecurityPolicy-TLS-1-2-2017-01",
        "DefaultActions": [
          {
            "Type": "forward",
            "TargetGroupArn": "arn:aws:elasticloadbalancing:region-id:123456789012:targetgroup/CorpPclusterCom-Targets/44577c583b695e81",
            "ForwardConfig": {
              "TargetGroups": [
                {
                   "TargetGroupArn": "arn:aws:elasticloadbalancing:region-id:123456789012:targetgroup/CorpPclusterCom-Targets/44577c583b695e81"
                 }
               ]
             }
           }
         ]
       }
     ]
   }
   ```

1. 

**Create the Hosted Zone to make the domain discoverable within the cluster VPC:**

   ```
   $ aws route53 create-hosted-zone --name corp.pcluster.com \
     --vpc VPCRegion=region-id,VPCId=vpc-021345abcdef6789 \
     --caller-reference "ParallelCluster AD Tutorial"
   {
     "Location": "https://route53.amazonaws.com/2013-04-01/hostedzone/Z09020002B5MZQNXMSJUB",
     "HostedZone": {
       "Id": "/hostedzone/Z09020002B5MZQNXMSJUB",
       "Name": "corp.pcluster.com.",
       "CallerReference": "ParallelCluster AD Tutorial",
       "Config": {
            "PrivateZone": true
       },
       "ResourceRecordSetCount": 2
     },
     "ChangeInfo": {
       "Id": "/change/C05533343BF3IKSORW1TQ",
       "Status": "PENDING",
       "SubmittedAt": "2022-05-05T13:21:53.863000+00:00"
     },
     "VPC": {
       "VPCRegion": "region-id",
       "VPCId": "vpc-021345abcdef6789"
     }
   }
   ```

1. 

**Create a file named `recordset-change.json` with the following content \(pay attention here that `HostedZoneId` is the Canonical Hosted Zone ID of the Load Balancer\):**

   ```
   {
     "Changes": [
       {
         "Action": "CREATE",
         "ResourceRecordSet": {
           "Name": "corp.pcluster.com",
           "Type": "A",
           "Region": "region-id",
           "SetIdentifier": "pcluster-active-directory",
           "AliasTarget": {
             "HostedZoneId": "Z2IFOLAFXWLO4F",
             "DNSName": "CorpPclusterCom-NLB-3afe296bf4ba80d4.elb.region-id.amazonaws.com",
             "EvaluateTargetHealth": true
           }
         }
       }
     ]
   }
   ```

1. 

**Submit the recordset change to the hosted zone, this time using the hosted zone ID:**

   ```
   $ aws route53 change-resource-record-sets --hosted-zone-id Z09020002B5MZQNXMSJUB \
     --change-batch file://recordset-change.json
   {
     "ChangeInfo": {
       "Id": "/change/C0137926I56R3GC7XW2Y",
       "Status": "PENDING",
       "SubmittedAt": "2022-05-05T13:40:36.553000+00:00"
     }
   }
   ```

1. 

**Create a policy document `policy.json` with the following content:**

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Action": [
           "secretsmanager:GetSecretValue"
         ],
         "Resource": [
           "arn:aws:secretsmanager:region-id:123456789012:secret:pcluster-cert-abc123"
         ],
         "Effect": "Allow"
       }
     ]
   }
   ```

1. 

**Create a policy document named `policy.json` with the following content:**

   ```
   $ aws iam create-policy --policy-name ReadCertPcluster \
     --policy-document file://policy.json
   {
     "Policy": {
       "PolicyName": "ReadCertPcluster",
       "PolicyId": "ANPAUUXUVBC42VZSI4LDY",
       "Arn": "arn:aws:iam::123456789012:policy/ReadCertPcluster-efg456",
       "Path": "/",
       "DefaultVersionId": "v1",
       "AttachmentCount": 0,
       "PermissionsBoundaryUsageCount": 0,
       "IsAttachable": true,
       "CreateDate": "2022-05-05T13:42:18+00:00",
       "UpdateDate": "2022-05-05T13:42:18+00:00"
     }
   }
   ```

1. 

**Continue at [\(Optional\) Step 2: Manage AD users and groups](#tutorials_05_multi-user-ad-step2) or [Step 3: Create the cluster](#tutorials_05_multi-user-ad-step3)\.**

## \(Optional\) Step 2: Manage AD users and groups<a name="tutorials_05_multi-user-ad-step2"></a>

In this step, you manage users and groups from an EC2 Amazon Linux 2 instance that is joined to the AD domain\.

If you followed the *automated* path, restart and log in to the AD joined instance that was created as part of the automation\.

If you followed the *manual* path, restart and log in to the instance that you created and joined to the AD in preceding steps\.

In these steps, you use the [adcli](https://www.mankier.com/package/adcli) and [openldap\-clients](https://www.mankier.com/package/openldap-clients) tools that were installed in the instance as part of a preceding step\.

**Log in to an EC2 instance that is joined to the AD domain**

1. From the EC2 console, select the untitled EC2 instance that was created in previous steps\. The instance state might be **Stopped**\.

1. If the instance state is **Stopped**, choose **Instance state** and then **Start instance**\.

1. After the status checks pass, select the instance and choose **Connect** and ssh in to the instance\.

**Manage users and groups when logged into an EC2 Amazon Linux 2 instance that is joined the AD**

The `adcli` commands with the ` -U "Admin"` option ask you to enter the AD `Admin` password\. You include the AD `Admin` password as part of the `ldapsearch` commands\.

1. 

**Create a user\.**

   ```
   $ adcli create-user "clusteruser" --domain "corp.pcluster.com" -U "Admin"
   ```

1. 

**Set a user password**

   ```
   $ aws --region "region-id" ds reset-user-password --directory-id "d-abcdef01234567890" --user-name "clusteruser" --new-password "new-p@ssw0rd"
   ```

1. 

**Create a group\.**

   ```
   $ adcli create-group "clusterteam" --domain "corp.pcluster.com" -U "Admin"
   ```

1. 

**Add a user to a group\.**

   ```
   $ adcli add-member "clusterteam" "clusteruser" --domain "corp.pcluster.com" -U "Admin"
   ```

1. 

**Describe users and groups**

   Describe all users\.

   ```
   $ ldapsearch "(&(objectClass=user))" -x -h "192.0.2.254" -b "DC=corp,DC=pcluster,DC=com" -D "CN=Admin,OU=Users,OU=CORP,DC=corp,DC=pcluster,DC=com" -w "p@ssw0rd"
   ```

   Describe a specific user\.

   ```
   $ ldapsearch "(&(objectClass=user)(cn=clusteruser))" -x -h "192.0.2.254" -b "DC=corp,DC=pcluster,DC=com" -D "CN=Admin,OU=Users,OU=CORP,DC=corp,DC=pcluster,DC=com" -w "p@ssw0rd"
   ```

   Describe all users with a name pattern\.

   ```
   $ ldapsearch "(&(objectClass=user)(cn=user*))" -x -h "192.0.2.254" -b "DC=corp,DC=pcluster,DC=com" -D "CN=Admin,OU=Users,OU=CORP,DC=corp,DC=pcluster,DC=com" -w "p@ssw0rd"
   ```

   Describe all users that are part of a specific group\.

   ```
   $ ldapsearch "(&(objectClass=user)(memberOf=CN=clusterteam,OU=Users,OU=CORP,DC=corp,DC=pcluster,DC=com))" -x -h "192.0.2.254" -b "DC=corp,DC=pcluster,DC=com" -D "CN=Admin,OU=Users,OU=CORP,DC=corp,DC=pcluster,DC=com" -w "p@ssw0rd"
   ```

   Describe all groups

   ```
   $ ldapsearch "objectClass=group" -x -h "192.0.2.254" -b "DC=corp,DC=pcluster,DC=com" -D "CN=Admin,OU=Users,OU=CORP,DC=corp,DC=pcluster,DC=com" -w "p@ssw0rd"
   ```

   Describe a specific group

   ```
   $ ldapsearch "(&(objectClass=group)(cn=clusterteam))" -x -h "192.0.2.254" -b "DC=corp,DC=pcluster,DC=com" -D "CN=Admin,OU=Users,OU=CORP,DC=corp,DC=pcluster,DC=com" -w "p@ssw0rd"
   ```

1. 

**Remove a user from a group\.**

   ```
   $ adcli remove-member "clusterteam" "clusteruser" --domain "corp.pcluster.com" -U "Admin"
   ```

1. 

**Delete a user**

   ```
   $ adcli delete-user "clusteruser" --domain "corp.pcluster.com" -U "Admin"
   ```

1. 

**Delete a group\.**

   ```
   $ adcli delete-group "clusterteam" --domain "corp.pcluster.com" -U "Admin"
   ```

## Step 3: Create the cluster<a name="tutorials_05_multi-user-ad-step3"></a>

If you haven't exited the EC2 instance, do so now\.

The environment is set up to create a cluster that can authenticate users against the AD\.

Create a simple cluster configuration and provide the settings relevant to connecting to the AD\. For more information, see the [`DirectoryService`](DirectoryService-v3.md) section\.

Choose one of the following cluster configurations and copy it to a file named `ldaps_config.yaml`, `ldaps_nocert_config.yaml`, and `ldap_config.yaml` respectively\.

If you choose the LDAPS configuration with certificate verification \(recommended\) you must also copy bootstrap script to a file named `active-directory.head.post.sh` and locate it in an Amazon S3 bucket as indicated in the configuration file\.

### LDAPS with certificate verification configuration \(recommended\)<a name="tutorials_05_multi-user-ad-step3-ldaps"></a>

**Note**  
`KeyName`: One of your EC2 keypairs\.
`SubnetId / SubnetIds`: One of the subnet IDs provided in the output of the CloudFormation quick create stack \(automated tutorial\) or python script \(manual tutorial\)\.
`Region`: The Region where you created the AD infrastructure\.
`DomainAddr`: This IP address is one of the DNS addresses of your AD service\.
`PasswordSecretArn`: The ARN of the secret that contains the password for the `DomainReadOnlyUser`\.
`BucketName`: The name of the bucket that holds the bootstrap script\.
`AdditionalPolicies` / `Policy`: The ARN of the Read Domain Cerfification policy ReadCertPcluster\.
`CustomActions` / `OnNodeConfigured` / `Args`: The ARN of secret that holds the domain certification policy\.

```
Region: region-id
Image:
  Os: alinux2
HeadNode: 
  InstanceType: t2.micro
  Networking:
    SubnetId: subnet-abcdef01234567890
  Ssh:
    KeyName: keypair
  Iam:
    AdditionalIamPolicies:
      - Policy: arn:aws:iam::123456789012:policy/ReadCertPcluster
    S3Access:
      - BucketName: my-bucket
        EnableWriteAccess: false
        KeyName: bootstrap/active-directory/active-directory.head.post.sh
  CustomActions:
    OnNodeConfigured:
      Script: s3://my-bucket/bootstrap/active-directory/active-directory.head.post.sh
      Args:
        - arn:aws:secretsmanager:region-id:123456789012:secret:pcluster-cert-123abc
        - /opt/parallelcluster/shared/directory_service/domain-certificate.crt
Scheduling:
  Scheduler: slurm
  SlurmQueues:
    - Name: queue0
      ComputeResources:
        - Name: queue0-t2-micro
          InstanceType: t2.micro
          MinCount: 1
          MaxCount: 10         
      Networking:
        SubnetIds:
          - subnet-abcdef01234567890
DirectoryService:
  DomainName: corp.pcluster.com
  DomainAddr: ldaps://corp.pcluster.com
  PasswordSecretArn: arn:aws:secretsmanager:region-id:123456789012:secret:ADSecretPassword-1234
  DomainReadOnlyUser: cn=ReadOnlyUser,ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com
  LdapTlsCaCert: /opt/parallelcluster/shared/directory_service/domain-certificate.crt
  LdapTlsReqCert: hard
```

**Bootstrap script**

After you create the bootstrap file and before you upload it to your S3 bucket, run `chmod +x active-directory.head.post.sh` to give AWS ParallelCluster run permission\.

```
#!/bin/bash
set -e

CERTIFICATE_SECRET_ARN="$1"
CERTIFICATE_PATH="$2"

[[ -z $CERTIFICATE_SECRET_ARN ]] && echo "[ERROR] Missing CERTIFICATE_SECRET_ARN" && exit 1
[[ -z $CERTIFICATE_PATH ]] && echo "[ERROR] Missing CERTIFICATE_PATH" && exit 1

source /etc/parallelcluster/cfnconfig
REGION="${cfn_region:?}"

mkdir -p $(dirname $CERTIFICATE_PATH)
aws secretsmanager get-secret-value --region $REGION --secret-id $CERTIFICATE_SECRET_ARN --query SecretString --output text > $CERTIFICATE_PATH
```

### LDAPS without certificate verification configuration<a name="tutorials_05_multi-user-ad-step3-ldaps-no-cert"></a>

**Note**  
`KeyName`: One of your EC2 keypairs\.
`SubnetId / SubnetIds`: One of the subnet IDs provided in the output of the CloudFormation quick create stack \(automated tutorial\) or python script \(manual tutorial\)\.
`Region`: The Region where you created the AD infrastructure\.
`DomainAddr`: This IP address is one of the DNS addresses of your AD service\.
`PasswordSecretArn`: The ARN of the secret that contains the password for the `DomainReadOnlyUser`\.

```
Region: region-id
Image:
  Os: alinux2
HeadNode: 
  InstanceType: t2.micro
  Networking:
    SubnetId: subnet-abcdef01234567890
  Ssh:
    KeyName: keypair
Scheduling:
  Scheduler: slurm
  SlurmQueues:
    - Name: queue0
      ComputeResources:
        - Name: queue0-t2-micro
          InstanceType: t2.micro
          MinCount: 1
          MaxCount: 10         
      Networking:
        SubnetIds:
          - subnet-abcdef01234567890
DirectoryService:
  DomainName: corp.pcluster.com
  DomainAddr: ldaps://corp.pcluster.com
  PasswordSecretArn: arn:aws:secretsmanager:region-id:123456789012:secret:ADSecretPassword-1234
  DomainReadOnlyUser: cn=ReadOnlyUser,ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com
  LdapTlsReqCert: never
```

### LDAP configuration<a name="tutorials_05_multi-user-ad-step3-ldap.title"></a>

**Note**  
`KeyName`: One of your EC2 keypairs\.
`SubnetId / SubnetIds`: One of the subnet IDs provided in the output of the CloudFormation quick create stack \(automated tutorial\) or python script \(manual tutorial\)\.
`Region`: The Region where you created the AD infrastructure\.
`DomainAddr`: This IP address is one of the DNS addresses of your AD service\.
`PasswordSecretArn`: The ARN of the secret that contains the password for the `DomainReadOnlyUser`\.

```
Region: region-id
Image:
  Os: alinux2
HeadNode: 
  InstanceType: t2.micro
  Networking:
    SubnetId: subnet-abcdef01234567890
  Ssh:
    KeyName: keypair
Scheduling:
  Scheduler: slurm
  SlurmQueues:
    - Name: queue0
      ComputeResources:
        - Name: queue0-t2-micro
          InstanceType: t2.micro
          MinCount: 1
          MaxCount: 10         
      Networking:
        SubnetIds:
          - subnet-abcdef01234567890
DirectoryService:
  DomainName: dc=corp,dc=pcluster,dc=com
  DomainAddr: ldap://192.0.2.254,ldap://203.0.113.237
  PasswordSecretArn: arn:aws:secretsmanager:region-id:123456789012:secret:ADSecretPassword-1234
  DomainReadOnlyUser: cn=ReadOnlyUser,ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com
  AdditionalSssdConfigs:
    ldap_auth_disable_tls_never_use_in_production: True
```

Create your cluster with the following command:

```
$ pcluster create-cluster --cluster-name "ad-cluster" --cluster-configuration "./ldaps_config.yaml"
{
  "cluster": {
    "clusterName": "pcluster",
    "cloudformationStackStatus": "CREATE_IN_PROGRESS",
    "cloudformationStackArn": "arn:aws:cloudformation:region-id:123456789012:stack/ad-cluster/1234567-abcd-0123-def0-abcdef0123456",
    "region": "region-id",
    "version": 3.3.0,
    "clusterStatus": "CREATE_IN_PROGRESS"
  }
}
```

## Step 4: Connect to the cluster as a user<a name="tutorials_05_multi-user-ad-step4"></a>

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

## Step 5: Clean up<a name="tutorials_05_multi-user-ad-step5"></a>

1. 

**From your local machine, delete the cluster\.**

   ```
   $ pcluster delete-cluster --cluster-name "ad-cluster" --region "region-id"
   {
     "cluster": {
       "clusterName": "ad-cluster",
       "cloudformationStackStatus": "DELETE_IN_PROGRESS",
       "cloudformationStackArn": "arn:aws:cloudformation:region-id:123456789012:stack/ad-cluster/1234567-abcd-0123-def0-abcdef0123456",
       "region": "region-id",
       "version": "3.3.0",
       "clusterStatus": "DELETE_IN_PROGRESS"
     }
   }
   ```

1. 

**Check the status of the cluster delete\.**

   ```
   $ pcluster describe-cluster --cluster-name "ad-cluster" --region "region-id" --query "clusterStatus"
   "DELETE_IN_PROGRESS"
   ```

   After the cluster delete is complete, proceed to the next step\.

### Automated<a name="tutorials_05_multi-user-ad-step5-automated"></a>

**Delete the Active Directory resources**

1. From [https://console\.aws\.amazon\.com/cloudformation/](https://console.aws.amazon.com/cloudformation/)

1. Choose **Stacks** in the navigation pane\.

1. From the list of stacks, choose the AD stack, for example `pcluster-ad`\.

1. Choose **Delete**\.

### Manual<a name="tutorials_05_multi-user-ad-step5-manual"></a>

1. 

**Delete the EC2 instance\.**

   1. From [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/), choose **Instances** in the navigation pane\.

   1. From the list of instances, choose the instance that you created to add users to the directory\.

   1. **New EC2 console**:

      Choose **Instance state**, then **Terminate instance**\.

      **Old EC2 console**:

      Choose **Actions**, **Instance State**, then **Terminate**\.

1. 

**Delete the Hosted Zone\.**

   1. Create a `recordset-delete.json` with the following content \(pay attention here that HostedZoneId is the Canonical Hosted Zone ID of the Load Balancer\):

      ```
      {
        "Changes": [
          {
            "Action": "DELETE",
            "ResourceRecordSet": {
              "Name": "corp.pcluster.com",
              "Type": "A",
              "Region": "region-id",
              "SetIdentifier": "pcluster-active-directory",
              "AliasTarget": {
                "HostedZoneId": "Z2IFOLAFXWLO4F",
                "DNSName": "CorpPclusterCom-NLB-3afe296bf4ba80d4.elb.region-id.amazonaws.com",
                "EvaluateTargetHealth": true
              }
            }
          }
        ]
      }
      ```

   1. Submit the recordset change to the hosted zone, this time using the hosted zone ID:

      ```
      $ aws route53 change-resource-record-sets --hosted-zone-id Z09020002B5MZQNXMSJUB \
        --change-batch file://recordset-delete.json
      {
       "ChangeInfo": {
           "Id": "/change/C04853642A0TH2TJ5NLNI",
           "Status": "PENDING",
           "SubmittedAt": "2022-05-05T14:25:51.046000+00:00"
       }
      }
      ```

   1. Delete the Hosted Zone:

      ```
      $ aws route53 delete-hosted-zone --id Z09020002B5MZQNXMSJUB
      {
       "ChangeInfo": {
           "Id": "/change/C0468051QFABTVHMDEG9",
           "Status": "PENDING",
           "SubmittedAt": "2022-05-05T14:26:13.814000+00:00"
       }
      }
      ```

1. 

**Delete the LB Listener:**

   ```
   $ aws elbv2 delete-listener \
     --listener-arn arn:aws:elasticloadbalancing:region-id:123456789012:listener/net/CorpPclusterCom-NLB/3afe296bf4ba80d4/a8f9d97318743d4b --region region-id
   ```

1. 

**Delete Target Group:**

   ```
   $ aws elbv2 delete-target-group \
     --target-group-arn arn:aws:elasticloadbalancing:region-id:123456789012:targetgroup/CorpPclusterCom-Targets/44577c583b695e81 --region region-id
   ```

1. 

**Delete the Load Balancer:**

   ```
   $ aws elbv2 delete-load-balancer \
     --load-balancer-arn arn:aws:elasticloadbalancing:region-id:123456789012:loadbalancer/net/CorpPclusterCom-NLB/3afe296bf4ba80d4 --region region-id
   ```

1. 

**Delete the policy used by the cluster to read the certificate from Secrets Manager:**

   ```
   $ aws iam delete-policy --policy-arn arn:aws:iam::123456789012:policy/ReadCertPcluster
   ```

1. 

**Delete the secret containing the domain certificate:**

   ```
   $ aws secretsmanager delete-secret \
     --secret-id arn:aws:secretsmanager:region-id:123456789012:secret:pcluster-cert-123abc \
     --region region-id
   {
    "ARN": "arn:aws:secretsmanager:region-id:123456789012:secret:pcluster-cert-123abc",
    "Name": "pcluster-cert",
    "DeletionDate": "2022-06-04T16:27:36.183000+02:00"
   }
   ```

1. 

**Delete the certificate from ACM:**

   ```
   $ aws acm delete-certificate \
     --certificate-arn arn:aws:acm:region-id:123456789012:certificate/343db133-490f-4077-b8d4-3da5bfd89e72 --region region-id
   ```

1. 

**Delete the Active Directory resources\.**

   1. Get the following resource IDs from the output of the python script `ad.py`:
      + AD ID
      + AD subnet IDs
      + AD VPC ID

   1. Delete the directory:

      ```
      $ aws ds delete-directory --directory-id d-abcdef0123456789 --region region-id
      {
         "DirectoryId": "d-abcdef0123456789"
      }
      ```

   1. List the Security Groups in the VPC:

      ```
      $ aws ec2 describe-security-groups --filters '[{"Name":"vpc-id","Values":["vpc-07614ade95ebad1bc"]}]' --region region-id
      ```

   1. Delete the non default Security Group:

      ```
      $ aws ec2 delete-security-group --group-id sg-021345abcdef6789 --region region-id
      ```

   1. Delete the subnets:

      ```
      $ aws ec2 delete-subnet --subnet-id subnet-1234567890abcdef --region region-id
      ```

      ```
      $ aws ec2 delete-subnet --subnet-id subnet-021345abcdef6789 --region region-id
      ```

   1. Describe Internet Gateway:

      ```
      $ aws ec2 describe-internet-gateways \
        --filters Name=attachment.vpc-id,Values=vpc-021345abcdef6789 \
        --region region-id
      {
        "InternetGateways": [
          {
            "Attachments": [
              {
                "State": "available",
                "VpcId": "vpc-021345abcdef6789"
              }
            ],
            "InternetGatewayId": "igw-1234567890abcdef",
            "OwnerId": "123456789012",
            "Tags": []
          }
        ]  
      }
      ```

   1. Detach Internet Gateway:

      ```
      $ aws ec2 detach-internet-gateway \
        --internet-gateway-id igw-1234567890abcdef \
        --vpc-id vpc-021345abcdef6789 \
        --region region-id
      ```

   1. Delete Internet Gateway:

      ```
      $ aws ec2 delete-internet-gateway \
        --internet-gateway-id igw-1234567890abcdef \
        --region region-id
      ```

   1. Delete VPC:

      ```
      $ aws ec2 delete-vpc \
        --vpc-id vpc-021345abcdef6789 \
        --region region-id
      ```

   1. Delete the secret containing the ReadOnlyUser password:

      ```
      $ aws secretsmanager delete-secret \
        --secret-id arn:aws:secretsmanager:region-id:123456789012:secret:ADSecretPassword-1234" \
        --region region-id
      ```