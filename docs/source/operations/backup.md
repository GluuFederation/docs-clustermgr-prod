## Backup & Restore

Cluster Manager stores all data under `~/.clustermgr4`. 

### Backup
To backup Cluster Manager, simply:

```
# cd ~
# tar -zcf clustermgr4-backup.tgz .clustermgr4
```

!!! Attention
    Make sure to keep `clustermgr4-backup.tgz` in a safe palce. 

### Restore
To restore from backup, extract as follows:

```
# cd ~
# tar -zxf clustermgr4-backup.tgz
```
