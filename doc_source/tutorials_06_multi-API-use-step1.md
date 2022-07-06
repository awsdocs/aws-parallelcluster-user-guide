# Step 1: Build the API with Amazon API Gateway<a name="tutorials_06_multi-API-use-step1"></a>

**Stay in your home user directory and activate your virtual environment:**

1. Install a helpful JSON command line processor\.

   ```
   $ sudo yum groupinstall -y "Development Tools"
    sudo yum install -y jq python3-devel
   ```

1. Run the following command to get your AWS ParallelCluster version and assign it to an environment variable\.

   ```
   $ PCLUSTER_VERSION=$(pcluster version | jq -r '.version')
    echo "export PCLUSTER_VERSION=${PCLUSTER_VERSION}" |tee -a ~/.bashrc
   ```

1. Create an environment variable and assign your Region ID to it\.

   ```
   $ export AWS_DEFAULT_REGION="us-east-1"
    echo "export AWS_DEFAULT_REGION=${AWS_DEFAULT_REGION}" |tee -a ~/.bashrc
   ```

1. Run the following commands to deploy the API

   ```
   API_STACK_NAME="pc-api-stack"
    echo "export API_STACK_NAME=${API_STACK_NAME}" |tee -a ~/.bashrc
   ```

   ```
   aws cloudformation create-stack \
      --region ${AWS_DEFAULT_REGION} \
      --stack-name ${API_STACK_NAME} \
      --template-url https://${AWS_DEFAULT_REGION}-aws-parallelcluster.s3.${AWS_DEFAULT_REGION}.amazonaws.com/parallelcluster/${PCLUSTER_VERSION}/api/parallelcluster-api.yaml \
      --capabilities CAPABILITY_NAMED_IAM CAPABILITY_AUTO_EXPAND \
      --parameters ParameterKey=EnableIamAdminAccess,ParameterValue=true
        
       {
          "StackId": "arn:aws:cloudformation:us-east-1:123456789012:stack/my-api-stack/abcd1234-ef56-gh78-ei90-1234abcd5678"
       }
   ```

   After the process completes, proceed to the next step\.