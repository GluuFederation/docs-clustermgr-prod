# Cluster Manager Installation

## Prerequisites

- A minimum of four (4) machines: One (1) machine is for cluster manager, which will proxy TCP and HTTP traffic. This could be localhost on the installers computer. The other three (3) machines will host Gluu Servers. 

- Ubuntu 14 and 16 installed on the machine hosting Cluster Manager. The other three machines hosting Gluu can have Ubuntu, CentOS, RHEL, or Debian.

- Cluster Manager must have SSH access to all servers in the cluster and should be installed on a secure administrators computer or a VM. 

!!! Note
    After initial setup, Cluster Manager no longer needs an active connection to the cluster. However, in order to take advantage of monitoring, configuration, and logging features, Cluster Manager must be connected to the cluster. 

## Ports

The following external ports need to be opened on the following machines:


| Gluu Servers | Description |
| -- | -- |
| 22 | SSH |
| 443 | SSL |
| 30865 | Csync2 |
| 1636 | LDAP |
| 4444 | LDAP Repl |
| 8989 | LDAP Repl |
| 7777 | Stunnel |

| Load Balancer | Description |
|--| --|
| 22 | SSH |
| 80 | HTTP |
| 443 | HTTPS |
| 8888 | Stunnel |

!!! Note
    The Load Balancer is the only node that should be externally accessible through 80 and 443 from outside your cluster network.

| Cluster Manager | Description|
| -- | --|
| 22 | SSH |
|1636| LDAP |

### Port usage

- 22 will be used by Cluster Manager to pull logs and make adjustments to the systems. 

- 80 and 443 are self explanatory. 443 must be open between the Load Balancer and the Gluu Server/oxAuth. 

- 1636, 4444 and 8989 are necessary for LDAP usage and replication. These should be open between Gluu Server nodes.

- 30865 is the default port for csync2 file system replication.

- 7777 and 8888 are for securing communication between the Proxy server and the Gluu servers with stunnel.

## Installing Cluster Manager

### SSH & Keypairs

Give Cluster Manager the ability to establish an ssh connection to the servers in the cluster. This includes the NGINX/Load-balancing server:

`ssh-keygen -t rsa`

- This will initiate a prompt to create a key-pair. **Do not input a password here**. Cluster Manager must be able to open connections to the servers.

- Copy the key (default is `id_rsa.pub`) to the `/root/.ssh/authorized_keys` file of all servers in the cluster, including the NGINX server (unless another load-balancing service will be used).

**This MUST be the root authorized_keys or Cluster Manager will not work**

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

### Install oxd (optional)

We recommend using the [oxd client software](../authentication/index.md) to leverage your Gluu Server(s) for authentication to Cluster Manager. After oxd has been installed and configured, default authentication can be disabled. 

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

## Deploy clusters
Next, move on to [deploy the Gluu cluster](../deploy/index.md). 

