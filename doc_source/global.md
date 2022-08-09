# `[global]` section<a name="global"></a>

Specifies global configuration options related to `pcluster`\.

```
[global]
```

**Topics**
+ [`cluster_template`](#cluster-template)
+ [`update_check`](#update-check)
+ [`sanity_check`](#sanity-check)

## `cluster_template`<a name="cluster-template"></a>

Defines the name of the `cluster` section that's used for the cluster by default\. For additional information about `cluster` sections, see [`[cluster]` section](cluster-definition.md)\. The cluster name must start with a letter, contain no more than 60 characters, and only contain letters, numbers, and hyphens \(\-\)\.

For example, the following setting specifies that the section that starts `[cluster default]` is used by default\.

```
cluster_template = default
```

[Update policy: This setting is not analyzed during an update.](using-pcluster-update.md#update-policy-setting-ignored)

## `update_check`<a name="update-check"></a>

**\(Optional\)** Checks for updates to `pcluster`\.

The default value is `true`\.

```
update_check = true
```

[Update policy: This setting is not analyzed during an update.](using-pcluster-update.md#update-policy-setting-ignored)

## `sanity_check`<a name="sanity-check"></a>

**\(Optional\)** Attempts to validate the configuration of the resources that are defined in the cluster parameters\.

The default value is `true`\.

**Warning**  
If `sanity_check` is set to `false`, important checks are skipped\. This might cause your configuration to not function as intended\.

```
sanity_check = true
```

**Note**  
Before AWS ParallelCluster version 2\.5\.0, [`sanity_check`](#sanity-check) defaulted to `false`\.

[Update policy: This setting is not analyzed during an update.](using-pcluster-update.md#update-policy-setting-ignored)