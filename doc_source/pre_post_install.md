# Custom Bootstrap Actions<a name="pre_post_install"></a>

AWS ParallelCluster can execute arbitrary code either before \(pre\-install\) or after \(post\-install\) the main bootstrap action during cluster creation\. This code is typically stored in Amazon Simple Storage Service \(Amazon S3\) and accessed via HTTPS during cluster creation\. The code is executed as root and can be in any script language that is supported by the cluster OS, typically *bash* or *python*\.

Pre\-install actions are called before any cluster deployment bootstrap, such as configuring NAT, Amazon Elastic Block Store \(Amazon EBS\) or the scheduler\. Typical pre\-install actions can include modifying storage, adding extra users, or adding packages\.

Post\-install actions are called after cluster bootstrap is complete, as the last action before an instance is considered complete\. Typical post\-install actions can include changing scheduler settings, modifying storage, or modifying packages\.

Arguments can be passed to scripts by specifying them in the configuration\. These are passed double\-quoted to the pre\-install or post\-install actions\.

If a pre\-install or post\-install action fails, the instance bootstrap fails\. Success is signaled with an exit code of 0\. Any other exit code is considered a failure\.

It is possible to differentiate between master and compute nodes execution by sourcing the `/etc/parallelcluster/cfnconfig` file and evaluating the `cfn_node_type` environment variable, whose possible values are "MasterServer" and "ComputeFleet" for the master and compute nodes respectively\.

```
#!/bin/bash

. "/etc/parallelcluster/cfnconfig"

case "${cfn_node_type}" in
    MasterServer)
        echo "I am the master node" >> /tmp/master.txt
    ;;
    ComputeFleet)
        echo "I am a compute node" >> /tmp/compute.txt
    ;;
    *)
    ;;
esac
```

## Configuration<a name="configuration"></a>

The following configuration settings are used to define pre\-install and post\-install actions and arguments\.

```
# URL to a preinstall script. This is executed before any of the boot_as_* scripts are run
# (defaults to NONE)
pre_install = NONE
# Arguments to be passed to preinstall script
# (defaults to NONE)
pre_install_args = NONE
# URL to a postinstall script. This is executed after any of the boot_as_* scripts are run
# (defaults to NONE)
post_install = NONE
# Arguments to be passed to postinstall script
# (defaults to NONE)
post_install_args = NONE
```

## Arguments<a name="arguments"></a>

The first two arguments — `$0` and `$1` — are reserved for the script name and url\.

```
$0 => the script name
$1 => s3 url
$n => args set by pre/post_install_args
```

## Example<a name="example"></a>

The following steps create a simple post\-install script that installs the R packages in a cluster\.

1. Create a script\.

   ```
   #!/bin/bash
   
   yum -y install --enablerepo=epel R
   ```

1. Upload the script with the correct permissions to Amazon S3\.

   ```
   aws s3 cp --acl public-read /path/to/myscript.sh s3://<bucket-name>/myscript.sh
   ```

1. Update the AWS ParallelCluster configuration to include the new post\-install action\.

   ```
   [cluster default]
   ...
   post_install = https://<bucket-name>.s3.amazonaws.com/myscript.sh
   ```

   If the bucket does not have public\-read permission, use `s3` as the URL protocol\.

   ```
   [cluster default]
   ...
   post_install = s3://<bucket-name>/myscript.sh
   ```

1. Launch the cluster\.

   ```
   pcluster create mycluster
   ```