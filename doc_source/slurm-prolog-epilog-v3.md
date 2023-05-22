# Slurm `prolog` and `epilog`<a name="slurm-prolog-epilog-v3"></a>

Starting with AWS ParallelCluster version 3\.6\.0, the Slurm configuration that's deployed with AWS ParallelCluster includes the `Prolog` and `Epilog` configuration parameters:

```
# PROLOG AND EPILOG
Prolog=/opt/slurm/etc/scripts/prolog.d/*
Epilog=/opt/slurm/etc/scripts/epilog.d/*
SchedulerParameters=nohold_on_prolog_fail
BatchStartTimeout=180
```

For more information, see the [Prolog and Epilog Guide](https://slurm.schedmd.com/prolog_epilog.html) in the Slurm documentation\.

AWS ParallelCluster includes the following prolog and epilog scripts:
+ `90_plcuster_health_check_manager` \(in the `Prolog` folder\)
+ `90_pcluster_noop` \(in the `Epilog` folder\)

**Note**  
Both the `Prolog` and `Epilog` folder must contain at least one file\.

You can use your own custom `prolog` or `epilog` scripts by adding them to the corresponding `Prolog` and `Epilog` folders\.

**Warning**  
Slurm runs every script in the folders, in reverse alphabetical order\.

The run time duration of the `prolog` and `epilog` scripts impact the time needed to run a job\. Update the `BatchStartTimeout` configuration setting when running multiple or long running `prolog` scripts\. The default is 3 minutes\.

If you are using custom `prolog` and `epilog` scripts, locate the scripts in the respective `Prolog` and `Epilog` folders\. We recommend that you keep the `90_plcuster_health_check_manager` script that runs before every custom script\. For more information, see [Slurm configuration customization](slurm-configuration-settings-v3.md)\.