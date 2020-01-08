# Backing Up and Upgrading Cluster Manager

## Backup and Restore
**Cluster Manager** keeps all data under `~/.clustermgr4`, so it is ehough to keep somehere to backup. So, for backup Cluster Manager:

```
# cd ~
# tar -zcf clustermgr4-backup.tgz .clustermgr4
```

and keep clustermgr4-backup.tgz` in a safe palce. Once you need to restore backup you can extract as follows:

```
# cd ~
# tar -zxf clustermgr4-backup.tgz
```

## Upgrade

Cluster Manager has builtin upgrade feature, it checks version on github twice a day. If version is changed it will ask you to
perform upgrade from github. Otherwise you can perfom upgrade manually, just uninstall current installation and install 
latest package. Uninstall and re-install won't touch `~/.clustermgr4`, so it is keep to uninstall and re-install. But we still
recommend you to perform backup as explained above. To uninstull Cluster Manager:

```
# pip uninstall clustermgr4
```

To re-install latest package from githbu

```
# pip install https://github.com/GluuFederation/cluster-mgr/archive/4.0.zip
```
