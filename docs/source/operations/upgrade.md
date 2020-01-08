# Upgrades

## Overview
Cluster Manager has a built-in upgrade feature which checks the GitHub repo for new versions twice a day. If a new version is available, CM will prompt you to upgrade. 

To perfom an upgrade manually, simply uninstall the current installation and install latest package. Although this process won't affect `~/.clustermgr4`, and is therefore safe, a [backup](./backup.md) is still recommended before proceeding. 

## Uninstall
To uninstall Cluster Manager:

```
# pip uninstall clustermgr4
```

## Reinstall
To re-install latest package from githbu

```
# pip install https://github.com/GluuFederation/cluster-mgr/archive/4.0.zip
```
