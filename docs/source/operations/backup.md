# Backup & Restore

## Overview
Cluster Manager stores all data under `~/.clustermgr5`. The process to backup and restore is described below. 

### Backup
To backup Cluster Manager, simply:

```
# clustermgr5-cli stop
# cd ~
# tar -zcf clustermgr4-backup.tgz .clustermgr5
```

!!! Attention
    Make sure to keep `clustermgr5-backup.tgz` in a safe palce. 

### Restore
To restore from backup, extract as follows:

```
# cd ~
# tar -zxf clustermgr5-backup.tgz
# clustermgr5-cli start
```
