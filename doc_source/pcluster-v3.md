# `pcluster`<a name="pcluster-v3"></a>

`pcluster` is the primary AWS ParallelCluster CLI command\. You use `pcluster` to launch and manage HPC clusters in the AWS Cloud\.

`pcluster` writes logs of your commands to `pcluster.log.#` files in `/home/user/.parallelcluster/`\. For more information, see [Use pcluster CLI logs for troubleshooting](troubleshooting-v3-pc-cli-logs.md)\.

To use `pcluster`, you must have an IAM role with the [permissions](iam-roles-in-parallelcluster-v3.md#iam-roles-in-parallelcluster-v3-example-user-policies) required to run it\.

```
pcluster [-h]
```

## Arguments<a name="pcluster.arguments-v3"></a>

**`pcluster command`**  
Possible choices: [`build-image`](pcluster.build-image-v3.md) [`configure`](pcluster.configure-v3.md) [`create-cluster`](pcluster.create-cluster-v3.md) [`dcv-connect`](pcluster.dcv-connect-v3.md) [`delete-cluster`](pcluster.delete-cluster-v3.md) [`delete-cluster-instances`](pcluster.delete-cluster-instances-v3.md) [`delete-image`](pcluster.delete-image-v3.md) [`describe-cluster`](pcluster.describe-cluster-v3.md) [`describe-cluster-instances`](pcluster.describe-cluster-instances-v3.md) [`describe-compute-fleet`](pcluster.describe-compute-fleet-v3.md) [`describe-image`](pcluster.describe-image-v3.md) [`export-cluster-logs`](pcluster.export-cluster-logs-v3.md) [`export-image-logs`](pcluster.export-image-logs-v3.md) [`get-cluster-log-events`](pcluster.get-cluster-log-events-v3.md) [`get-cluster-stack-events`](pcluster.get-cluster-stack-events-v3.md) [`get-image-log-events`](pcluster.get-image-log-events-v3.md) [`get-image-stack-events`](pcluster.get-image-stack-events-v3.md) [`list-clusters`](pcluster.list-clusters-v3.md) [`list-cluster-log-streams`](pcluster.list-cluster-log-streams-v3.md) [`list-images`](pcluster.list-images-v3.md) [`list-image-log-streams`](pcluster.list-image-log-streams-v3.md) [`list-official-images`](pcluster.list-official-images-v3.md) [`ssh`](pcluster.ssh-v3.md) [`update-cluster`](pcluster.update-cluster-v3.md) [`update-compute-fleet`](pcluster.update-compute-fleet-v3.md) [`version`](pcluster.version-v3.md)

## Sub\-commands:<a name="pcluster.subcommands-v3"></a>

**Topics**
+ [Arguments](#pcluster.arguments-v3)
+ [Sub\-commands:](#pcluster.subcommands-v3)
+ [`pcluster build-image`](pcluster.build-image-v3.md)
+ [`pcluster configure`](pcluster.configure-v3.md)
+ [`pcluster create-cluster`](pcluster.create-cluster-v3.md)
+ [`pcluster dcv-connect`](pcluster.dcv-connect-v3.md)
+ [`pcluster delete-cluster`](pcluster.delete-cluster-v3.md)
+ [`pcluster delete-cluster-instances`](pcluster.delete-cluster-instances-v3.md)
+ [`pcluster delete-image`](pcluster.delete-image-v3.md)
+ [`pcluster describe-cluster`](pcluster.describe-cluster-v3.md)
+ [`pcluster describe-cluster-instances`](pcluster.describe-cluster-instances-v3.md)
+ [`pcluster describe-compute-fleet`](pcluster.describe-compute-fleet-v3.md)
+ [`pcluster describe-image`](pcluster.describe-image-v3.md)
+ [`pcluster export-cluster-logs`](pcluster.export-cluster-logs-v3.md)
+ [`pcluster export-image-logs`](pcluster.export-image-logs-v3.md)
+ [`pcluster get-cluster-log-events`](pcluster.get-cluster-log-events-v3.md)
+ [`pcluster get-cluster-stack-events`](pcluster.get-cluster-stack-events-v3.md)
+ [`pcluster get-image-log-events`](pcluster.get-image-log-events-v3.md)
+ [`pcluster get-image-stack-events`](pcluster.get-image-stack-events-v3.md)
+ [`pcluster list-clusters`](pcluster.list-clusters-v3.md)
+ [`pcluster list-cluster-log-streams`](pcluster.list-cluster-log-streams-v3.md)
+ [`pcluster list-images`](pcluster.list-images-v3.md)
+ [`pcluster list-image-log-streams`](pcluster.list-image-log-streams-v3.md)
+ [`pcluster list-official-images`](pcluster.list-official-images-v3.md)
+ [`pcluster ssh`](pcluster.ssh-v3.md)
+ [`pcluster update-cluster`](pcluster.update-cluster-v3.md)
+ [`pcluster update-compute-fleet`](pcluster.update-compute-fleet-v3.md)
+ [`pcluster version`](pcluster.version-v3.md)