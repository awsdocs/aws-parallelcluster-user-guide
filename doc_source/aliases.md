# `[aliases]` section<a name="aliases"></a>

**Topics**

Specifies aliases, and enables you to customize the `ssh` command\.

Note the following default settings:
+ *CFN\_USER* is set to the default user name for the OS
+ *MASTER\_IP* is set to the IP address of the master instance
+ *ARGS* is set to whatever arguments the user provides after *pcluster ssh cluster\_name*

```
[aliases]
# This is the aliases section, you can configure
# ssh alias here
ssh = ssh {CFN_USER}@{MASTER_IP} {ARGS}
```

[Update policy: This setting is not analyzed during an update.](using-pcluster-update.md#update-policy-setting-ignored)