# Troubleshooting multiuser integration with Active Directory<a name="troubleshooting-v3-multi-user"></a>

This section is relevant to clusters integrated with an Active Directory\.

If the Active Directory integration feature isn't working as expected the SSSD logs can provide useful diagnostic information\. These logs are located in `/var/log/sssd` on cluster nodes\. By default, they're also stored in a cluster’s Amazon CloudWatch log group\.

**Topics**
+ [Active Directory specific troubleshooting](#troubleshooting-v3-multi-ad-specific)
+ [Enable debug mode](#troubleshooting-v3-multi-user-debug)
+ [How to move from LDAPS to LDAP](#troubleshooting-v3-multi-user-ldaps-ldap)
+ [How to disable LDAPS server certificate verification](#troubleshooting-v3-multi-user-ldaps-verify)
+ [How to log in with an SSH key rather than password](#troubleshooting-v3-multi-user-ssh-login)
+ [How to reset a user password and expired passwords](#troubleshooting-v3-multi-user-reset-passwd)
+ [How to verify the joined domain](#troubleshooting-v3-multi-user-domain-verify)
+ [How to troubleshoot issues with certificates](#troubleshooting-v3-multi-user-certificates)
+ [How to verify that the integration with Active Directory is working](#troubleshooting-v3-multi-user-ad-verify)
+ [How to troubleshoot logging in to compute nodes](#troubleshooting-v3-multi-user-ad-compute-node-login)
+ [Known issues with SimCenter StarCCM\+ jobs in a multiuser environment](#troubleshooting-v3-multi-user-ad-starccm)
+ [Known issues with username resolution](#troubleshooting-v3-multi-user-name-resolution)
+ [How to resolve home directory create issues](#troubleshooting-v3-multi-home-directory)

## Active Directory specific troubleshooting<a name="troubleshooting-v3-multi-ad-specific"></a>

This section is relevant to troubleshooting specific to an Active Directory type\.

### Simple AD<a name="troubleshooting-v3-multi-user-simple-ad"></a>
+ The `DomainReadOnlyUser` value must match the Simple AD directory base search for users:

  `cn=ReadOnlyUser,cn=Users,dc=corp,dc=pcluster,dc=com`

  Note `cn` for `Users`\.
+ Default admin user is `Administrator`\.
+ `Ldapsearch` requires NetBIOS name before the username\.

  `Ldapsearch` syntax must be as follows:

  ```
  $ ldapsearch -x -D "corp\\Administrator" -w "Password" -H ldap://192.0.2.103 \
     -b "cn=Users,dc=corp,dc=pcluster,dc=com"
  ```

### AWS Managed Microsoft AD<a name="troubleshooting-v3-multi-users-ms-ad"></a>
+ The `DomainReadOnlyUser` value must match the AWS Managed Microsoft AD directory base search for users:

  `cn=ReadOnlyUser,ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com`
+ Default admin user is `Admin`\.
+ `Ldapsearch` syntax must be as follows:

  ```
  $ ldapsearch -x -D "Admin" -w "Password" -H ldap://192.0.2.103 \
     -b "ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com"
  ```

## Enable debug mode<a name="troubleshooting-v3-multi-user-debug"></a>

Debug logs from SSSD can be useful to troubleshoot issues\. To enable debug mode, you must update the cluster with the following changes made to the cluster configuration:

```
DirectoryService:
  AdditionalSssdConfigs:
    debug_level: "0x1ff"
```

## How to move from LDAPS to LDAP<a name="troubleshooting-v3-multi-user-ldaps-ldap"></a>

Moving from LDAPS \(LDAP with TLS/SSL\) to LDAP is discouraged because LDAP alone doesn't provide any encryption\. Nevertheless, it can be useful for testing purposes and troubleshooting\.

You can restore the cluster to its previous configuration by updating the cluster with the previous configuration definition\.

To move from LDAPS to LDAP, you must update the cluster with the following changes in the cluster configuration:

```
DirectoryService:
  LdapTlsReqCert: never
  AdditionalSssdConfigs:
    ldap_auth_disable_tls_never_use_in_production: True
```

## How to disable LDAPS server certificate verification<a name="troubleshooting-v3-multi-user-ldaps-verify"></a>

It can be useful to temporarily disable LDAPS server certificate verification on the head node, for testing or troubleshooting purposes\.

You can restore the cluster to its previous configuration by updating the cluster with the previous configuration definition\.

To disable the LDAPS server certificate verification, you must update the cluster with the following changes in the cluster configuration:

```
DirectoryService:
  LdapTlsReqCert: never
```

## How to log in with an SSH key rather than password<a name="troubleshooting-v3-multi-user-ssh-login"></a>

The SSH key is created in `/home/$user/.ssh/id_rsa` after the first time that you log in with a password\. To log in with the SSH key, you must log in with your password, copy the SSH key locally, and then use it to SSH password\-less as usual:

```
$ ssh -i $LOCAL_PATH_TO_SSH_KEY $username@$head_node_ip
```

## How to reset a user password and expired passwords<a name="troubleshooting-v3-multi-user-reset-passwd"></a>

If a user loses access to a cluster, their [AWS Managed Microsoft AD password might have expired](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_password_policies.html)\.

To reset the password, run the following command with a user and role having write permission on the directory:

```
$ aws ds reset-user-password \
  --directory-id "d-abcdef01234567890" \
  --user-name "USER_NAME" \
  --new-password "NEW_PASSWORD" \
  --region "region-id"
```

If you reset the password for the [`DirectoryService`](DirectoryService-v3.md) / [`DomainReadOnlyUser`](DirectoryService-v3.md#yaml-DirectoryService-DomainReadOnlyUser):

1. Be sure to update the [`DirectoryService`](DirectoryService-v3.md) / [`PasswordSecretArn`](DirectoryService-v3.md#yaml-DirectoryService-PasswordSecretArn) secret with the new password\.

1. Update the cluster for the new secret value:

   1. Stop the compute fleet with the `pcluster update-compute-fleet` command\.

   1. Run the following command from within the cluster head node\.

      ```
      $ sudo /opt/parallelcluster/scripts/directory_service/update_directory_service_password.sh
      ```

After the password reset and cluster update, the user's cluster access should be restored\.

For more information, see [Reset a user password](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_manage_users_groups_reset_password.html) in the *AWS Directory Service Administration Guide*\.

## How to verify the joined domain<a name="troubleshooting-v3-multi-user-domain-verify"></a>

The following command must run from an instance that's joined to the domain, not the head node\.

```
$ realm list corp.pcluster.com \
  type: kerberos \
  realm-name: CORP.PCLUSTER.COM \
  domain-name: corp.pcluster.com \
  configured: kerberos-member \
  server-software: active-directory \
  client-software: sssd \
  required-package: oddjob \
  required-package: oddjob-mkhomedir \
  required-package: sssd \
  required-package: adcli \
  required-package: samba-common-tools \
  login-formats: %U \
  login-policy: allow-realm-logins
```

## How to troubleshoot issues with certificates<a name="troubleshooting-v3-multi-user-certificates"></a>

When LDAPS communication isn't working, it can be due to errors in the TLS communication, which in turn can be due to issues with certificates\.

**Notes about certificates:**
+ The certificate specified in cluster config `LdapTlsCaCert` must be a bundle of PEM certificates containing the certificates for the whole certificate of authority \(CA\) chain that issued certificates for the domain controllers\.
+ A bundle of PEM certificates is a file made of the concatenation of PEM certificates\.
+ A certificate in PEM format \(typically used in Linux\) is equivalent to a certificate in base64 DER format \(typically exported by Windows\)\.
+ If the certificate for domain controllers is issued by a subordinate CA, then the certificate bundle must contain the certificate of both the subordinate and root CA\.

**Troubleshooting verification steps:**

The following verification steps assume that the commands are run from within the cluster head node and that the domain controller is reachable at `SERVER:PORT`\.

To troubleshoot an issue that's related to certificates, follow these verification steps:

**Verification steps:**

1. **Check the connection to the Active Directory domain controllers:**

   Verify that you can connect to a domain controller\. If this step succeeds, then the SSL connection to the domain controller succeeds and the certificate is verified\. Your issue isn't related to certificates\.

   If this step fails, go ahead with next verification\.

   ```
   $ openssl s_client -connect SERVER:PORT -CAfile PATH_TO_CA_BUNDLE_CERTIFICATE
   ```

1. **Check the certificate verification:**

   Verify that the local CA certificate bundle can validate the certificate provided by the domain controller\. If this step succeeds, then your issue isn't related to certificates, but to other networking issues\.

   If this step fails, go ahead with next verification\.

   ```
   $ openssl verify -verbose -CAfile PATH_TO_CA_BUNDLE_CERTIFICATE PATH_TO_A_SERVER_CERTIFICATE
   ```

1. **Check the certificate provided by the Active Directory domain controllers:**

   Verify that the content of the certificate provided by the domain controllers is as expected\. If this step succeeds, you probably have issues with the CA certificate used to verify controllers, go to the next troubleshooting step\.

   If this step fails, you must correct the certificate issued for the domain controllers and re\-execute the troubleshooting steps\.

   ```
   $ openssl s_client -connect SERVER:PORT -showcerts
   ```

1. **Check the content of a certificate:**

   Verify that the content of the certificate that's provided by the domain controllers is as expected\. If this step succeeds, you probably have issues with the CA certificate used to verify controller’s, go to the next troubleshooting step\.

   If this step fails, you must correct the certificate issued for the domain controllers and rerun the troubleshooting steps\.

   ```
   $ openssl s_client -connect SERVER:PORT -showcerts
   ```

1. **Check the content of the local CA certificate bundle:**

   Verify that the content of the local CA certificate bundle used to validate domain controllers certificate is as expected\. If this step succeeds, you probably have issues with the certificate that are provided by the domain controllers\.

   If this step fails, you must correct CA certificate bundle issued for the domain controllers and rerun the troubleshooting steps\.

   ```
   $ openssl x509 -in PATH_TO_A_CERTIFICATE -text
   ```

## How to verify that the integration with Active Directory is working<a name="troubleshooting-v3-multi-user-ad-verify"></a>

If the following two checks succeed, the integration with the Active Directory is working\.

**Checks:**

1. **You can discover users defined in the directory:**

   From within the cluster head node, as an `ec2-user`:

   ```
   $ getent passwd $ANY_AD_USER
   ```

1. **You can SSH into the head node providing the user password:**

   ```
   $ ssh $ANY_AD_USER@$HEAD_NODE_IP
   ```

If check one fails, we expect check two to fail also\.

Additional troubleshooting checks:
+ Verify that the user exists in the directory\.
+ Enable [debug logging](#troubleshooting-v3-multi-user-debug)\.
+ Consider temporarily disabling encryption by [moving from LDAPS to LDAP](#troubleshooting-v3-multi-user-ldaps-ldap) to rule out LDAPS issues\.

## How to troubleshoot logging in to compute nodes<a name="troubleshooting-v3-multi-user-ad-compute-node-login"></a>

This section is relevant to logging in to compute nodes in clusters integrated with Active Directory\.

With AWS ParallelCluster, password logins to cluster compute nodes are disabled by design\.

All users must use their own SSH key to log in to compute nodes\.

Users can retrieve their SSH key in the head node after first authentication \(for example login\), if [`GenerateSshKeysForUsers`](DirectoryService-v3.md#yaml-DirectoryService-GenerateSshKeysForUsers) is enabled in the cluster configuration\.

When users authenticate on the head node for the first time, they can retrieve SSH keys that are automatically generated for them as directory users\. Home directories for the user are also created\. This can also happen the first time a sudo\-user switches to a user in the head node\.

If a user hasn't logged into the head node, SSH keys aren't generated and the user won't be able to log in to compute nodes\.

## Known issues with SimCenter StarCCM\+ jobs in a multiuser environment<a name="troubleshooting-v3-multi-user-ad-starccm"></a>

This section is relevant to jobs launched in a multiuser environment by Simcenter StarCCM\+ computational fluid dynamics software from Siemens\.

If you run StarCCM\+ v16 jobs configured to use the embedded IntelMPI, by default the MPI processes are bootstrapped using SSH\.

Due to a known [Slurm bug](https://bugs.schedmd.com/show_bug.cgi?id=13385) that causes username resolution to be wrong, jobs might fail with an error like `error setting up the bootstrap proxies`\. This bug only impacts AWS ParallelCluster versions 3\.1\.1 and 3\.1\.2\.

To prevent this from occurring, force IntelMPI to use Slurm as MPI bootstrap method\. Export the environment variable `I_MPI_HYDRA_BOOTSTRAP=slurm` into the job script that launches StarCCM\+, as described in the [IntelMPI official documentation](https://www.intel.com/content/www/us/en/develop/documentation/mpi-developer-reference-linux/top/environment-variable-reference/hydra-environment-variables.html)\.

## Known issues with username resolution<a name="troubleshooting-v3-multi-user-name-resolution"></a>

This section is relevant to retrieving usernames within jobs\.

Due to a known [bug in Slurm](https://bugs.schedmd.com/show_bug.cgi?id=13385), the username retrieved within a job process might be `nobody` if you run a job without `srun`\. This bug only impacts AWS ParallelCluster versions 3\.1\.1 and 3\.1\.2\.

For example, if you run the command `sbatch --wrap 'srun id'` as a directory user, the correct username is returned\. However, if you run the `sbatch --wrap 'id'` as a directory user, `nobody` might be returned as the username\.

You can use the following workarounds\.

1. Launch your job with `'srun'` instead of `'sbatch'`, if possible\.

1. Enable SSSD enumeration by setting the [AdditionalSssdConfigs](DirectoryService-v3.md#yaml-DirectoryService-AdditionalSssdConfigs) in cluster configuration as follows\.

   ```
   AdditionalSssdConfigs:
     enumerate: true
   ```

## How to resolve home directory create issues<a name="troubleshooting-v3-multi-home-directory"></a>

This section is relevant to home directory creation issues\.

If you see errors like the one shown in the following example, a home directory wasn't created for you when you first logged in to the head node\. Or, a home directory wasn't created for you when you first switched from a sudoer to an Active Directory user in the head node\.

```
$ ssh AD_USER@$HEAD_NODE_IP
/opt/parallelcluster/scripts/generate_ssh_key.sh failed: exit code 1

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
Could not chdir to home directory /home/PclusterUser85: No such file or directory
```

The home directory create failure can be caused by the `oddjob` and `oddjob-mkhomedir` packages installed in the cluster head node\.

Without a home directory and SSH key, the user can't submit jobs or SSH into the cluster nodes\.

If you need the `oddjob` packages in your system, verify that the `oddjobd` service is running and refresh the PAM config files to make sure that the home directory is created\. To do this, run the commands in the head node as shown in the following example\.

```
sudo systemctl start oddjobd
sudo authconfig --enablemkhomedir --updateall
```

If you don't need the `oddjob` packages in your system, uninstall them and refresh the PAM config files to make sure that the home directory is created\. To do this, run the commands in the head node as shown in the following example\.

```
sudo yum remove -y oddjob oddjob-mkhomedir
sudo authconfig --enablemkhomedir --updateall
```