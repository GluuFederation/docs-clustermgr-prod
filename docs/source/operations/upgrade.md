# Upgrades

## Auto-upgrades
Cluster Manager has a built-in upgrade feature which checks the GitHub repo for new versions twice a day. If a new version is available, CM will prompt you to upgrade. Simply accept the prompt to automatically upgrade your instance of Cluster Manager. 

## Manual Upgrades
To perform an upgrade manually, simply uninstall the current installation and install latest package. 

!!! Attention
    Although this process won't affect `~/.clustermgr5`, and is therefore safe, a [backup](./backup.md) is still recommended before proceeding. 

### Uninstall
To uninstall Cluster Manager:

```
clustermgr5-cli stop
pip3 uninstall clustermgr5
```

### Reinstall
To re-install latest package from Github:
1. Download latest version as explained in [Install from GitHub](../installation/index.md#installing-cluster-manager)
2. Upload cluster-mgr-4.5.zip to your server
3. Execute `pip3 install --upgrade /root/cluster-mgr-4.5.zip`
