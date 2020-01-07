# Cluster Manager Documentation
## Overview
Cluster Manager (CM) is a GUI tool for installing and managing a highly available, clustered [Gluu Server](https://gluu.org/docs/ce) infrastructure on physical servers or VMs (i.e. **not** Docker, K8, etc.). CM can be used to cluster an existing single node Gluu Server (a "seed"), or can be used to deploy a new cluster of Gluu Servers from scratch.  

## Features
CM automates many tasks associated with building and operating an HA/DR Gluu Server environment, including: 

- Gluu Server installation 
- Gluu OpenDJ LDAP Replication   
- Cache Management   
- Monitoring    
- Central Logging      
- End-to-end secure tunneling between oxAuth and Redis   

## Get Started
- [Review the Reference Architecture](./architecture/index.md) 
- [Install Cluster Manager](./installation/index.md)   
- [Deploy Gluu Clusters](./deploy/index.md)
- [Troubleshooting](./troubleshooting/index.md)

## License
Licensed under the [GLUU SUPPORT LICENSE](https://github.com/GluuFederation/cluster-mgr/blob/master/LICENSE). To obtain a Gluu Support contract, see [support pricing](https://gluu.org/pricing). 



