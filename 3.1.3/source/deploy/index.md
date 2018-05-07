# Deploying a Gluu Cluster

Follow this doc to deploy a cluster of Gluu Servers using Cluster Manager!

## Getting Started

Upon initial launch of Cluster Manager, the following screen will be presented to create an admin username and password:

![Admin_Creation](../img/Cluster_Manager-01.png)

Afterwards, start the process of building a cluster by clicking the `Setup Cluster` button:

![Add Server Prompt](../img/CM_Intro.png)

The two options are `Click Here To Use Your Standalone Gluu Server` and `Add Server`.

Click Here To Use Your Standalone Gluu Server:

This method is used to take a standalone Gluu Server deployment and prepare it for clustering. This is achieved by changing the hostname of the Gluu Server to that of the load-balancing server, which will act as a proxy between all the Gluu Server nodes. From there, it will use the standalone server as your seed for each new server added.

![Standalone Server seed](../img/CM_Standalone.png)

After it's finished, click the `Start` button to move on to the dashboard.

Add Server:

![New Server - Primary Server](../img/Cluster_Manager-05.png)

The following screen is used to add the Primary Server, which will be used as a "seed" by other nodes to pull their Gluu configuration and certificates. After deployment, all servers will function in a Master-Master configuration.

!!! Note
    Hostname here will be the actual hostname of the server, not the hostname of the NGINX/Proxy server. This is so that Cluster Manager can discover and connect to the server for installation and configuration. If the `Add IP Addresses and Hostnames to/etc/hosts file on each server` option was enabled in the `Settings` menu, the hostname here will be embedded automatically in the `/etc/hosts` files on this machine.

![Application Settings Screen](../img/Cluster_Manager-03.png)

- Replication Manager Password will be used in OpenDJ for replication purposes

- Load Balancer will be the hostname of either the NGINX proxy server, or any other load balancing server in use for the cluster 

!!! Warning
    The load balancer hostname cannot be changed *easily* after Gluu Server has been deployed. Please follow [these instructions](https://github.com/GluuFederation/community-edition-setup/tree/master/static/scripts/change_hostname) for every Gluu Server in your cluster if you must change the hostname.

- If any servers do not have Fully Qualified Domain Names (FQDNs), enable the `Add IP Addresses and hostnames to /etc/hosts file on each server` option. This will automatically assign hostnames to IP addresses in the `/etc/hosts` files inside and outside the Gluu chroot 

Once the settings are configured, click the `Update Configuration button`.

![Dashboard](../img/Cluster_Manager-06.png)

Click `Submit` to get routed to the Dashboard.

The Dashboard lists all servers in the cluster and provides the ability to add more servers, edit hostnames and IP addresses, and install Gluu automatically.

Click the `Add Server` button to add another node. 

!!! Note
    The admin password set in the Primary Server is the same for all the servers.

Once all servers have been added to the cluster, `Install Gluu` on the Primary Server.

![Install Primary Gluu Server](../img/Cluster_Manager-07.png)

- The values for the first five fields are used to create certificates
- Next, select which modules should be installed. The default Gluu components are pre-selected. For more information on each component, see the [Gluu docs](https://github.com/GluuFederation/docs-ce-prod/blob/3.1.3/3.1.3/source/index.md#free-open-source-software)
- Currently only OpenDJ is supported in Cluster Manager. This is pre-selected
- Accept the license agreements if you agree to the terms

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

Next navigate to the `Replication` tab to set up replication across the cluster. 

![Deploying LDAP Replication](../img/Cluster_Manager-10.png)

After configuring OpenDJ replication for the first time, this page will display replication status and other replication information.

![Replication Deployed screen](../img/Cluster_Manager-11.png)

If using Shibboleth, enable file system replication by clicking `Replication` on the left menu and selecting `File system Replication`. Click `Install File System Replication` to install and configure Csync2 and replicate necessary configuration files.

![File System Replication](../img/Cluster_Manager-12.png)

!!! Note
    If necessary, replication paths for other file systems can be added here as well.
    
!!! Warning
    Do not try to replicate databases with Csync2.

Navigate to `Cache Management` in the left menu to complete the cluster configuration. 

## Cache

![Cache Management](../img/Cluster_Manager-13.png)

oxAuth caches short-lived tokens, and in a balanced cluster all instances of oxAuth need access to the cache. To support this requirement and still enable high availability, Redis is installed outside the chroot on every Gluu Server. Configuration settings inside LDAP are also changed to allow access to these instances of Redis.

The `Fetch Cache Method` button is used to determine whether or not your LDAP is properly configured to utilize Redis or if it's still using `IN_MEMORY`, which will cause failures. It's not necessary to click, but good to have to make sure everything is configured properly.

Click `Setup Redis`

!!! Note
    Redis does not utilize encrypted communication, so Stunnel is installed and configured on all servers to protect information with SSL/TLS.

!!! Note
    Twemproxy is also installed on the NGINX/Proxy server to achieve redundancy. Twemproxy can detect Redis server communication failure to ensure high availability.

Cache configuration settings can be customized per the [component configuration](https://gluu.org/docs/cm/#default-components) documentation and also inside oxTrust.

![Successful Cache Management Installation](../img/Cluster_Manager-14.png)

Once this task is complete, the Gluu Server cluster is fully functional. 

Navigate to the hostname of the proxy server provided in the `Settings` option.

You can also go back to the cache management screen and check the status of Redis and Stunnel. 

## Monitoring   
Cluster Manager monitors the servers in the cluster to help better understand performance and potential issues. 

!!! Note
    Cluster Manager must be connected to the cluster in order to take advantage of monitoring features. 

Navigate to the `Monitoring` tab in the left-hand menu to see details about the servers in the cluster. 

![Monitoring Screen](../img/Cluster_Manager-15.png)

## Logging   
Cluster Manager gathers logs from all the nodes in the cluster for troubleshooting. Logs can be sorted by log type (oxAuth, oxTrust, HTTPD[Apache2], OpenDJ and Redis), Host and string search filters for easy sorting.

!!! Note
    Cluster Manager must be connected to the cluster in order to take advantage of logging features. 

Navigate to the `Logging` tab in the left-hand menu to view and sort logs related to servers in the cluster. 

![Logging Screen](../img/Cluster_Manager-16.png)

## Key Rotation

Key rotation is important for security.

![Key Rotation](../img/CM_KeyRotation.png)

Click `Settings` to enable this functionality, as well as setting the time interval in hours.

![Key Rotation Set](../img/CM_KeyRotationConfig.png)

## Custom Attributes

It can be a pain to add custom attributes into OpenDJ properly, so we've created a method to do it through the GUI. It will create a custom attribute object class you can define and then add attributes to that object class, which you can register in oxTrust.

![Custom ObjectClass Image](../img/CM_CustomObjectClass.png)

Now hit `Submit`

![Create Custom ObjectClass Update](../img/CM_CustomObjectClassUpdate.png)

Create Custom Attribute:

![Custom Attribute Image](../img/CM_CustomAttributesSet.png)

Please refer to the [LDAP Schema RFC](https://tools.ietf.org/html/rfc4519) and [this documentation](https://www.ldap.com/understanding-ldap-schema) on descriptions of LDAP schema attributes, if you're not already familiar with them.
