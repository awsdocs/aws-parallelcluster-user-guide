# Arm Performance Libraries<a name="arm-performance-libraries"></a>

Starting with AWS ParallelCluster version 2\.10\.1, Arm Performance Libraries are available on the AWS ParallelCluster AMIs for `alinux2`, `centos8`, `ubuntu1804`, and `ubuntu2004` values for the [`base_os`](cluster-definition.md#base-os) setting\. The Arm Performance Libraries provides optimized standard core math libraries for high\-performance computing applications on Arm processors\. To use Arm Performance Libraries, you must acknowledge and accept the terms of the [Arm Performance Libraries \(free version\) \- End User License Agreement](https://developer.arm.com/tools-and-software/server-and-hpc/downloads/arm-performance-libraries/eula)\. For more information on Arm Performance Libraries, see [Free Arm Performance Libraries](https://developer.arm.com/tools-and-software/server-and-hpc/downloads/arm-performance-libraries)\.

To enable Arm Performance Libraries, you must first load the Arm Performance Libraries module\. `Armpl-21.0.0` needs GCC\-9\.3 as a requirement, when you load the `armpl/21.0.0` module, the `gcc/9.3` module will also be loaded\. The exact name of the module changes with every update\. To see which modules are available, run `module avail`\. Then, you need to install the latest version by using `module load armpl`\.The output is as follows\.

```
$ module avail

------------------------------- /usr/share/Modules/modulefiles --------------------------------
armpl/21.0.0         dot                  libfabric-aws/1.11.1amzn1.0               module-git
module-info          modules              null                 openmpi/4.1.0        use.own
```

To load a module, run `module load modulename`\. You can add this to the script used to run `mpirun`\.

```
$ module load armpl

Use of the free of charge version of Arm Performance Libraries is subject to the terms and
conditions of the Arm Performance Libraries (free version) -  End User License Agreement
(EULA). A copy of the EULA can be found in the
'/opt/arm/armpl/21.0.0/arm-performance-libraries_21.0_gcc-9.3/license_terms' folder
```

To see which modules are loaded, run `module list`\.

```
$ module list
Currently Loaded Modulefiles:
1) /opt/arm/armpl/21.0.0/modulefiles/armpl/gcc-9.3
2) /opt/arm/armpl/21.0.0/modulefiles/armpl/21.0.0_gcc-9.3
3) armpl/21.0.0
```

To verify that Arm Performance Libraries are enabled, run example tests\.

```
$ sudo chmod 777 /opt/arm/armpl/21.0.0/armpl_21.0_gcc-9.3/examples
$ cd /opt/arm/armpl/21.0.0/armpl_21.0_gcc-9.3/examples
$ make
...
Testing: no example difference files were generated.
Test passed OK
```

After the Arm Performance Libraries module has been loaded, multiple paths are changed to use the Arm Performance Libraries tools\. To run code that was compiled by the Arm Performance Libraries tools, load the Arm Performance Libraries module first\.

**Note**  
AWS ParallelCluster versions between 2\.10\.1 and 2\.10\.4 use `armpl/20.2.1`\.