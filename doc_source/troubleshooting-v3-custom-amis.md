# Troubleshooting custom AMI issues<a name="troubleshooting-v3-custom-amis"></a>

When you use a custom AMI, you can see the following warnings:

```
"validationMessages": [
  {
    "level": "WARNING",
    "type": "CustomAmiTagValidator",
    "message": "The custom AMI may not have been created by pcluster. You can ignore this warning if the AMI is shared or copied from another pcluster AMI. If the AMI is indeed not created by pcluster, cluster creation will fail. If the cluster creation fails, please go to https://docs.aws.amazon.com/parallelcluster/latest/ug/troubleshooting.html#troubleshooting-stack-creation-failures for troubleshooting."
  },
  {
   "level": "WARNING",
   "type": "AmiOsCompatibleValidator",
   "message": "Could not check node AMI ami-0000012345 OS and cluster OS alinux2 compatibility, please make sure they are compatible before cluster creation and update operations."
  }
]
```

If you're sure that the correct AMI is being used, you can ignore these warnings\.

If you don't want to see these warnings in the future, tag the custom AMI with the following tags, where *`my-os`* is one of `alinux2`, `ubuntu1804`, `ubuntu2004`, or `centos7` and *"3\.5\.0"* is the `pcluster` version in use:

```
$ aws ec2 create-tags \
  --resources ami-yourcustomAmi \
  --tags Key="parallelcluster:version",Value="3.5.0" Key="parallelcluster:os",Value="my-os"
```