# Network troubleshooting<a name="troubleshooting-v3-networking"></a>

## Cluster in a single public subnet issues<a name="troubleshooting-v3-networking-private-subnet"></a>

Check the `cloud-init-output.log` from one of the compute nodes\. If you find something like the following that indicates the node is stuck in Slurm initialization, it is most likely due to a missing DynamoDB VPC endpoint\. Add the DynamoDB endpoint\. For more information see [AWS ParallelCluster in a single subnet with no internet access](network-configuration-v3.md#aws-parallelcluster-in-a-single-public-subnet-no-internet-v3)\.

```
ruby_block[retrieve compute node info] action run[2022-03-11T17:47:11+00:00] INFO: Processing ruby_block[retrieve compute node info] action run (aws-parallelcluster-slurm::init line 31)
```