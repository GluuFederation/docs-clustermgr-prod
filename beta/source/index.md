# Gluu Cluster Manager Documentation
## Overview
Cluster Manager is a GUI tool for installing and managing a highly available, clustered Gluu Server infrastructure.

## Beta Release    
This service is in beta. Please report bugs or feature requests via the [Gluu support portal](https://support.gluu.org). 

## Features

- LDAP Replication
- Cache Management
- Monitoring
- Logging

## Components

Cluster Manager utilizes the following components to manage and deploy a highly available Gluu Server identity & access management service:

1. **Gluu Server:** Free open source software package for identity and access management. 

1. **Redis-Server:** A value key-store known for it's high performance. Installed outside the chroot on all servers. Configuration file located at `/etc/redis/redis.conf` or `/etc/redis.conf` on the servers with Gluu installed.

1. **Stunnel:** Used to protect communications between oxAuth and the caching services: Redis and Twemproxy. Configuration file located at `/etc/stunnel/stunnel.conf` on **all** servers. Runs on port 8888 of the NGINX/Proxy server and 7777 on the Gluu servers. **For security Redis runs on localhost.** Stunnel faciliates SSL communication over the Internet for Redis which doesn't come default with encrypted traffic.

1. **Twemproxy:** Used for cache failover, round-robin proxying and caching performance with Redis. The configuration file for this program can be found in `/etc/nutcracker/nutcracker.yml` on the proxy server. Runs locally on port 2222 of your NGINX/Proxy server. Because of demand for high availability, Twemproxy is a must as it automates detection of Redis server failure and redirects traffic to working instances. Please note that Twemproxy will not reintroduce failed servers. You can manually or create a script that automates the task of restarting twemproxy, which will reset the "down" flag of that server.

1. **NGINX:** Used to proxy communication to the instances of Gluu. Configuration file located at `/etc/nginx/nginx.conf` on the load balancing server (if installed). Can be set to round-robin instances of oxAuth for balancing load across servers by changing the nginx.conf to use `backend` instead of `backend_id`. **Note:** this breaks SCIM functionality if one of the servers goes down and redundancy isn't built into the logic of your SCIM client.

## Installation
Get started by following the [installation guide](./installation/index.md). 

## License
Licensed under the [GLUU SUPPORT LICENSE](https://github.com/GluuFederation/cluster-mgr/blob/master/LICENSE). Copyright Gluu 2018.



