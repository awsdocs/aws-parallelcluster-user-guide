# Example<a name="examples"></a>

The following example launches a cluster with the `awsbatch` scheduler\. It is set to pick the optimal instance type, based on your job resource needs\.

The example configuration allows a maximum of 40 concurrent vCPUs, and scales down to zero when no jobs have run for 10 minutes\.

```
[global]
update_check = true
sanity_check = true
cluster_template = awsbatch

[aws]
aws_region_name = [your_aws_region]

[cluster awsbatch]
scheduler = awsbatch
compute_instance_type = optimal # optional, defaults to optimal
min_vcpus = 0                   # optional, defaults to 0
desired_vcpus = 0               # optional, defaults to 4
max_vcpus = 40                  # optional, defaults to 20
base_os = alinux                # optional, defaults to alinux, controls the base_os of the master instance and the docker image for the compute fleet
key_name = [your_ec2_keypair]
vpc_settings = public

[vpc public]
master_subnet_id = [your_subnet]
vpc_id = [your_vpc]
```