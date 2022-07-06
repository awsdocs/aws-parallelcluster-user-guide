# Step 4: Copy client code script and run cluster tests<a name="tutorials_06_multi-API-use-step4"></a>

1. Copy the following example client code to `test_pcluster_client.py` in your home user directory\. The client code makes requests to:
   + Create the cluster\.
   + Describe the cluster\.
   + List the clusters\.
   + Describe the compute fleet\.
   + Describe the cluster instances\.

   ```
   # Copyright 2021 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   # SPDX-License-Identifier: MIT-0
   #
   # Permission is hereby granted, free of charge, to any person obtaining a copy of this
   # software and associated documentation files (the "Software"), to deal in the Software
   # without restriction, including without limitation the rights to use, copy, modify,
   # merge, publish, distribute, sublicense, and/or sell copies of the Software, and to
   # permit persons to whom the Software is furnished to do so.
   #
   # THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED,
   # INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A
   # PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
   # HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
   # OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
   # SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
   #
   #  Author: Evan F. Bollig (Github: bollig)
   
   import time, datetime
   import os
   import pcluster_client
   from pprint import pprint
   from pcluster_client.api import (
       cluster_compute_fleet_api,
       cluster_instances_api,
       cluster_operations_api
   )
   from pcluster_client.model.create_cluster_request_content import CreateClusterRequestContent
   from pcluster_client.model.cluster_status import ClusterStatus
   region=os.environ.get("AWS_DEFAULT_REGION")
   
   # Defining the host is optional and defaults to http://localhost
   # See configuration.py for a list of all supported configuration parameters.
   configuration = pcluster_client.Configuration(
       host = os.environ.get("PCLUSTER_API_URL")
   )
   cluster_name=os.environ.get("CLUSTER_NAME")
   
   # Enter a context with an instance of the API client
   with pcluster_client.ApiClient(configuration) as api_client:
       cluster_ops = cluster_operations_api.ClusterOperationsApi(api_client)
       fleet_ops = cluster_compute_fleet_api.ClusterComputeFleetApi(api_client)
       instance_ops = cluster_instances_api.ClusterInstancesApi(api_client)
       
       # Create cluster
       build_done = False
       try:
           with open('cluster-config.yaml', encoding="utf-8") as f:
               body = CreateClusterRequestContent(cluster_name=cluster_name, cluster_configuration=f.read())
               api_response = cluster_ops.create_cluster(body, region=region)
       except pcluster_client.ApiException as e:
           print("Exception when calling create_cluster: %s\n" % e)
           build_done = True
       time.sleep(60)
       
       # Confirm cluster status with describe_cluster
       while not build_done:
           try:
               api_response = cluster_ops.describe_cluster(cluster_name, region=region)
               pprint(api_response)
               if api_response.cluster_status == ClusterStatus('CREATE_IN_PROGRESS'):
                   print('. . . working . . .', end='', flush=True)
                   time.sleep(60)
               elif api_response.cluster_status == ClusterStatus('CREATE_COMPLETE'):
                   print('READY!')
                   build_done = True
               else:
                   print('ERROR!!!!')
                   build_done = True    
           except pcluster_client.ApiException as e:
               print("Exception when calling describe_cluster: %s\n" % e)  
    
       # List clusters
       try:
           api_response = cluster_ops.list_clusters(region=region)
           pprint(api_response)
       except pcluster_client.ApiException as e:
           print("Exception when calling list_clusters: %s\n" % e)
                   
       # DescribeComputeFleet
       try:
           api_response = fleet_ops.describe_compute_fleet(cluster_name, region=region)
           pprint(api_response)
       except pcluster_client.ApiException as e:
           print("Exception when calling compute fleet: %s\n" % e)
   
       # DescribeClusterInstances
       try:
           api_response = instance_ops.describe_cluster_instances(cluster_name, region=region)
           pprint(api_response)
       except pcluster_client.ApiException as e:
           print("Exception when calling describe_cluster_instances: %s\n" % e)
   ```

1. Create a cluster configuration\.

   ```
   $ pcluster configure --config cluster-config.yaml
   ```

