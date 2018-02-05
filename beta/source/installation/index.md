# Cluster Manager Installation

## Prerequisites

- A minimum of four (4) machines: One machine will be used for cluster manager, which could be localhost on the installers computer. This machine will only be used for proxying TCP and HTTP traffic. The other three machines will host the Gluu Server. 

- Ubuntu 14 and 16 installed on the machine hosting Cluster Manager. The other three machines hosting Gluu can have Ubuntu, CentOS, RHEL, or Debian.

- Cluster Manager will have SSH access to all servers in the cluster and should be installed on a secure administrators computer or a VM. 

- Cluster Manager no longer needs to be actively connected to the cluster after initial setup. However, in order to take advantage of its monitoring, configuration, and logging features, Cluster Manager must be connected to the cluster. 

### External ports

The following external ports need to be opened:

<table>
  <tr><th> Gluu Servers </th><th> Load Balancer </th> <th> Cluster Manager </th></tr>
<tr><td>

|22| --| 443| 808* |
|--| -- | -- | -- |
|1636| 4444 | 8989 | 7777|

</td><td>

|22| 80 |
|--|--|
|443 | 8888 |

</td>

</td><td>

|22|
|--|
|1636|

</td></tr> 

</table>

- 22 will be used by Cluster Manager to pull logs and make adjustments to the systems. 

- 80 and 443 are self explanatory. 

- 1636, 4444 and 8989 are necessary for LDAP usage and replication. 

- 7777 and 8888 are for securing communication between the Proxy server and the Gluu servers with stunnel.

## Default Components
See the [homepage](../index.md#default-components) for a discussion of default components installed with Cluster Manager. 

## Installing Cluster Manager

### SSH & Keypairs

Give Cluster Manager the ability to establish an ssh connection to the servers in the cluster. This includes the NGINX/Load-balancing server:

`ssh-keygen -t rsa`

- This will initiate a prompt to create a key-pair. **Do not input a password here**. Cluster Manager must be able to open connections to the servers.

- Copy the key (default is `id_rsa.pub`) to the `/root/.ssh/authorized_keys` file of all servers in the cluster, including the NGINX server (unless another load-balancing service will be used).

**This HAS to be the root authorized_keys or Cluster Manager will not work**

### Install dependencies  

Install the necessary dependencies on the Gluu Cluster Manager machine:

```
sudo apt-get update
sudo apt-get install python-pip python-dev libffi-dev libssl-dev redis-server default-jre
(default-jre is for license requirements. Not necessary if Java already installed)
sudo pip install --upgrade setuptools influxdb
```

### Install the package

Install cluster manager using the following command:

```
pip install clustermgr
```

There may be a few innocuous warnings, but this is normal.

### Prepare Database

Prepare the database using the following commands:

```
clustermgr-cli db upgrade
```

### Add license validator 

Prepare the license validator by using the following commands:

```
mkdir -p $HOME/.clustermgr/javalibs
wget http://ox.gluu.org/maven/org/xdi/oxlicense-validator/3.2.0-SNAPSHOT/oxlicense-validator-3.2.0-SNAPSHOT-jar-with-dependencies.jar -O $HOME/.clustermgr/javalibs/oxlicense-validator.jar
```

!!! Note
    Licenses files are not currently enforced. It is on the honor system! In future versions, a license file may be required.  

### Run Celery

Run celery scheduler and workers in separate terminals:

```
# Terminal 1
clustermgr-beat &

# Terminal 2
clustermgr-celery &
```

### Run clustermgr-cli

Open another terminal to run clustermgr-cli:

```
clustermgr-cli run
```

### Create Credentials

When Cluster Manager is run for the first time, it will prompt creation of an administrator user name and password. This creates an authentication config file at `$HOME/.clustermgr/auth.ini`. The default authentication method can be disabled by removing the file.

### Intstall oxd (optional)

We recommend utilizing the [oxd client software](https://github.com/GluuFederation/cluster-mgr/wiki/User-Authentication#using-oxd-and-gluu-server) to leverage Gluu for authentication to Cluster Manager.  After oxd has been installed and configured, [default authentication](https://github.com/GluuFederation/cluster-mgr/wiki/User-Authentication#using-default-admin-user) can be disabled. 

### Create new user
It is recommended to create an additional "cluster" user, other than the one used to install and configure cluster manager. 

This is a basic security precaution, due to the fact that the user ssh'ing into this server has unfettered access to every server connected to cluster manager. By using a separate user, which will still be able to connect to localhost:5000, an administrator can give an operator limited access to a server, while still being able to take full control of Cluster Manager. 

```
ssh -L 5000:localhost:5000 cluster@<server>
```

### Login

Navigate to the cluster-mgr web GUI on your local machine:

```
http://localhost:5000/
```
