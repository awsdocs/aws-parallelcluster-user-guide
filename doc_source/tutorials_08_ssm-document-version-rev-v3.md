# Reverting to a previous AWS Systems Manager document version<a name="tutorials_08_ssm-document-version-rev-v3"></a>

Learn how to revert to a previous AWS Systems Manager document version\. For more information about SSM documents, see [AWS Systems Manager Documents](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-ssm-docs.html) in the *AWS Systems Manager User Guide*\.

When using the AWS ParallelCluster command line interface \(CLI\) or API, you only pay for the AWS resources that are created when you create or update AWS ParallelCluster images and clusters\. For more information, see [AWS services used by AWS ParallelCluster](aws-services-v3.md)\.

The AWS ParallelCluster UI is built on a serverless architecture and you can use it within the AWS Free Tier category for most cases\. For more information, see [AWS ParallelCluster UI costs](install-pcui-v3.md#install-pcui-costs-v3)\.

**Prerequisites:**
+ An AWS account with permissions to manage SSM documents\.
+ The AWS CLI [is installed and configured\.](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

## Revert to a previous SSM document version<a name="tutorials_08_ssm-document-version-rev-steps"></a>

1. In your terminal, run the following command to get the list of existing SSM documents that you own\.

   ```
   $ aws ssm list-documents --document-filter "key=Owner,value=Self"
   ```

1. Revert an SSM document to a previous version\. In this example, we revert to a previous version of the `SessionManagerRunShell` document\. You can use the SSM `SessionManagerRunShell` document to customize every SSM shell session that you initiate\.

   1. Find the `DocumentVersion` parameter for `SessionManagerRunShell` by running the following command:

      ```
      $ aws ssm describe-document --name "SSM-SessionManagerRunShell"
      {
          "Document": {
              "Hash": "...",
              "HashType": "Sha256",
              "Name": "SSM-SessionManagerRunShell",
              "Owner": "123456789012",
              "CreatedDate": "2023-02-20T19:04:32.390000+00:00",
              "Status": "Active",
              "DocumentVersion": "1",
              "Parameters": [
                  {
                      "Name": "linuxcmd",
                      "Type": "String",
                      "Description": "The command to run on connection...",
                      "DefaultValue": "if [ -d '/opt/parallelcluster' ]; then source /opt/parallelcluster/cfnconfig; sudo su - $cfn_cluster_user; fi; /bin/bash"
                  }
              ],
              "PlatformTypes": [
                  "Windows",
                  "Linux",
                  "MacOS"
              ],
              "DocumentType": "Session",
              "SchemaVersion": "1.0",
              "LatestVersion": "2",
              "DefaultVersion": "1",
              "DocumentFormat": "JSON",
              "Tags": []
          }
      }
      ```

      The latest version is `2`\.

   1. Revert to the previous version by running the following command:

      ```
      $ aws ssm delete-document --name "SSM-SessionManagerRunShell" --document-version 2
      ```

1. Verify that the document version has been reverted by running the `describe-document` command again:

   ```
   $ aws ssm describe-document --name "SSM-SessionManagerRunShell"
   {
       "Document": {
           "Hash": "...",
           "HashType": "Sha256",
           "Name": "SSM-SessionManagerRunShell",
           "Owner": "123456789012",
           "CreatedDate": "2023-02-20T19:04:32.390000+00:00",
           "Status": "Active",
           "DocumentVersion": "1",
           "Parameters": [
               {
                   "Name": "linuxcmd",
                   "Type": "String",
                   "Description": "The command to run on connection...",
                   "DefaultValue": "if [ -d '/opt/parallelcluster' ]; then source /opt/parallelcluster/cfnconfig; sudo su - $cfn_cluster_user; fi; /bin/bash"
               }
           ],
           "PlatformTypes": [
               "Windows",
               "Linux",
               "MacOS"
           ],
           "DocumentType": "Session",
           "SchemaVersion": "1.0",
           "LatestVersion": "1",
           "DefaultVersion": "1",
           "DocumentFormat": "JSON",
           "Tags": []
       }
   }
   ```

   The latest version is `1`\.