# LDAPS with certificate verification \(recommended\) setup<a name="tutorials_05_multi-user-ad-step1-manual-ldaps"></a>

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

**Continue at [\(Optional\) Step 2: Manage AD users and groups](tutorials_05_multi-user-ad-step2.md) or [Step 3: Create the cluster](tutorials_05_multi-user-ad-step3.md)\.**