# Enable Intel MPI<a name="intelmpi"></a>

Intel MPI is available on the AWS ParallelCluster AMIs for `alinux`, `alinux2`, `centos7`, `ubuntu1604`, and `ubuntu1804` values for the [`base_os`](cluster-definition.md#base-os) setting\. Using Intel MPI indicates that you accept the [Intel Simplified Software License](https://software.intel.com/en-us/license/intel-simplified-software-license)\. Open MPI is placed on the path by default\. To enable Intel MPI instead of Open MPI, the Intel MPI module must be loaded first\. Install the latest using `module load intelmpi`\. The exact name of the module changes with every update\. To see which modules are available, run `module avail`\.

```
$ module avail

----------------------------------------- /usr/share/Modules/modulefiles ------------------------------------------
dot                        libfabric-aws/1.8.1amzn1.3 module-info                null                       use.own
module-git                 modules                    openmpi/4.0.2

------------------------------------------------ /etc/modulefiles -------------------------------------------------

--------------------------------- /opt/intel/impi/2019.7.217/intel64/modulefiles ----------------------------------
intelmpi
```

To load a module, run `module load modulename`\. You can add this to the script used to run `mpirun`\.

```
$ module load intelmpi
```

To see which modules are loaded, run `module list`\.

```
$ module list
Currently Loaded Modulefiles:
  1) intelmpi
```

To verify that Intel MPI is enabled, run `mpirun --version`\.

```
$ mpirun --version
Intel(R) MPI Library for Linux* OS, Version 2019 Update 7 Build 20200312 (id: 5dc2dd3e9)
Copyright 2003-2020, Intel Corporation.
```

After the Intel MPI module has been loaded, multiple paths are changed to use the Intel MPI tools\. To run code that was compiled by the Intel MPI tools, load the Intel MPI module first\.

**Note**  
Intel MPI is not compatible with AWS Graviton\-based instances\.

**Note**  
Prior to AWS ParallelCluster 2\.5\.0, Intel MPI is not available on the AWS ParallelCluster AMIs in China \(Beijing\) and China \(Ningxia\)\.