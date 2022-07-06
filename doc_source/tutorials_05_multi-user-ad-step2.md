# \(Optional\) Step 2: Manage AD users and groups<a name="tutorials_05_multi-user-ad-step2"></a>

In this step, you manage users and groups from an EC2 Amazon Linux 2 instance that is joined to the AD domain\.

If you followed the *automated* path, restart and log in to the AD joined instance that was created as part of the automation\.

If you followed the *manual* path, restart and log in to the instance that you created and joined to the AD in preceding steps\.

In these steps, you use the [adcli](https://www.mankier.com/package/adcli) and [openldap\-clients](https://www.mankier.com/package/openldap-clients) tools that were installed in the instance as part of a preceding step\.

**Log in to an EC2 instance that is joined to the AD domain**

1. From the EC2 console, select the untitled EC2 instance that was created in previous steps\. The instance state might be **Stopped**\.

1. If the instance state is **Stopped**, choose **Instance state** and then **Start instance**\.

1. After the status checks pass, select the instance and choose **Connect** and ssh in to the instance\.

**Manage users and groups when logged into an EC2 Amazon Linux 2 instance that is joined the AD**

The `adcli` commands with the ` -U "Admin"` option ask you to enter the AD `Admin` password\. You include the AD `Admin` password as part of the `ldapsearch` commands\.

1. 

**Create a user\.**

   ```
   $ adcli create-user "clusteruser" --domain "corp.pcluster.com" -U "Admin"
   ```

1. 

**Set a user password**

   ```
   $ aws --region "region-id" ds reset-user-password --directory-id "d-abcdef01234567890" --user-name "clusteruser" --new-password "new-p@ssw0rd"
   ```

1. 

**Create a group\.**

   ```
   $ adcli create-group "clusterteam" --domain "corp.pcluster.com" -U "Admin"
   ```

1. 

**Add a user to a group\.**

   ```
   $ adcli add-member "clusterteam" "clusteruser" --domain "corp.pcluster.com" -U "Admin"
   ```

1. 

**Describe users and groups**

   Describe all users\.

   ```
   $ ldapsearch "(&(objectClass=user))" -x -h "192.0.2.254" -b "DC=corp,DC=pcluster,DC=com" -D "CN=Admin,OU=Users,OU=CORP,DC=corp,DC=pcluster,DC=com" -w "p@ssw0rd"
   ```

   Describe a specific user\.

   ```
   $ ldapsearch "(&(objectClass=user)(cn=clusteruser))" -x -h "192.0.2.254" -b "DC=corp,DC=pcluster,DC=com" -D "CN=Admin,OU=Users,OU=CORP,DC=corp,DC=pcluster,DC=com" -w "p@ssw0rd"
   ```

   Describe all users with a name pattern\.

   ```
   $ ldapsearch "(&(objectClass=user)(cn=user*))" -x -h "192.0.2.254" -b "DC=corp,DC=pcluster,DC=com" -D "CN=Admin,OU=Users,OU=CORP,DC=corp,DC=pcluster,DC=com" -w "p@ssw0rd"
   ```

   Describe all users that are part of a specific group\.

   ```
   $ ldapsearch "(&(objectClass=user)(memberOf=CN=clusterteam,OU=Users,OU=CORP,DC=corp,DC=pcluster,DC=com))" -x -h "192.0.2.254" -b "DC=corp,DC=pcluster,DC=com" -D "CN=Admin,OU=Users,OU=CORP,DC=corp,DC=pcluster,DC=com" -w "p@ssw0rd"
   ```

   Describe all groups

   ```
   $ ldapsearch "objectClass=group" -x -h "192.0.2.254" -b "DC=corp,DC=pcluster,DC=com" -D "CN=Admin,OU=Users,OU=CORP,DC=corp,DC=pcluster,DC=com" -w "p@ssw0rd"
   ```

   Describe a specific group

   ```
   $ ldapsearch "(&(objectClass=group)(cn=clusterteam))" -x -h "192.0.2.254" -b "DC=corp,DC=pcluster,DC=com" -D "CN=Admin,OU=Users,OU=CORP,DC=corp,DC=pcluster,DC=com" -w "p@ssw0rd"
   ```

1. 

**Remove a user from a group\.**

   ```
   $ adcli remove-member "clusterteam" "clusteruser" --domain "corp.pcluster.com" -U "Admin"
   ```

1. 

**Delete a user**

   ```
   $ adcli delete-user "clusteruser" --domain "corp.pcluster.com" -U "Admin"
   ```

1. 

**Delete a group\.**

   ```
   $ adcli delete-group "clusterteam" --domain "corp.pcluster.com" -U "Admin"
   ```