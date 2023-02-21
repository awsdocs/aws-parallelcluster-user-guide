# Trying to run a job<a name="troubleshooting-fc-v3-run-job"></a>

## Job is stuck in `CF` state with `squeue` command<a name="run-job-cf-stuck-v3"></a>

This might be an issue with dynamic nodes powering up\. For more information, see [Seeing errors in compute node initializations](troubleshooting-fc-v3-compute-node-initialization-v3.md)\.

## Running large scale jobs and seeing `nfsd: too many open connections, consider increasing the number of threads in /var/log/messages`<a name="run-job-network-limits-v3"></a>

With a networked file system, when network limits are reached, I/O wait time also increases\. This can result in soft lockups because the network is used to write data for both networking and I/O metrics\.

With 5th generation instances, we use the ENA driver to expose packet counters\. These counters count the packets shaped by AWS when the network reaches instance bandwidth limits\. You can check these counters to see if they are greater than 0\. If they are, then you have exceeded your bandwidth limits\. You can view these counters by running `ethtool -S eth0 | grep exceeded`\.

Exceeding network limits is often a result of supporting too many NFS connections\. This is one of the first things to check when you reach or exceed network limits\.

For example, the following output shows dropped packages:

```
$ ethtool -S eth0 | grep exceeded
  bw_in_allowance_exceeded: 38750610
  bw_out_allowance_exceeded: 1165693
  pps_allowance_exceeded: 103
  conntrack_allowance_exceeded: 0
  linklocal_allowance_exceeded: 0
```

To avoid getting this message, consider changing the head node instance type to a more performant instance type\. Consider moving your data storage to shared storage file systems that aren't exported as an NFS share, such a Amazon EFS or Amazon FSx\. For more information, see [Shared storage](shared-storage-quotas-integration-v3.md) and the [Best Practices](https://github.com/aws/aws-parallelcluster/wiki/Best-Practices) at the AWS ParallelCluster Wiki on GitHub\.

## Running an MPI job<a name="run-job-mpi-v3"></a>

### Enabling debug mode<a name="run-job-mpi-enable-v3"></a>

To enable OpenMPI debug mode, see [What controls does Open MPI have that aid in debugging](https://www-lb.open-mpi.org/faq/?category=debugging#debug-ompi-controls)\.

To enable IntelMPI debug mode, see [Other Environment Variables](https://www.intel.com/content/www/us/en/develop/documentation/mpi-developer-reference-linux/top/environment-variable-reference/other-environment-variables.html)\.

### Seeing `MPI_ERRORS_ARE_FATAL` and `OPAL ERROR` in the job output<a name="run-job-mpi-errors-v3"></a>

These error codes come from the MPI layer in your application\. To learn how to get MPI debug logs from your application, see [Enabling debug mode](#run-job-mpi-enable-v3)\.

A possible cause for this error is that your application has been compiled for a specific MPI implementation, such as OpenMPI, and you are trying to run it with a different MPI implementation, such as IntelMPI\. Make sure you are both compiling and running your application with the same MPI implementation\.

### Using `mpirun` with managed DNS disabled<a name="run-job-mpi-dns-disabled-v3"></a>

For clusters created with [SlurmSettings](Scheduling-v3.md#Scheduling-v3-SlurmSettings) / [Dns](Scheduling-v3.md#Scheduling-v3-SlurmSettings-Dns) / [DisableManagedDns](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-Dns-DisableManagedDns) and [UseEc2Hostnames](Scheduling-v3.md#yaml-Scheduling-SlurmSettings-Dns-UseEc2Hostnames) set to `true`, the Slurm node name isn't resolved by the DNS\. Slurm can bootstrap MPI processes when `nodenames` aren't enabled and if the MPI job is run in a Slurm context\. We recommend following the guidance in the [Slurm MPI User's Guide](https://slurm.schedmd.com/mpi_guide.html) to run MPI jobs with Slurm\.