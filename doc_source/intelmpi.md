# Enable Intel MPI<a name="intelmpi"></a>

Intel MPI is available on the AWS ParallelCluster AMIs \(except in China \(Beijing\) and China \(Ningxia\)\) for `alinux`, `centos7`, and `ubuntu1604` [`base_os`](cluster-definition.md#base-os)\. Open MPI is placed on the path by default\. To enable Intel MPI instead of Open MPI, the Intel MPI module must be loaded\. The exact name of the module changes with every update\. To see which modules are available, run `module avail`\.

```
$ module avail

----------------------------------------------- /usr/share/Modules/modulefiles ------------------------------------------------
dot                 module-git          modules             use.own        openmpi/3.1.4
intelmpi/2019.4.243 module-info         null
```

To load a module, run `module load modulename`\.

```
$ module load intelmpi
```

To see what modules are loaded, run `module list`\.

```
$ module list
Currently Loaded Modulefiles:
  1) intelmpi/2019.4.243
```

To verify that Intel MPI is enabled, run `mpirun --version`\.

```
$ mpirun --version
Intel(R) MPI Library for Linux* OS, Version 2019 Update 4 Build 20190430 (id: cbdd16069)
Copyright 2003-2019, Intel Corporation.
```

After the Intel MPI module has been loaded, multiple paths are changed to use the Intel MPI tools\. To run code that was compiled by the Intel MPI tools, the Intel MPI module must be loaded first\.