# Step 6: Clean up<a name="tutorials_06_multi-API-use-step6"></a>

You can use the AWS Management Console or AWS CLI to delete your API\.

1. From the AWS CloudFormation console, choose the API stack and then choose **Delete**\.

1. Run the following command if using the AWS CLI\.

   Using AWS CloudFormation:

   ```
   $ aws cloudformation delete-stack --stack-name ${API_STACK_NAME}
   ```