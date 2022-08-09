# `pcluster version`<a name="pcluster.version-v3"></a>

Displays the version of AWS ParallelCluster\.

```
pcluster version [-h] [--debug]
```

## Named arguments<a name="pcluster-v3.version.namedargs"></a>

`-h, --help`  
Shows the help text for `pcluster version`\.

`--debug`  
Enables debug logging\.

**Example using AWS ParallelCluster version 3\.1\.4:**

```
$ pcluster version
{
  "version": "3.1.4"
}
```