# Step 3: Prepare and test an example client to invoke the API<a name="tutorials_06_multi-API-use-step3"></a>



Clone the AWS ParallelCluster source code, `cd` to the `api` directory, and install the Python client libraries\.

1. 

   ```
   $ git clone -b v${PCLUSTER_VERSION} https://github.com/aws/aws-parallelcluster aws-parallelcluster-v${PCLUSTER_VERSION}
    cd aws-parallelcluster-v${PCLUSTER_VERSION}/api
   ```

   ```
   $ pip3 install client/src
   ```

1. Navigate back to your home user directory\.

1. Export the API Gateway base URL that the client uses when running\.

   ```
   $ export PCLUSTER_API_URL=$( aws cloudformation describe-stacks --stack-name ${API_STACK_NAME} --query 'Stacks[0].Outputs[?OutputKey==`ParallelClusterApiInvokeUrl`].OutputValue' --output text )
    echo "export PCLUSTER_API_URL=${PCLUSTER_API_URL}" |tee -a ~/.bashrc
   ```

1. Export a cluster name to that the client uses to create a cluster\.

   ```
   $ export CLUSTER_NAME="test-api-cluster"
    echo "export CLUSTER_NAME=${CLUSTER_NAME}" |tee -a ~/.bashrc
   ```

1. Run the following commands to store the credentials that the example client uses to access the API\.

   ```
   $ export PCLUSTER_API_USER_ROLE=$( aws cloudformation describe-stacks --stack-name ${API_STACK_NAME} --query 'Stacks[0].Outputs[?OutputKey==`ParallelClusterApiUserRole`].OutputValue' --output text )
    echo "export PCLUSTER_API_USER_ROLE=${PCLUSTER_API_USER_ROLE}" |tee -a ~/.bashrc
   ```