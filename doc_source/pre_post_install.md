# Custom Bootstrap Actions<a name="pre_post_install"></a>

AWS ParallelCluster can run arbitrary code either before \(pre\-install\) or after \(post\-install\) the main bootstrap action when the cluster is created\. In most cases, this code is stored in Amazon Simple Storage Service \(Amazon S3\) and accessed through an HTTPS connection\. The code is run as root and can be in any script language that is supported by the cluster OS\. Often the code is in *bash* or *python*\.

Pre\-install actions are called before any cluster deployment bootstrap action is started, such as configuring NAT, Amazon Elastic Block Store \(Amazon EBS\) or the scheduler\. Some pre\-install actions include modifying storage, adding extra users, and adding packages\.

Post\-install actions are called after the cluster bootstrap processes are complete\. Post\-install actions serve the last actions to occur before an instance is considered fully configured and complete\. Some post\-install actions include changing scheduler settings, modifying storage, and modifying packages\.

You can pass argument to scripts by specifying them during configuration\. For this, you pass them double\-quoted to the pre\-install or post\-install actions\.

If a pre\-install or post\-install action fails, the instance bootstrap also fails\. Success is signaled with an exit code of zero \(0\)\. Any other exit code indicates the instance bootstrap failed\.

You can differentiate between running head and compute nodes\. Source the `/etc/parallelcluster/cfnconfig` file and evaluate the `cfn_node_type` environment variable that have a value of "`MasterServer`" and "`ComputeFleet`" for the head and compute nodes, respectively\.

```
#!/bin/bash

. "/etc/parallelcluster/cfnconfig"

case "${cfn_node_type}" in
    MasterServer)
        echo "I am the head node" >> /tmp/head.txt
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
   
   echo "post-install script has $# arguments"
   for arg in "$@"
   do
       echo "arg: ${arg}"
   done
   
   yum -y install "${@:2}"
   ```

1. Upload the script with the correct permissions to Amazon S3\. If public read permissions aren't appropriate for you, use either [`s3_read_resource`](cluster-definition.md#s3-read-resource) or [`s3_read_write_resource`](cluster-definition.md#s3-read-write-resource) parameters to grant access\. For more information, see [Working with Amazon S3](s3_resources.md)\.

   ```
   $ aws s3 cp --acl public-read /path/to/myscript.sh s3://<bucket-name>/myscript.sh
   ```
**Important**  
If the script was edited on Windows, line endings must be changed from CRLF to LF before the script is uploaded to Amazon S3\.

1. Update the AWS ParallelCluster configuration to include the new post\-install action\.

   ```
   [cluster default]
   ...
   post_install = https://<bucket-name>.s3.amazonaws.com/myscript.sh
   post_install_args = 'R curl wget'
   ```

   If the bucket doesn't have public\-read permission, use `s3` as the URL protocol\.

   ```
   [cluster default]
   ...
   post_install = s3://<bucket-name>/myscript.sh
   post_install_args = 'R curl wget'
   ```

1. Launch the cluster\.

   ```
   $ pcluster create mycluster
   ```

1. Verify the output\.

   ```
   $ less /var/log/cfn-init.log
   2019-04-11 10:43:54,588 [DEBUG] Command runpostinstall output: post-install script has 4 arguments
   arg: s3://<bucket-name>/test.sh
   arg: R
   arg: curl
   arg: wget
   Loaded plugins: dkms-build-requires, priorities, update-motd, upgrade-helper
   Package R-3.4.1-1.52.amzn1.x86_64 already installed and latest version
   Package curl-7.61.1-7.91.amzn1.x86_64 already installed and latest version
   Package wget-1.18-4.29.amzn1.x86_64 already installed and latest version
   Nothing to do
   ```