1. The API Client library automatically detects configuration details from your environment variables \(i\.e\., AWS\_ACCESS\_KEY\_ID, AWS\_SECRET\_ACCESS\_KEY, AWS\_SESSION\_TOKEN\) or $HOME/\.aws\. The following command will switch your current IAM role to the designated ParallelClusterApiUserRole:

   ```
   $  eval $(aws sts assume-role --role-arn ${PCLUSTER_API_USER_ROLE} --role-session-name ApiTestSession | jq -r '.Credentials | "export AWS_ACCESS_KEY_ID=\(.AccessKeyId)\nexport AWS_SECRET_ACCESS_KEY=\(.SecretAccessKey)\nexport AWS_SESSION_TOKEN=\(.SessionToken)\n"')
   ```

   **Error to watch for:**

   If you see an error similar to the following, you have already assumed the ParallelClusterApiUserRole and your `AWS_SESSION_TOKEN` has expired\.

   ```
   An error occurred (AccessDenied) when calling the AssumeRole operation: 
   User: arn:aws:sts::XXXXXXXXXXXX:assumed-role/ParallelClusterApiUserRole-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/ApiTestSession 
   is not authorized to perform: sts:AssumeRole on resource: arn:aws:iam::XXXXXXXXXXXX:role/ParallelClusterApiUserRole-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

   Drop the role and then re\-run the `aws sts assume-role` command to use the ParallelClusterApiUserRole\.

   ```
   $ unset AWS_SESSION_TOKEN
   unset AWS_SECRET_ACCESS_KEY
   unset AWS_ACCESS_KEY_ID
   ```

   To provide your current user permissions for API access, you must [expand the Resource Policy](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-resource-policies.html)\.

1. Run the following command to test the example client\.

   ```
   $ python3 test_pcluster_client.py
   {'cluster_configuration': 'Region: us-east-1\n'
                             'Image:\n'
                             '  Os: alinux2\n'
                             'HeadNode:\n'
                             '  InstanceType: t2.micro\n'
                             '  Networking . . . :\n'
                             '    SubnetId: subnet-1234567890abcdef0\n'
                             '  Ssh:\n'
                             '    KeyName: adpc\n'
                             'Scheduling:\n'
                             '  Scheduler: slurm\n'
                             '  SlurmQueues:\n'
                             '  - Name: queue1\n'
                             '    ComputeResources:\n'
                             '    - Name: t2micro\n'
                             '      InstanceType: t2.micro\n'
                             '      MinCount: 0\n'
                             '      MaxCount: 10\n'
                             '    Networking . . . :\n'
                             '      SubnetIds:\n'
                             '      - subnet-1234567890abcdef0\n',
    'cluster_name': 'test-api-cluster'}
   {'cloud_formation_stack_status': 'CREATE_IN_PROGRESS',
    'cloudformation_stack_arn': 'arn:aws:cloudformation:us-east-1:123456789012:stack/test-api-cluster/abcd1234-ef56-gh78-ij90-1234abcd5678',
    'cluster_configuration': {'url': 'https://parallelcluster-021345abcdef6789-v1-do-not-delete...},
    'cluster_name': 'test-api-cluster',
    'cluster_status': 'CREATE_IN_PROGRESS',
    'compute_fleet_status': 'UNKNOWN',
    'creation_time': datetime.datetime(2022, 4, 28, 16, 18, 47, 972000, tzinfo=tzlocal()),
    'last_updated_time': datetime.datetime(2022, 4, 28, 16, 18, 47, 972000, tzinfo=tzlocal()),
    'region': 'us-east-1',
    'tags': [{'key': 'parallelcluster:version', 'value': '3.1.3'}],
    'version': '3.1.3'}
           .
           . 
           .
   . . . working . . . {'cloud_formation_stack_status': 'CREATE_COMPLETE',
    'cloudformation_stack_arn': 'arn:aws:cloudformation:us-east-1:123456789012:stack/test-api-cluster/abcd1234-ef56-gh78-ij90-1234abcd5678',
    'cluster_configuration': {'url': 'https://parallelcluster-021345abcdef6789-v1-do-not-delete...},
    'cluster_name': 'test-api-cluster',
    'cluster_status': 'CREATE_COMPLETE',
    'compute_fleet_status': 'RUNNING',
    'creation_time': datetime.datetime(2022, 4, 28, 16, 18, 47, 972000, tzinfo=tzlocal()),
    'head_node': {'instance_id': 'i-abcdef01234567890',
                  'instance_type': 't2.micro',
                  'launch_time': datetime.datetime(2022, 4, 28, 16, 21, 46, tzinfo=tzlocal()),
                  'private_ip_address': '172.31.27.153',
                  'public_ip_address': '52.90.156.51',
                  'state': 'running'},
    'last_updated_time': datetime.datetime(2022, 4, 28, 16, 18, 47, 972000, tzinfo=tzlocal()),
    'region': 'us-east-1',
    'tags': [{'key': 'parallelcluster:version', 'value': '3.1.3'}],
    'version': '3.1.3'}
   READY!
   ```