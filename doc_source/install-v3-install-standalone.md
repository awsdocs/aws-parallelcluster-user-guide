# Install AWS ParallelCluster as a standalone application<a name="install-v3-install-standalone"></a>

Install AWS ParallelCluster as a standalone application on your environment\. Follow the instructions for installing AWS ParallelCluster on an available OS in the following section\.

**Prerequisites**
+ An environment with an operating system compatible with an available version of the installer\.

## Linux<a name="collapsible-linux"></a>

------
#### [ Linux x86 \(64\-bit\) ]

**Install AWS ParallelCluster on your environment\.**

1. Download the latest [pcluster installer](https://us-east-1-aws-parallelcluster.s3.us-east-1.amazonaws.com/parallelcluster/3.5.1/installer/pcluster-installer-bundle-3.5.1.36-node-v16.19.0-Linux_x86_64-signed.zip)\.

1. Unzip the installer bundle and install AWS ParallelCluster by using the following commands:

   ```
   $ unzip pcluster-installer-bundle-3.5.1.36-node-v16.19.0-Linux_x86_64-signed.zip
   $ cd pcluster-installer-bundle-3.5.1.36-node-v16.19.0-Linux_x86_64-signed
   $ chmod +x install_pcluster.sh
   ```

1. Run the following install script\.

   ```
   $ bash install-pcluster.sh
   ```

1. Verify that AWS ParallelCluster is installed correctly\.

   ```
   $ pcluster version
   {
       "version": "3.5.1"
   }
   ```

**Troubleshooting `pcluster` installation errors**
+ If the AWS ParallelCluster version isn't returned in step 4, restart the terminal or `source` the `bash_profile` to update the `PATH` variable to include the new binary directory as shown in the following example:

  ```
  $ source ~/.bash_profile
  ```
+ If you use your `pcluster` installation to create clusters with `CustomActions` specified as HTTPS resources, rather than S3 URIs, you might see a `WARNING` message indicating that these resources might not be verified \(\[`SSL: CERTIFICATE_VERIFY_FAILED`\]\)\. This is caused by a known issue and you can ignore this warning if you trust the authenticity of the specified resources\.

------

**Previous installer bundle versions**
+ None