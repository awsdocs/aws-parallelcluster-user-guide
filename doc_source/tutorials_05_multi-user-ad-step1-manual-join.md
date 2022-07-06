# Join your instance to the AD<a name="tutorials_05_multi-user-ad-step1-manual-join"></a>

1. 

**Connect to your instance and join the AD realm as `admin`**

   Run the following commands to connect to the instance:

   ```
   $ INSTANCE_ID="i-1234567890abcdef0"
   ```

   ```
   $ PUBLIC_IP=$(aws ec2 describe-instances \
   --instance-ids $INSTANCE_ID \
   --query "Reservations[0].Instances[0].PublicIpAddress" \
   --output text)
   ```

   ```
   $ ssh -i ~/.ssh/keys/keypair.pem ec2-user@$PUBLIC_IP
   ```

1. 

**Install necessary software and join the realm\.**

   ```
   $ sudo yum -y install sssd realmd oddjob oddjob-mkhomedir adcli samba-common samba-common-tools krb5-workstation openldap-clients policycoreutils-python
   ```

1. 

**Replace the Admin password with your `admin` password:**

   ```
   $ ADMIN_PW="asdfASDF1234"
   ```

   ```
   $ echo $ADMIN_PW | sudo realm join -U Admin corp.pcluster.com
   Password for Admin:
   ```

   If the above has succeeded, you are joined to the realm and can proceed to the next step\.