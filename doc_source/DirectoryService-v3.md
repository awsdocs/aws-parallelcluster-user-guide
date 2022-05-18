# `DirectoryService` section<a name="DirectoryService-v3"></a>

**Note**  
Support for `DirectoryService` was added in AWS ParallelCluster version 3\.1\.1\.

**\(Optional\)** The directory service settings for a cluster that supports multiple user access\.

AWS ParallelCluster manages permissions that support multiple user access to clusters with an Active Directory \(AD\) over Lightweight Directory Access Protocol \(LDAP\) supported by the [System Security Services Daemon \(SSSD\)](https://sssd.io/docs/introduction.html)\. For more information, see [What is AWS Directory Service?](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/what_is.html) in the *AWS Directory Service Administration Guide*\.

We recommend that you use LDAP over TLS/SSL \(abbreviated LDAPS for short\) to ensure that any potentially sensitive information is transmitted over encrypted channels\.

```
DirectoryService:
  DomainName: string
  DomainAddr: string
  PasswordSecretArn: string
  DomainReadOnlyUser: string
  LdapTlsCaCert: string
  LdapTlsReqCert: string
  LdapAccessFilter: string
  GenerateSshKeysForUsers: boolean
  AdditionalSssdConfigs: dict
```

## `DirectoryService` Properties<a name="DirectoryService-v3.properties"></a>

**Note**  
If you plan to use AWS ParallelCluster in a single subnet with no internet access, see [AWS ParallelCluster in a single subnet with no internet access](network-configuration-v3.md#aws-parallelcluster-in-a-single-public-subnet-no-internet-v3) for additional requirements\.

`DomainName` \(**Required**, `String`\)  
The Active Directory \(AD\) domain that you use for identity information\.  
`DomainName` accepts both the Fully Qualified Domain Name \(FQDN\) and LDAP Distinguished Name \(DN\) formats\.  
+ FQDN example: `corp.pcluster.com`
+ LDAP DN example: `DC=corp,DC=pcluster,DC=com`
This property corresponds to the sssd\-ldap parameter that's called `ldap_search_base`\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`DomainAddr` \(**Required**, `String`\)  
The URI or URIs that point to the AD domain controller that's used as the LDAP server\. The URI corresponds to the SSSD\-LDAP parameter that's called `ldap_uri`\. The value can be a comma separated string of URIs\. To use LDAP, you must add `ldap://` to the beginning of the each URI\.  
Example values:  

```
ldap://192.0.2.0,ldap://203.0.113.0          # LDAP
ldaps://192.0.2.0,ldaps://203.0.113.0        # LDAPS without support for certificate verification
ldaps://abcdef01234567890.corp.pcluster.com  # LDAPS with support for certificate verification
192.0.2.0,203.0.113.0                        # AWS ParallelCluster uses LDAPS by default
```
If you use LDAPS with certificate verification, the URIs must be hostnames\.  
If you use LDAPS without certificate verification or LDAP, URIs can be hostnames or IP addresses\.  
Use LDAP over TLS/SSL \(LDAPS\) to avoid transmission of passwords and other sensitive information over unencrypted channels\. If AWS ParallelCluster doesn't find a protocol, it adds `ldaps://` to the beginning of each URI or hostname\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`PasswordSecretArn` \(**Required**, `String`\)  
The Amazon Resource Name \(ARN\) of the AWS Secrets Manager secret that contains the `DomainReadOnlyUser` plaintext password\. The content of the secret corresponds to SSSD\-LDAP parameter that's called `ldap_default_authtok`\.  
The LDAP client uses the password to authenticate to the AD domain as a `DomainReadOnlyUser` when requesting identity information\.  
If the user has the permission to `[DescribeSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DescribeSecret.html)`, `PasswordSecretArn` is validated\. `PasswordSecretArn` is valid if the specified secret exists\. If the user IAM policy doesn't include `DescribeSecret`, `PasswordSecretArn` isn't validated and a warning message is displayed\. For more information, see [Base user policy required to invoke AWS ParallelCluster features](iam-roles-in-parallelcluster-v3.md#iam-roles-in-parallelcluster-v3-base-user-policy)\.  
When the value of the secret changes, the cluster *isn't* automatically updated\. To update the cluster for the new secret value, you must stop the compute fleet with the ``pcluster update-compute-fleet`` command and then run the following command from within the head node\.  

```
$ sudo ./opt/parallelcluster/scripts/directory_service/update_directory_service_password.sh
```
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`DomainReadOnlyUser` \(**Required**, `String`\)  
The identity that's used to query the AD domain for identity information when authenticating cluster user logins\. It corresponds to SSSD\-LDAP parameter that's called `ldap_default_bind_dn`\. Use your AD identity information for this value\.  
Specify the identity in the form required by the specific LDAP client that's on the node:  
+ MicrosoftAD:

  ```
  cn=ReadOnlyUser,ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com
  ```
+ SimpleAD:

  ```
  cn=ReadOnlyUser,cn=Users,dc=corp,dc=pcluster,dc=com
  ```
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`LdapTlsCaCert` \(**Optional**, `String`\)  
The absolute path to a certificates bundle containing the certificates for every certification authority in the certification chain that issued a certificate for the domain controllers\. It corresponds to the SSSD\-LDAP parameter that's called `ldap_tls_cacert`\.  
A certificate bundle is a file that's composed of the concatenation of distinct certificates in PEM format, also known as DER Base64 format in Windows\. It is used to verify the identity of the AD domain controller that is acting as the LDAP server\.  
AWS ParallelCluster isn't responsible for initial placement of certificates onto nodes\. As the cluster administrator, you can configure the certificate in the head node manually after the cluster is created or you can use a [bootstrap script](custom-bootstrap-actions-v3.md)\. Alternatively, you can use an Amazon Machine Image \(AMI\) that includes the certificate configured on the head node\.  
[Simple AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_simple_ad.html) doesn't provide LDAPS support\. To learn how to integrate a Simple AD directory with AWS ParallelCluster, see [How to configure an LDAPS endpoint for Simple AD](http://aws.amazon.com/blogs/security/how-to-configure-ldaps-endpoint-for-simple-ad/) in the *AWS Security Blog*\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`LdapTlsReqCert` \(**Optional**, `String`\)  
Specifies what checks to perform on server certificates in a TLS session\. It corresponds to SSSD\-LDAP parameter that's called `ldap_tls_reqcert`\.  
Valid values: `never`, `allow`, `try`, `demand`, and `hard`\.  
`never`, `allow`, and `try` enable connections to proceed even if problems with certificates are found\.  
`demand` and `hard` enable communication to continue if no problems with certificates are found\.  
If the cluster administrator uses a value that doesn't require the certificate validation to succeed, a warning message is returned to the administrator\. For security reasons, we recommend that you don't disable certificate verification\.  
The default value is `hard`\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`LdapAccessFilter` \(**Optional**, `String`\)  
Specifies a filter to limit directory access to a subset of users\. This property corresponds to the SSSD\-LDAP parameter that's called `ldap_access_filter`\. You can use it to limit queries to an AD that supports a large number of users\.  
This filter can block user access to the cluster\. However, it doesn't impact the discoverability of blocked users\.  
If this property is set, the SSSD parameter `access_provider` is set to `ldap` internally by AWS ParallelCluster and must not be modified by `DirectoryService` / `AdditionalSssdConfigs` settings\.  
If this property is omitted and customized user access isn't specified in `DirectoryService` / `AdditionalSssdConfigs`, all users in the directory can access the cluster\.  
Examples:  

```
"!(cn=SomeUser*)"  # denies access to every user with alias starting with "SomeUser"
"(cn=SomeUser*)"   # allows access to every user with alias starting with "SomeUser"
"memberOf=cn=TeamOne,ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com" # allows access only to users in group "TeamOne".
```
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`GenerateSshKeysForUsers` \(**Optional**, `Boolean`\)  
Defines whether AWS ParallelCluster generates an SSH key for cluster users immediately after their initial authentication on the head node\.  
If set to `true`, an SSH key is generated and saved to `USER_HOME_DIRECTORY/.ssh/id_rsa`, if it doesn't exist, for every user after their first authentication on the head node\.  

For a user that has not yet been authenticated on the head node, first authentication can happen in the following cases:
+ The user logs into the head node for the first time with her or his own password\.
+ In the head node, a sudoer switches to the user for the first time: `su USERNAME`
+ In the head node, a sudoer runs a command as the user for the first time: `su -u USERNAME COMMAND`
Users can use the SSH key for subsequent logins to the cluster head node and compute nodes\. With AWS ParallelCluster, password logins to cluster compute nodes are disabled by design\. If a user hasn't logged into the head node, SSH keys aren't generated and the user won't be able to log in to compute nodes\.  
The default is `true`\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`AdditionalSssdConfigs` \(**Optional**, `Dict`\)  
A dict of key\-value pairs containing SSSD parameters and values to write to the SSSD config file on cluster instances\. For a full description of the SSSD configuration file, see the on\-instance man pages for `SSSD` and related configuration files\.  
The SSSD parameters and values must be compatible with AWS ParallelCluster's SSSD configuration as described in the following list\.  
+ `id_provider` is set to `ldap` internally by AWS ParallelCluster and must not be modified\.
+ `access_provider` is set to `ldap` internally by AWS ParallelCluster when `DirectoryService` / `LdapAccessFilter` is specified, and this setting must not be modified\.

  If `DirectoryService` / `LdapAccessFilter` is omitted, its `access_provider` specification is omitted also\. For example, if you set `access_provider` to `simple` in `AdditionalSssdConfigs`, then `DirectoryService` / `LdapAccessFilter` must not be specified\.
The following configuration snippets are examples of valid configurations for `AdditionalSssdConfigs`\.  
This example enables debug level for SSSD logs, restricts the search base to a specific organizational unit, and disables credentials caching\.  

```
DirectoryService:
  ...
  AdditionalSssdConfigs:
    debug_level: "0xFFF0"
    ldap_search_base: OU=Users,OU=CORP,DC=corp,DC=myOrg,DC=com
    cache_credentials: False
```
This example specifies the configuration of an SSSD `[simple](https://www.mankier.com/5/sssd-simple)` `access_provider`\. Users from the `EngineeringTeam` are provided access to the directory\. `DirectoryService` / `LdapAccessFilter` must not be set in this case\.  

```
DirectoryService:
  ...
  AdditionalSssdConfigs:
    access_provider: simple
    simple_allow_groups: EngineeringTeam
```
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)