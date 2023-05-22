# Multiple user access to clusters<a name="multi-user-v3"></a>

Learn to implement and manage multiple user access to a single cluster\.

In this topic, an AWS ParallelCluster user refers to a system user for compute instances\. An example is an `ec2-user` for an AWS EC2 instance\.

AWS ParallelCluster multi\-user access support is available in all the AWS Regions where AWS ParallelCluster is currently available\. It works with other AWS services, including [Amazon FSx for Lustre](https://docs.aws.amazon.com/fsx/latest/LustreGuide/what-is.html) and [Amazon Elastic File System](https://docs.aws.amazon.com/efs/latest/ug/whatisefs.html)\.

You can use an [AWS Directory Service for Microsoft Active Directory](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_microsoft_ad.html) or [Simple AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_simple_ad.html) to manage cluster access\. Make sure to check [AWS Region availability](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/regions.html) for these services\. To set up a cluster, specify an [AWS ParallelCluster DirectoryService](DirectoryService-v3.md) configuration\. AWS Directory Service directories can be connected to multiple clusters\. This allows for centralized management of identities across multiple environments and a unified login experience\.

When you use AWS Directory Service for AWS ParallelCluster multiple user access, you can log in to the cluster with user credentials that are defined in the directory\. These credentials consist of a user name and password\. After you log in to the cluster for the first time, a user SSH key is automatically generated\. You can use it to log in without a password\.

You can create, delete, and modify a cluster’s users or groups after your directory service is deployed\. With AWS Directory Service, you can do this in the AWS Management Console or by using the *Active Directory Users and Computers* tool\. This tool is accessible from any EC2 instance that's joined to your Active Directory\. For more information, see [Installing the Active Directory administration tools](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_install_ad_tools.html)\.

If you plan to use AWS ParallelCluster in a single subnet with no internet access, see [AWS ParallelCluster in a single subnet with no internet access](network-configuration-v3.md#aws-parallelcluster-in-a-single-public-subnet-no-internet-v3) for additional requirements\.

**Topics**
+ [Create an Active Directory](#create-addir-v3)
+ [Create a cluster with an AD domain](#create-addircluster-v3)
+ [Log in to a cluster integrated with an AD domain](#login-addircluster-v3)
+ [Running MPI jobs](#addircluster-MPI-v3)
+ [Example AWS Managed Microsoft AD over LDAP\(S\) cluster configurations](#examples-addir-v3)

## Create an Active Directory<a name="create-addir-v3"></a>

Make sure that you create an Active Directory \(AD\) before you create your cluster\. For information about how to choose the type of active directory for your cluster, see [Which to choose](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/what_is.html#choosing_an_option) in the *AWS Directory Service Administration Guide*\.

If the directory is empty, add users with user names and passwords\. For more information, see the documentation that's specific to [AWS Directory Service for Microsoft Active Directory](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_manage_users_groups.html) or [Simple AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/simple_ad_manage_users_groups.html)\.

**Note**  
AWS ParallelCluster requires every Active Directory user directory to be in the `/home/$user` directory\.

## Create a cluster with an AD domain<a name="create-addircluster-v3"></a>

**Warning**  
This introductory section describes how to set up AWS ParallelCluster with a Managed Active Delivery \(AD\) server over the Lightweight Directory Access Protocol \(LDAP\)\. LDAP is an insecure protocol\. For production systems, we strongly recommended the use of TLS certificates \(LDAPS\) as described in the [Example AWS Managed Microsoft AD over LDAP\(S\) cluster configurations](#examples-addir-v3) section that follows\.

Configure your cluster to integrate with a directory by specifying the relevant information in the `DirectoryService` section of the cluster configuration file\. For more information, see the [`DirectoryService`](DirectoryService-v3.md) configuration section\.

You can use this following example to integrate your cluster with an AWS Managed Microsoft AD over the Lightweight Directory Access Protocol \(LDAP\)\.

**Specific definitions that are required for an AWS Managed Microsoft AD over LDAP configuration:**
+ You must set the `ldap_auth_disable_tls_never_use_in_production` parameter to `True` under [`DirectoryService`](DirectoryService-v3.md) / [`AdditionalSssdConfigs`](DirectoryService-v3.md#yaml-DirectoryService-AdditionalSssdConfigs)\.
+ You can specify either controller hostnames or IP addresses for [`DirectoryService`](DirectoryService-v3.md) / [`DomainAddr`](DirectoryService-v3.md#yaml-DirectoryService-DomainAddr)\.
+ [`DirectoryService`](DirectoryService-v3.md) / [`DomainReadOnlyUser`](DirectoryService-v3.md#yaml-DirectoryService-DomainReadOnlyUser) syntax must be as follows:

  ```
  cn=ReadOnly,ou=Users,ou=CORP,dc=corp,dc=example,dc=com
  ```

**Get your AWS Managed Microsoft AD configuration data:**

```
$ aws ds describe-directories --directory-id "d-abcdef01234567890"
```

```
{
    "DirectoryDescriptions": [
        {
            "DirectoryId": "d-abcdef01234567890",
            "Name": "corp.example.com",
            "DnsIpAddrs": [
                "203.0.113.225",
                "192.0.2.254"
            ],
            "VpcSettings": {
                "VpcId": "vpc-021345abcdef6789",
                "SubnetIds": [
                    "subnet-1234567890abcdef0",
                    "subnet-abcdef01234567890"
                ],
                "AvailabilityZones": [
                    "region-idb",
                    "region-idd"
                ]
            }
        }
    ]
}
```

**Cluster configuration for an AWS Managed Microsoft AD:**

```
Region: region-id
Image:
  Os: alinux2
HeadNode:
  InstanceType: t2.micro
  Networking:
    SubnetId: subnet-1234567890abcdef0
  Ssh:
    KeyName: pcluster
Scheduling:
  Scheduler: slurm
  SlurmQueues:
    - Name: queue1
      ComputeResources:
        - Name: t2micro
          InstanceType: t2.micro
          MinCount: 1
          MaxCount: 10
      Networking:
        SubnetIds:
          - subnet-abcdef01234567890
DirectoryService:
  DomainName: dc=corp,dc=example,dc=com
  DomainAddr: ldap://203.0.113.225,ldap://192.0.2.254
  PasswordSecretArn: arn:aws:secretsmanager:region-id:123456789012:secret:MicrosoftAD.Admin.Password-1234
  DomainReadOnlyUser: cn=ReadOnly,ou=Users,ou=CORP,dc=corp,dc=example,dc=com
  AdditionalSssdConfigs:
    ldap_auth_disable_tls_never_use_in_production: True
```

**To use this configuration for a Simple AD, change the `DomainReadOnlyUser` property value in the `DirectoryService` section:**

```
DirectoryService:
  DomainName: dc=corp,dc=example,dc=com
  DomainAddr: ldap://203.0.113.225,ldap://192.0.2.254
  PasswordSecretArn: arn:aws:secretsmanager:region-id:123456789012:secret:SimpleAD.Admin.Password-1234
  DomainReadOnlyUser: cn=ReadOnlyUser,cn=Users,dc=corp,dc=example,dc=com
  AdditionalSssdConfigs:
    ldap_auth_disable_tls_never_use_in_production: True
```

**Considerations:**
+ We recommend that you use LDAP over TLS/SSL \(or LDAPS\) rather than LDAP alone\. TLS/SSL ensures that the connection is encrypted\.
+ The [`DirectoryService`](DirectoryService-v3.md) / [`DomainAddr`](DirectoryService-v3.md#yaml-DirectoryService-DomainAddr) property value matches the entries in the `DnsIpAddrs` list from the `describe-directories` output\.
+ We recommend that your cluster use subnets that are located in the same Availability Zone that the [`DirectoryService`](DirectoryService-v3.md) / [`DomainAddr`](DirectoryService-v3.md#yaml-DirectoryService-DomainAddr) points to\. If you use [custom Dynamic Host Configuration Protocol \(DHCP\) configuration](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/dhcp_options_set.html) that's recommended for directory VPCs and your subnets *aren't* located in the [`DirectoryService`](DirectoryService-v3.md) / [`DomainAddr`](DirectoryService-v3.md#yaml-DirectoryService-DomainAddr) Availability Zone, cross traffic among Availability Zones is possible\. The use of custom DHCP configurations *isn't* required to use the multi\-user AD integration feature\.
+ The [`DirectoryService`](DirectoryService-v3.md) / [`DomainReadOnlyUser`](DirectoryService-v3.md#yaml-DirectoryService-DomainReadOnlyUser) property value specifies a user that must be created in the directory\. This user *isn't* created by default\. We recommend that you *don't* give this user permission to modify directory data\.
+ The [`DirectoryService`](DirectoryService-v3.md) / [`PasswordSecretArn`](DirectoryService-v3.md#yaml-DirectoryService-PasswordSecretArn) property value points to an AWS Secrets Manager secret that contains the password of the user that you specified for the [`DirectoryService`](DirectoryService-v3.md) / [`DomainReadOnlyUser`](DirectoryService-v3.md#yaml-DirectoryService-DomainReadOnlyUser) property\. If this user’s password changes, update the secret value and update the cluster\. To update the cluster for the new secret value, you must stop the compute fleet with the `pcluster update-compute-fleet` command and then run the following command from within the cluster head node\.

  ```
   sudo /opt/parallelcluster/scripts/directory_service/update_directory_service_password.sh
  ```

For another example, see also [Integrating Active Directory](tutorials_05_multi-user-ad.md)\.

## Log in to a cluster integrated with an AD domain<a name="login-addircluster-v3"></a>

If you enabled the Active Delivery \(AD\) domain integration feature, authentication by password is enabled on the cluster head node\. The home directory of an AD user is created at the first user login to the head node or the first time a sudo\-user switches to the AD user on the head node\.

Password authentication isn't enabled for cluster compute nodes\. AD users must log in to compute nodes with SSH keys\.

By default, SSH keys are set up in the AD user `/${HOME}/.ssh` directory at the first SSH login to the head node\. This behavior can be disabled by setting [`DirectoryService`](DirectoryService-v3.md) / [`GenerateSshKeysForUsers`](DirectoryService-v3.md#yaml-DirectoryService-GenerateSshKeysForUsers) boolean property to `false` in the cluster configuration\. By default, [`DirectoryService`](DirectoryService-v3.md) / [`GenerateSshKeysForUsers`](DirectoryService-v3.md#yaml-DirectoryService-GenerateSshKeysForUsers) is set to `true`\.

If an AWS ParallelCluster application requires passwordless SSH between cluster nodes, make sure that the SSH keys are correctly set up in the user's home directory\.

AWS Managed Microsoft AD passwords expire after 42 days\. For more information, see [Manage password policies for AWS Managed Microsoft AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_password_policies.html) in the *AWS Directory Service Administration Guide*\. If your password expires, it must be reset to restore cluster access\. For more information, see [How to reset a user password and expired passwords](troubleshooting-v3-multi-user.md#troubleshooting-v3-multi-user-reset-passwd)\.

**Note**  
If the AD integration feature doesn't work as expected, the SSSD logs can provide useful diagnostic information for troubleshooting the issue\. These logs are located in the `/var/log/sssd` directory on cluster nodes\. By default, they're also stored in a cluster’s Amazon CloudWatch log group\.  
For more information, see [Troubleshooting multi\-user integration with Active Directory](troubleshooting-v3-multi-user.md)\.

## Running MPI jobs<a name="addircluster-MPI-v3"></a>

As suggested in SchedMD, bootstrap MPI jobs using Slurm as the MPI bootstrapping method\. For more information, refer to the official [Slurm documentation](https://slurm.schedmd.com/mpi_guide.html#intel_mpi) or the official documentation for your MPI library\.

For example, in the [IntelMPI official documentation](https://www.intel.com/content/www/us/en/develop/documentation/mpi-developer-reference-linux/top/environment-variable-reference/hydra-environment-variables.html), you learn that when running a StarCCM job, you must set Slurm as process orchestrator by exporting the environment variable `I_MPI_HYDRA_BOOTSTRAP=slurm`\.

**Note**  
Known issue  
In the case where your MPI application relies on SSH as mechanism to spawn MPI jobs, it's possible to incur in a [known bug in Slurm](https://bugs.schedmd.com/show_bug.cgi?id=13385) that causes the wrong resolution of the directory user name to "nobody"\.  
Either configure your application to use Slurm as the MPI bootstrapping method or refer to [Known issues with username resolution](troubleshooting-v3-multi-user.md#troubleshooting-v3-multi-user-name-resolution) in the Troubleshooting section for further details and possible workarounds\.

## Example AWS Managed Microsoft AD over LDAP\(S\) cluster configurations<a name="examples-addir-v3"></a>

AWS ParallelCluster supports multiple user access by integrating with an AWS Directory Service over the Lightweight Directory Access Protocol \(LDAP\), or LDAP over TLS/SSL \(LDAPS\)\.

The following examples show how to create cluster configurations to integrate with an AWS Managed Microsoft AD over LDAP\(S\)\.

### AWS Managed Microsoft AD over LDAPS with certificate verification<a name="LDAP-example-1"></a>

You can use this example to integrate your cluster with an AWS Managed Microsoft AD over LDAPS, with certificate verification\.

**Specific definitions for an AWS Managed Microsoft AD over LDAPS with certificates configuration:**
+ [`DirectoryService`](DirectoryService-v3.md) / [`LdapTlsReqCert`](DirectoryService-v3.md#yaml-DirectoryService-LdapTlsReqCert) must be set to `hard` \(default\) for LDAPS with certificate verification\.
+ [`DirectoryService`](DirectoryService-v3.md) / [`LdapTlsCaCert`](DirectoryService-v3.md#yaml-DirectoryService-LdapTlsCaCert) must specify the path to your certificate of authority \(CA\) certificate\.

  The CA certificate is a certificate bundle that contains the certificates of the entire CA chain that issued certificates for the AD domain controllers\.

  Your CA certificate and certificates must be installed on the cluster nodes\.
+ Controllers hostnames must be specified for [`DirectoryService`](DirectoryService-v3.md) / [`DomainAddr`](DirectoryService-v3.md#yaml-DirectoryService-DomainAddr), not IP addresses\.
+ [`DirectoryService`](DirectoryService-v3.md) / [`DomainReadOnlyUser`](DirectoryService-v3.md#yaml-DirectoryService-DomainReadOnlyUser) syntax must be as follows:

  ```
  cn=ReadOnly,ou=Users,ou=CORP,dc=corp,dc=example,dc=com
  ```

**Example cluster configuration file for using AD over LDAPS:**

```
Region: region-id
Image:
  Os: alinux2
HeadNode:
  InstanceType: t2.micro
  Networking:
    SubnetId: subnet-1234567890abcdef0
  Ssh:
    KeyName: pcluster
  Iam:
    AdditionalIamPolicies:
      - Policy: arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess
  CustomActions:
    OnNodeConfigured:
      Script: s3://aws-parallelcluster/scripts/pcluster-dub-msad-ldaps.post.sh
Scheduling:
  Scheduler: slurm
  SlurmQueues:
    - Name: queue1
      ComputeResources:
        - Name: t2micro
          InstanceType: t2.micro
          MinCount: 1
          MaxCount: 10
      Networking:
        SubnetIds:
          - subnet-abcdef01234567890
      Iam:
        AdditionalIamPolicies:
          - Policy: arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess
      CustomActions:
        OnNodeConfigured:
          Script: s3://aws-parallelcluster-pcluster/scripts/pcluster-dub-msad-ldaps.post.sh
DirectoryService:
  DomainName: dc=corp,dc=example,dc=com
  DomainAddr: ldaps://win-abcdef01234567890.corp.example.com,ldaps://win-abcdef01234567890.corp.example.com
  PasswordSecretArn: arn:aws:secretsmanager:region-id:123456789012:secret:MicrosoftAD.Admin.Password-1234
  DomainReadOnlyUser: cn=ReadOnly,ou=Users,ou=CORP,dc=corp,dc=example,dc=com
  LdapTlsCaCert: /etc/openldap/cacerts/corp.example.com.bundleca.cer
  LdapTlsReqCert: hard
```

**Add certificates and configure domain controllers in post install script:**

```
*#!/bin/bash*
set -e

AD_CERTIFICATE_S3_URI="s3://corp.example.com/bundle/corp.example.com.bundleca.cer"
AD_CERTIFICATE_LOCAL="/etc/openldap/cacerts/corp.example.com.bundleca.cer"

AD_HOSTNAME_1="win-abcdef01234567890.corp.example.com"
AD_IP_1="192.0.2.254"

AD_HOSTNAME_2="win-abcdef01234567890.corp.example.com"
AD_IP_2="203.0.113.225"

# Download CA certificate
mkdir -p $(dirname "${AD_CERTIFICATE_LOCAL}")
aws s3 cp "${AD_CERTIFICATE_S3_URI}" "${AD_CERTIFICATE_LOCAL}"
chmod 644 "${AD_CERTIFICATE_LOCAL}"

# Configure domain controllers reachability
echo "${AD_IP_1} ${AD_HOSTNAME_1}" >> /etc/hosts
echo "${AD_IP_2} ${AD_HOSTNAME_2}" >> /etc/hosts
```

**You can retrieve the domain controllers hostnames from instances joined to the domain as shown in the following examples\.**

**From Windows instance**

```
$ nslookup 192.0.2.254
```

```
Server:  corp.example.com
Address:  192.0.2.254

Name:    win-abcdef01234567890.corp.example.com
Address:  192.0.2.254
```

**From Linux instance**

```
$ nslookup 192.0.2.254
```

```
192.0.2.254.in-addr.arpa   name = corp.example.com
192.0.2.254.in-addr.arpa   name = win-abcdef01234567890.corp.example.com
```

### AWS Managed Microsoft AD over LDAPS without certificate verification<a name="LDAP-example-2"></a>

You can use this example to integrate your cluster with an AWS Managed Microsoft AD over LDAPS, without certificate verification\.

**Specific definitions for an AWS Managed Microsoft AD over LDAPS without certificate verification configuration:**
+ [`DirectoryService`](DirectoryService-v3.md) / [`LdapTlsReqCert`](DirectoryService-v3.md#yaml-DirectoryService-LdapTlsReqCert) must be set to `never`\.
+ Either controller hostnames or IP addresses can be specified for [`DirectoryService`](DirectoryService-v3.md) / [`DomainAddr`](DirectoryService-v3.md#yaml-DirectoryService-DomainAddr)\.
+ [`DirectoryService`](DirectoryService-v3.md) / [`DomainReadOnlyUser`](DirectoryService-v3.md#yaml-DirectoryService-DomainReadOnlyUser) syntax must be as follows:

  ```
  cn=ReadOnly,ou=Users,ou=CORP,dc=corp,dc=example,dc=com
  ```

**Example cluster configuration file for using AWS Managed Microsoft AD over LDAPS without certificate verification:**

```
Region: region-id
Image:
  Os: alinux2
HeadNode:
  InstanceType: t2.micro
  Networking:
    SubnetId: subnet-1234567890abcdef0
  Ssh:
    KeyName: pcluster
Scheduling:
  Scheduler: slurm
  SlurmQueues:
    - Name: queue1
      ComputeResources:
        - Name: t2micro
          InstanceType: t2.micro
          MinCount: 1
          MaxCount: 10
      Networking:
        SubnetIds:
          - subnet-abcdef01234567890
DirectoryService:
  DomainName: dc=corp,dc=example,dc=com
  DomainAddr: ldaps://203.0.113.225,ldaps://192.0.2.254
  PasswordSecretArn: arn:aws:secretsmanager:region-id:123456789012:secret:MicrosoftAD.Admin.Password-1234
  DomainReadOnlyUser: cn=ReadOnly,ou=Users,ou=CORP,dc=corp,dc=example,dc=com
  LdapTlsReqCert: never
```