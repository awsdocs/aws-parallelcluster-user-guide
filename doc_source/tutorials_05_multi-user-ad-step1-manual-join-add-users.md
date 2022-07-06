# Add users to the AD<a name="tutorials_05_multi-user-ad-step1-manual-join-add-users"></a>

1. 

**Create the ReadOnlyUser and an additional user\.**

   In this step, you use [adcli](https://www.mankier.com/package/adcli) and [openldap\-clients](https://www.mankier.com/package/openldap-clients) tools that you installed in a preceding step\.

   ```
   $ echo $ADMIN_PW | adcli create-user -x -U Admin --domain=corp.pcluster.com --display-name=ReadOnlyUser ReadOnlyUser
   ```

   ```
   $ echo $ADMIN_PW | adcli create-user -x -U Admin --domain=corp.pcluster.com --display-name=user000 user000
   ```

1. **Verify the users have been created:**

   The directory DNS IP addresses are outputs of the python script\.

   ```
   $ DIRECTORY_IP="192.0.2.254"
   ```

   ```
   $ ldapsearch -x -h $DIRECTORY_IP -D Admin -w $ADMIN_PW -b "cn=ReadOnlyUser,ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com"
   ```

   ```
   $ ldapsearch -x -h $DIRECTORY_IP -D Admin -w $ADMIN_PW -b "cn=user000,ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com"
   ```

   When you create a user with the `ad-cli`, the user is disabled by default\.

1. 

****Reset and activate the user passwords from your local machine:****

   Log out of your EC2 instance\.
**Note**  
`ro-p@ssw0rd` below is the password of `ReadOnlyUser`, retrieved via secrets manager \(explained below\)\.
`user-p@ssw0rd` is the password of a cluster user, provided when you connect \(`ssh`\) to the cluster\.

   The `directory-id` is an output of the python script\.

   ```
   $ DIRECTORY_ID="d-abcdef01234567890"
   ```

   ```
   $ aws ds reset-user-password \
   --directory-id $DIRECTORY_ID \
   --user-name "ReadOnlyUser" \
   --new-password "ro-p@ssw0rd" \
   --region "region-id"
   ```

   ```
   $ aws ds reset-user-password \
   --directory-id $DIRECTORY_ID \
   --user-name "user000" \
   --new-password "user-p@ssw0rd" \
   --region "region-id"
   ```

1. **Add the password to a Secrets Manager secret\.**

   Now that you have created a `ReadOnlyUser` and set the password, store it in a secret that AWS ParallelCluster uses for validating logins\.

   Use Secrets Manager to create a new secret to hold the password for the `ReadOnlyUser` as the value\. The secret value format must be text only, not JSON\. Make note of the secret ARN for future steps\.

   ```
   $ aws secretsmanager create-secret --name "ADSecretPassword" \
   --region region_id \
   --secret-string "ro-p@ssw0rd" \
   --query ARN \
   --output text
   arn:aws:secretsmanager:region-id:123456789012:secret:ADSecretPassword-1234
   ```