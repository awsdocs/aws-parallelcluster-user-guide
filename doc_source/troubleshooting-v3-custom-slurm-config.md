# Seeing errors with custom Slurm configuration<a name="troubleshooting-v3-custom-slurm-config"></a>

Starting in AWS ParallelCluster version 3\.6\.0, you can no longer target single `prolog` or `epilog` scripts by including them in a custom Slurm configuration\. In AWS ParallelCluster version 3\.6\.0 and later versions, you must locate custom `prolog` and `epilog` scripts in the respective `Prolog` and `Epilog` folders\. These folders are configured by default to point to:
+ `Prolog` points to `/opt/slurm/etc/scripts/prolog.d/`\.
+ `Epilog` points to `/opt/slurm/etc/scripts/epilog.d/`\.

We recommend that you keep the `90_plcuster_health_check_manager` prolog script and the `90_pcluster_noop` epilog script in place\.

Slurm runs the scripts in reverse alphabetical order\. Both the `Prolog` and `Epilog` folder must contain at least one file\. For more information, see [Slurm `prolog` and `epilog`](slurm-prolog-epilog-v3.md) and [Slurm configuration customization](slurm-configuration-settings-v3.md)\.