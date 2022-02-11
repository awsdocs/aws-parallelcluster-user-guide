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
The Active Directory \(AD\) domain that you use for identity information\. This property corresponds to the sssd\-ldap parameter that's called `ldap_search_base`\.  

Use the syntax that the LDAP client requires on the node \(for example, `dc=corp,dc=pcluster,dc=com`\) to do the following actions:
+ Limit the portion of the AD domain that configures multiple domains\.
+ Change the scope of the query\.
+ Add a filter\.
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`DomainAddr` \(**Required**, `String`\)  
The URI or URIs that point to the AD domain controller that's used as the LDAP server\. The URI corresponds to the sssd\-ldap parameter that's called `ldap_uri`\. The value can be a comma separated string of URIs\. To use LDAP, you must add `ldap://` to the beginning of the each URI\.  
Example values:  

```
ldap://192.0.2.0,ldap://203.0.113.0          # LDAP
ldaps://192.0.2.0,ldaps://203.0.113.0        # LDAPS without support for certificate verification
ldaps://abcdef01234567890.corp.pcluster.com  # LDAPS with support for certificate verification
192.0.2.0,203.0.113.0                        # AWS ParallelCluster uses LDAPS by default
```
If you use LDAPS with certificate verification, the URIs must be hostnames\.  
If you use LDAPS without certificate verification or LDAP, URIs can be hostnames or IP addresses\.  
Use LDAP over TSL/SSL \(LDAPS\) to avoid transmission of passwords and other sensitive information over unencrypted channels\. If AWS ParallelCluster doesn't find a protocol, it adds `ldaps://` to the beginning of each URI or hostname\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`PasswordSecretArn` \(**Required**, `String`\)  
The Amazon Resource Name \(ARN\) of the AWS Secrets Manager secret that contains a password\. This ARN corresponds to sssd\-ldap parameter that's called `ldap_default_authtok`\.  
The LDAP client uses the password to authenticate to the AD domain as a `DomainReadOnlyUser` when requesting identity information\.  
When the value of the secret changes, the cluster *isn't* automatically updated\. To update the cluster for the new secret value, update the cluster manually or run the following command\.  

```
$ sudo cinc-client \
  --local-mode \
  --config /etc/chef/client.rb \
  --log_level auto \
  --force-formatter \
  --no-color \
  --chef-zero-port 8889 \
  --json-attributes /etc/chef/dna.json \
  --override-runlist aws-parallelcluster-config::directory_service
```
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`DomainReadOnlyUser` \(**Required**, `String`\)  
The identity that's used to query the AD domain for identity information when authenticating cluster user logins\. It corresponds to sssd\-ldap parameter that's called `ldap_default_bind_dn`\. Use your AD identity information for this value\.  
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
The absolute path to a certificates bundle containing the certificates for every certification authority in the certification chain that issued a certificate for the domain controllers\. It corresponds to the sssd\-ldap parameter that's called `ldap_tls_cacert`\.  
A certificate bundle is a file that's composed of the concatenation of distinct certificates in PEM format, also known as DER Base64 format in Windows\. It is used to verify the identity of the AD domain controller that is acting as the LDAP server\.  
AWS ParallelCluster isn't responsible for initial placement of certificates onto nodes\. As the cluster administrator, you can configure the certificate in the head node manually after the cluster is created or you can use a [bootstrap script](custom-bootstrap-actions-v3.md)\. Alternatively, you can use an Amazon Machine Image \(AMI\) that includes the certificate configured on the head node\.  
[Simple AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_simple_ad.html) doesn't provide LDAPS support\. To learn how to integrate a Simple AD directory with AWS ParallelCluster, see [How to configure an LDAPS endpoint for Simple AD](http://aws.amazon.com/blogs/security/how-to-configure-ldaps-endpoint-for-simple-ad/) in the *AWS Security Blog*\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`LdapTlsReqCert` \(**Optional**, `String`\)  
Specifies what checks to perform on server certificates in a TLS session\. It corresponds to sssd\-ldap parameter that's called `ldap_tls_reqcert`\.  
Valid values: `never`, `allow`, `try`, `demand`, and `hard`\.  
`never`, `allow`, and `try` enable connections to proceed even if problems with certificates are found\.  
`demand` and `hard` enable communication to continue if no problems with certificates are found\.  
If the cluster administrator uses a value that doesn't require the certificate validation to succeed, a warning message is returned to the administrator\. For security reasons, we recommend that you don't disable certificate verification\.  
The default value is `hard`\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`LdapAccessFilter` \(**Optional**, `String`\)  
Specifies a filter to limit LDAP queries to a subset of the directory that's being queried\. This property corresponds to the sssd\-ldap parameter that's called `ldap_access_filter`\. You can use it to limit queries to an AD that supports a large number of users\.  
This filter can block user access to the cluster\. However, it doesn't impact the discoverability of blocked users\.  
If this property is omitted, all users in the directory can access the cluster\.  
Examples:  

```
"!(cn=SomeUser*)"  # denies access to every user with alias starting with "SomeUser"
"(cn=SomeUser*)"   # allows access to every user with alias starting with "SomeUser"
"memberOf=cn=TeamOne,ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com" # allows access only to users in group "TeamOne".
```
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`GenerateSshKeysForUsers` \(**Optional**, `Boolean`\)  
Defines whether AWS ParallelCluster generates SSH key pairs for cluster users after they log in to the head node for the first time\. The key pair is saved to the user home directory at `/home/username/.ssh/`\. Users can use the SSH key pair for subsequent logins to the cluster head node and compute nodes\. With AWS ParallelCluster, logins to cluster compute nodes are disabled by design\. If a user hasn't logged into the head node, SSH keys aren't generated and the user won't be able to log in to compute nodes\.  
By default, AWS ParallelCluster generates a SSH key pair for each user at initial login\.  
The default is `TRUE`\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)

`AdditionalSssdConfigs` \(**Optional**, `Dict`\)  
A dict of key\-value pairs containing arbitrary SSSD parameters and values to write to the SSSD config file on cluster instances\. For a full description of the SSSD configuration file, see the on\-instance man pages for `sssd` and `sssd-ldap`\.  
[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update-cluster-v3.md#update-policy-compute-fleet-v3)