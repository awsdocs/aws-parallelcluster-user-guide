# Directories that cannot be replaced<a name="troubleshooting-v3-dirs-must-keep"></a>

The following directories are shared between the nodes and cannot be replaced\.
+  `/home` \- This includes the default user home folder \(`/home/ec2_user` on Amazon Linux and RedHat, `/home/centos` on CentOS, and `/home/ubuntu` on Ubuntu\)\.
+  `/opt/intel` \- This includes Intel MPI, Intel Parallel Studio, and related files\.
+  `/opt/slurm` \- This includes Slurm Workload Manager and related files\. \(Conditional, only if `Scheduler: slurm`\.\) 