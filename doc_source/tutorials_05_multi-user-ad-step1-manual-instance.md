# Create an EC2 instance<a name="tutorials_05_multi-user-ad-step1-manual-instance"></a>

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

1. In **Network settings**, choose **Edit**\.

1. In **Network settings**, **VPC**, select the directory VPC\.

1. Scroll down and select **Advanced details**\.

1. In **Advanced details**, **Domain join directory**, choose **corp\.pcluster\.com**\.

1. In **Advanced details**, **Instance profile**, choose the role you created in step 1 or a role with policies listed in step 4 attached,

1. In **Summary** choose **Launch instance**\.

1. Make note of the Instance ID \(for example, i\-1234567890abcdef0\) and wait for the instance to finish launching\.

1. After the instance has launched, continue to the next step\.