# Step 5: Copy client code script and delete cluster<a name="tutorials_06_multi-API-use-step5"></a>

1. Copy the following example client code to `delete_cluster_client.py`\. The client code makes a request request to delete the cluster\.

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
       
       # Delete the cluster
       gone = False
       try:
           api_response = cluster_ops.delete_cluster(cluster_name, region=region)
       except pcluster_client.ApiException as e:
           print("Exception when calling delete_cluster: %s\n" % e)
       time.sleep(60)
       
       # Confirm cluster status with describe_cluster
       while not gone:
           try:
               api_response = cluster_ops.describe_cluster(cluster_name, region=region)
               pprint(api_response)
               if api_response.cluster_status == ClusterStatus('DELETE_IN_PROGRESS'):
                   print('. . . working . . .', end='', flush=True)
                   time.sleep(60)    
           except pcluster_client.ApiException as e:
               gone = True
               print("DELETE COMPLETE or Exception when calling describe_cluster: %s\n" % e)
   ```

1. Run the following command to delete the cluster\.

   ```
   $ python3 delete_cluster_client.py
   {'cloud_formation_stack_status': 'DELETE_IN_PROGRESS',
   'cloudformation_stack_arn': 'arn:aws:cloudformation:us-east-1:123456789012:stack/test-api-cluster/abcd1234-ef56-gh78-ij90-1234abcd5678',
   'cluster_configuration': {'url': 'https://parallelcluster-021345abcdef6789-v1-do-not-delete...},
   'cluster_name': 'test-api-cluster',
   'cluster_status': 'DELETE_IN_PROGRESS',
   'compute_fleet_status': 'UNKNOWN',
   'creation_time': datetime.datetime(2022, 4, 28, 16, 50, 47, 943000, tzinfo=tzlocal()),
   'head_node': {'instance_id': 'i-abcdef01234567890',
                 'instance_type': 't2.micro',
                 'launch_time': datetime.datetime(2022, 4, 28, 16, 53, 48, tzinfo=tzlocal()),
                 'private_ip_address': '172.31.17.132',
                 'public_ip_address': '34.201.100.37',
                 'state': 'running'},
   'last_updated_time': datetime.datetime(2022, 4, 28, 16, 50, 47, 943000, tzinfo=tzlocal()),
   'region': 'us-east-1',
   'tags': [{'key': 'parallelcluster:version', 'value': '3.1.3'}],
   'version': '3.1.3'}
          .
          . 
          .
   . . . working . . . {'cloud_formation_stack_status': 'DELETE_IN_PROGRESS',
   'cloudformation_stack_arn': 'arn:aws:cloudformation:us-east-1:123456789012:stack/test-api-cluster/abcd1234-ef56-gh78-ij90-1234abcd5678',
   'cluster_configuration': {'url': 'https://parallelcluster-021345abcdef6789-v1-do-not-delete...},
   'cluster_name': 'test-api-cluster',
   'cluster_status': 'DELETE_IN_PROGRESS',
   'compute_fleet_status': 'UNKNOWN',
   'creation_time': datetime.datetime(2022, 4, 28, 16, 50, 47, 943000, tzinfo=tzlocal()),
   'last_updated_time': datetime.datetime(2022, 4, 28, 16, 50, 47, 943000, tzinfo=tzlocal()),
   'region': 'us-east-1',
   'tags': [{'key': 'parallelcluster:version', 'value': '3.1.3'}],
   'version': '3.1.3'}
   . . . working . . . DELETE COMPLETE or Exception when calling describe_cluster: (404)
   Reason: Not Found
    	      .
    	      .
    	      .
   HTTP response body: {"message":"Cluster 'test-api-cluster' does not exist or belongs to an incompatible ParallelCluster major version."}
   ```

1. After you are finished testing, unset the environment variables\.

   ```
   $ unset AWS_SESSION_TOKEN
   unset AWS_SECRET_ACCESS_KEY
   unset AWS_ACCESS_KEY_ID
   ```