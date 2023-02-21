# Multiple instance type allocation with Slurm<a name="slurm-multiple-instance-allocation-v3"></a>

Starting with AWS ParallelCluster version 3\.3\.0, you can configure your cluster to allocate from a compute resource's set of defined instance types\. Allocation can be based on EC2 fleet low cost or optimal capacity strategies\.

This set of defined instance types must either all have the same number of vCPUs or, if multithreading is disabled, the same number of cores\. Moreover, this set of instance types must have the same number of accelerators of the same manufacturers\. If [`Efa`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-ComputeResources-Efa) / [`Enabled`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-ComputeResources-Efa-Enabled) is set to `true`, the instances must have EFA supported\. For more information and requirements, see [`Scheduling`](Scheduling-v3.md) / [`SlurmQueues`](Scheduling-v3.md#Scheduling-v3-SlurmQueues) / [`AllocationStrategy`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-AllocationStrategy) and [`ComputeResources`](Scheduling-v3.md#Scheduling-v3-SlurmQueues-ComputeResources) / [`Instances`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-ComputeResources-Instances)\.

You can set [`AllocationStrategy`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-AllocationStrategy) to `lowest-price` or `capacity-optimized` depending on your [CapacityType](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-CapacityType) configuration\.

In [`Instances`](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-ComputeResources-Instances), you can configure a set of instance types\.

**Note**  
`EnableMemoryBasedScheduling` can't be enabled if you configure multiple instance types in [Instances](Scheduling-v3.md#yaml-Scheduling-SlurmQueues-ComputeResources-Instances)\.

The following examples show how you can query instance types for vCPUs, EFA support, and architecture\.

Query InstanceTypes with 96 vCPUs and x86\_64 architecture\.

```
$ aws ec2 describe-instance-types --region region-id \
  --filters "Name=vcpu-info.default-vcpus,Values=96" "Name=processor-info.supported-architecture,Values=x86_64" \
  --query "sort_by(InstanceTypes[*].{InstanceType:InstanceType,MemoryMiB:MemoryInfo.SizeInMiB,CurrentGeneration:CurrentGeneration,VCpus:VCpuInfo.DefaultVCpus,Cores:VCpuInfo.DefaultCores,Architecture:ProcessorInfo.SupportedArchitectures[0],MaxNetworkCards:NetworkInfo.MaximumNetworkCards,EfaSupported:NetworkInfo.EfaSupported,GpuCount:GpuInfo.Gpus[0].Count,GpuManufacturer:GpuInfo.Gpus[0].Manufacturer}, &InstanceType)" \
  --output table
```

Query InstanceTypes with 64 cores, EFA support, and arm64 architecture\.

```
$ aws ec2 describe-instance-types --region region-id \
  --filters "Name=vcpu-info.default-cores,Values=64" "Name=processor-info.supported-architecture,Values=arm64" "Name=network-info.efa-supported,Values=true" --query "sort_by(InstanceTypes[*].{InstanceType:InstanceType,MemoryMiB:MemoryInfo.SizeInMiB,CurrentGeneration:CurrentGeneration,VCpus:VCpuInfo.DefaultVCpus,Cores:VCpuInfo.DefaultCores,Architecture:ProcessorInfo.SupportedArchitectures[0],MaxNetworkCards:NetworkInfo.MaximumNetworkCards,EfaSupported:NetworkInfo.EfaSupported,GpuCount:GpuInfo.Gpus[0].Count,GpuManufacturer:GpuInfo.Gpus[0].Manufacturer}, &InstanceType)" \
  --output table
```

The next example cluster configuration snippet shows how you can use these InstanceType and AllocationStrategy properties\.

```
...
 Scheduling:
  Scheduler: slurm
  SlurmQueues:
    - Name: queue-1
      CapacityType: ONDEMAND
      AllocationStrategy: lowest-price
      ...
      ComputeResources:
        - Name: computeresource1
          Instances:
            - InstanceType: r6g.2xlarge
            - InstanceType: m6g.2xlarge
            - InstanceType: c6g.2xlarge
          MinCount: 0
          MaxCount: 500
        - Name: computeresource2
          Instances:
            - InstanceType: m6g.12xlarge
            - InstanceType: x2gd.12xlarge
          MinCount: 0
          MaxCount: 500
...
```