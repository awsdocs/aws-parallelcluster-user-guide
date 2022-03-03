# `awsbkill`<a name="awsbatchcli.awsbkill-v3"></a>

Cancels or terminates jobs submitted in the cluster\.

```
awsbkill [ - h ] [ - c CLUSTER ] [ - r REASON ] job_ids [ job_ids ... ]
```

## Positional Arguments<a name="awsbatchcli.awsbkill-v3.arguments"></a>

`job_ids`  
Specifies the space\-separated list of job IDs to cancel or terminate\.

## Named Arguments<a name="awsbatchcli.awsbkill-v3.namedarguments"></a>

`-c CLUSTER, --cluster CLUSTER`  
Indicates the name of the cluster to use\.

`-r REASON, --reason REASON`  
Indicates the message to attach to a job, explaining the reason for canceling it\.  
Default: “Terminated by the user”