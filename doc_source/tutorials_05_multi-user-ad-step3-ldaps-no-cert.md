# LDAPS without certificate verification configuration<a name="tutorials_05_multi-user-ad-step3-ldaps-no-cert"></a>

**Note**  
`KeyName`: One of your EC2 keypairs\.
`SubnetId / SubnetIds`: One of the subnet IDs provided in the output of the CloudFormation quick create stack \(automated tutorial\) or python script \(manual tutorial\)\.
`Region`: The Region where you created the AD infrastructure\.
`DomainAddr`: This IP address is one of the DNS addresses of your AD service\.
`PasswordSecretArn`: The ARN of the secret that contains the password for the `DomainReadOnlyUser`\.

```
Region: region-id
Image:
  Os: alinux2
HeadNode: 
  InstanceType: t2.micro
  Networking:
    SubnetId: subnet-abcdef01234567890
  Ssh:
    KeyName: keypair
Scheduling:
  Scheduler: slurm
  SlurmQueues:
    - Name: queue0
      ComputeResources:
        - Name: queue0-t2-micro
          InstanceType: t2.micro
          MinCount: 1
          MaxCount: 10         
      Networking:
        SubnetIds:
          - subnet-abcdef01234567890
DirectoryService:
  DomainName: corp.pcluster.com
  DomainAddr: ldaps://corp.pcluster.com
  PasswordSecretArn: arn:aws:secretsmanager:region-id:123456789012:secret:ADSecretPassword-1234
  DomainReadOnlyUser: cn=ReadOnlyUser,ou=Users,ou=CORP,dc=corp,dc=pcluster,dc=com
  LdapTlsReqCert: never
```