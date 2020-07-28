# `[global]` Section<a name="global"></a>

**Topics**
+ [`cluster_template`](#cluster-template)
+ [`update_check`](#update-check)
+ [`sanity_check`](#sanity-check)

Specifies global configuration options related to `pcluster`\.

```
[global]
```

## `cluster_template`<a name="cluster-template"></a>

Defines the name of the cluster section that is used by default for the cluster\.

See [Cluster Definition](cluster-definition.md)\.

For example, the following setting specifies that the section that starts `[cluster default]` is used by default\.

```
cluster_template = default
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `update_check`<a name="update-check"></a>

Checks for updates to `pcluster`\.

```
update_check = true
```

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)

## `sanity_check`<a name="sanity-check"></a>

Attempts to validate the existence of the resources that are defined in the cluster parameters\.

The default value is `true`\.

```
sanity_check = true
```

**Note**  
Prior to AWS ParallelCluster 2\.5\.0, [`sanity_check`](#sanity-check) defaulted to `false`\.

[Update policy: This setting can be changed during an update.](using-pcluster-update.md#update-policy-setting-supported)