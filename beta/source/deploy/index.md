# Deploying a Gluu Cluster

Follow this doc to deploy a cluster of Gluu Servers using Cluster Manager!

## Getting Started

Upon initial launch of Cluster Manager, the following screen will be presented to create an admin user and password:

![Admin_Creation](../img/Cluster_Manager-01.png)

Afterwards, start the process of building a cluster by clicking the `Setup Cluster` button:

![Setup Cluster](../img/Cluster_Manager-02.png)

Provide values for the fields in the Applications Settings page: 

![Application Settings Screen](../img/Cluster_Manager-03.png)

- Replication Manager Password will be used in OpenDJ for replication purposes.

- Load Balancer will be the hostname of either the NGINX proxy server, or any other load balancing server in use for the cluster. 

!!! Warning
    The load balancer hostname **cannot** be changed after Gluu has been deployed. To change the hostname, it is necessary to redeploy Gluu.

- If any servers do not have Fully Qualified Domain Names (FQDNs), enable the `Add IP Addresses and hostnames to /etc/hosts file on each server` option. This will automatically assign hostnames to IP addresses in the `/etc/hosts` files inside and outside the Gluu chroot. 

Once the settings are configured, click the `Update Configuration button`.

![Add Server Prompt](../img/Cluster_Manager-04.png)

Click `Add Server`

![New Server - Primary Server](../img/Cluster_Manager-05.png)

The following screen is used to add the Primary Server, which will be used by other nodes to pull their Gluu configuration and certificates. After Deployment, all servers will function in a Master-Master configuration.

!!! Note
    Hostname will be the actual hostname of the server, not the hostname of the NGINX/Proxy server. If the `Add IP Addresses and Hostnames to/etc/hosts file on each server` option was enabled in the `Settings` menu, this will be the hostname embedded automatically in the `/etc/hosts` files on this machine.

![Dashboard](../img/Cluster_Manager-06.png)

Click `Submit` to get routed to the Dashboard.

The Dashboard lists all servers in the cluster and provides the ability to add more servers, edit hostnames and IP addresses, and install Gluu automatically.

Click the `Add Server` button to add another node. 

!!! Note
    The admin password set in the Primary server is the same for all the servers.

Once all servers have been added to the cluster, `Install Gluu` on the primary server.

![Install Primary Gluu Server](../img/Cluster_Manager-07.png)

- Values for the first five fields are used to create certificates.
- Values for inumOrg and inumAppliance are generated automatically. Changing the defaults is not recommended.
- Next, select which modules should be installed. The default Gluu components are pre-selected. For more information on each component, see the [Gluu docs](https://github.com/GluuFederation/docs-ce-prod/blob/3.1.2/3.1.2/source/index.md#free-open-source-software). 
- Currently only OpenDJ is supported in Cluster Manager. This is pre-selected. 
- Accept the license agreements.

Click `Submit` to begin installation. 

![Installing Gluu Server](../img/Cluster_Manager-09.png)

!!! Note 
    This may take some time, so please be patient.

Once completed, repeat the process for the other servers in the cluster.

When all the installations have completed, install NGINX:

- Navigate to `Cluster` in the left menu
- Select `Install Nginx`

Finally, the `LDAP Replication` screen will appear, where LDAP replication can be enabled and disabled.  

During initial deloyment click the `Deploy All` button and wait for the process to finish.

## Replication

Next navigate to the `Replication` tab to setup replication across the cluster. 

![Deploying LDAP Replication](../img/Cluster_Manager-10.png)

After configuring OpenDJ replication for the first time, this page will display replication status and other replication information.

![Replication Deployed screen](../img/Cluster_Manager-11.png)

Enable file system replication by clicking `Replication` on the left menu and selecting `File system Replication`. Click `Install File System Replication` to install and configure csync2 and replicate necessary configuration files.

![File System Replication](../img/Cluster_Manager-12.png)

!!! Note
    If necessary, replication paths for other file systems can be added here as well.

Navigate to `Cache Management` in the left menu to complete the cluster configuration. 

## Cache

![Cache Management](../img/Cluster_Manager-13.png)

oxAuth caches short-lived tokens, and in a balanced cluster all instances of oxAuth need access to the cache. To support this requirement and still enable high-availability, Redis is installed outside the chroot on every Gluu server. Configuration settings inside LDAP are also changed to allow access to these instances of Redis.

!!! Warning
    Redis does not utilize encrypted communication, therefore stunnel needs to be installed and configured on all servers to protect information with SSL.

!!! Note
    Twemproxy is also installed on the NGINX/Proxy server to achieve redundancy. Twemproxy can detect redis server communication failure to ensure high availability.

Cache configuration settings can be customized per the (component configuration)[https://gluu.org/docs/cm/#default-components] documentation and also inside oxTrust.

![Successful Cache Management Installation](../img/Cluster_Manager-14.png)

Once this task is complete, the Gluu Server cluster is fully functional. 

Navigate to the hostname of the proxy server provided in the `Settings` option.

## Additional Management Components

Cluster Manager offers a couple additional services to help manage a cluster of Gluu Servers, including process monitoring and logging management. 

`Monitoring` and `Logging Management`, respectively, can be found in the left-hand menu.

Simply click `Setup Monitoring` and `Setup Logging` to take advantage of these features. 

![Monitoring Screen](../img/Cluster_Manager-15.png)

Monitoring offers a quick glimpse at performance and potential issues.

![Logging Screen](../img/Cluster_Manager-16.png)

Logging gathers logs from all the nodes for troubleshooting. These logs can be sorted by log type (oxAuth, oxTrust, HTTPD[Apache2], OpenDJ and Redis), Host and also string search filters for easy sorting.
