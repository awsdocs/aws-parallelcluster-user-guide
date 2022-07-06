# Create an EC2 instance<a name="tutorials_05_multi-user-ad-step1-manual-instance"></a>

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the **EC2 Dashboard**, choose **Launch Instance**\.

1. Select a recent Amazon Linux 2 AMI, t2\.micro instance type\.

**In **Configure Instance Details**, choose the following values:**
   + **Network**: **PclusterVPC**
   + **Auto\-assign Public IP**: **Enable**
   + **Domain join directory**: **corp\.pcluster\.com**
   + **IAM Role**: Create a role and select it with the following policies attached\. Create the `ResetUserPassword` policy, and use the directory ID from the output of the script you ran to create the AD\.
     + AWS managed policy [AmazonSSMManagedInstanceCore](https://docs.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore)
     + AWS managed policy [AmazonSSMDirectoryServiceAccess](https://docs.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/AmazonSSMDirectoryServiceAccess)
     + ResetUserPassword

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

1. Choose **Review and Launch** to launch the instance\.

1. Make note of the Instance ID \(for example, i\-1234567890abcdef0\) and wait for the instance to finish launching\.

1. After the instance has launched, continue to the next step\.