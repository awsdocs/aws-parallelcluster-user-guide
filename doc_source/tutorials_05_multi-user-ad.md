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
       "version": "3.2.0",
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

   1. Choose **Instance state**, then **Terminate instance**\.

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