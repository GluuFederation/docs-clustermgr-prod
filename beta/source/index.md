# Gluu Cluster Manager Documentation
## Overview
Cluster Manager is a GUI tool for installing and managing a highly available, clustered Gluu Server infrastructure.

## Beta Release    
This service is in beta. Please report bugs or feature requests via the [Gluu support portal](https://support.gluu.org). 

## Features

- LDAP Replication   
- Cache Management   
- Monitoring    
- Central Logging      
- End-to-end secure tunneling between oxAuth and Redis

## Components

Cluster Manager utilizes the following components:

1. **Gluu Server:** Free open source software package for identity and access management. 

1. **Redis-Server:** A value key-store known for it's high performance. Installed outside the chroot on all servers. The configuration file is located on the servers with Gluu at `/etc/redis/redis.conf` or `/etc/redis.conf`.

1. **Stunnel:** Used to protect communications between oxAuth and Redis and Twemproxy caching services. Configuration file located at `/etc/stunnel/stunnel.conf` on **all** servers. Runs on port 8888 of the NGINX/Proxy server and 7777 on the Gluu servers. **For security Redis runs on localhost.** Stunnel faciliates SSL communication over the Internet for Redis which doesn't come default with encrypted traffic.

1. **Twemproxy:** Used for cache failover, round-robin proxying and caching performance with Redis. The configuration file for this program can be found on the proxy server in `/etc/nutcracker/nutcracker.yml`. Runs locally on port 2222 of the NGINX/Proxy server. Twemproxy enables high availability by automatically detecting Redis server failure and redirecting traffic to other working instances. Twemproxy will **not** reintroduce failed servers. Restarting twemproxy can be performed manually, or a script can be written to automate the task of resetting the "down" flag of the failed server.

1. **NGINX:** Used to proxy communication between Gluu instances. The configuration file is located on the load balancing server (if installed) at `/etc/nginx/nginx.conf`. Can be set to round-robin for load balancing across servers by changing the nginx.conf to use `backend` instead of `backend_id`. **Note:** this breaks SCIM functionality if one of the servers goes down and redundancy isn't built into the logic of your SCIM client.

## Get Started
- [Install Cluster Manager](./installation/index.md)   
- [Deploy Gluu Clusters](./deploy/index.md)
- [Configure SSO](./authentication/index.md) (optional)
- [Troubleshooting](./troubleshooting/index.md)

## License
Licensed under the [GLUU SUPPORT LICENSE](https://github.com/GluuFederation/cluster-mgr/blob/master/LICENSE). To obtain a Gluu Support contract, see [support pricing](https://gluu.org/pricing). 



