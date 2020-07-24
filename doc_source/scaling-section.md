# `[scaling]` Section<a name="scaling-section"></a>

**Topics**
+ [`scaledown_idletime`](#scaledown-idletime)

Specifies settings that define how the compute nodes scale\.

The format is `[scaling <scalingname>]`\.

```
[scaling custom]
scaledown_idletime = 10
```

## `scaledown_idletime`<a name="scaledown-idletime"></a>

Specifies the amount of time in minutes without a job, after which the compute node terminates\.

This parameter is not used if `awsbatch` is the scheduler\.

The default value is `10`\.

```
scaledown_idletime = 10
```

[Update policy: The compute fleet must be stopped for this setting to be changed for an update.](using-pcluster-update.md#update-policy-compute-fleet)