# Cluster Manager Installation

## Prerequisites

- A minimum of four (4) machines: 
    - Cluster Manager: One (1) machine running Ubuntu 14 or 16 with at least 1GB of RAM for cluster manager, which will proxy TCP and HTTP traffic.     
    - Load Balancer: One (1) machine running Ubuntu, CentOS, RHEL, or Debian with at least 1GB of RAM for the Nginx load balancer and Twemproxy.      
    - Gluu Server(s): At least two (2) machines running Ubuntu, CentOS, RHEL, or Debian for Gluu Servers.         
- Cluster Manager must have passwordless SSH root access to all servers in the cluster and should be installed on a secure administrator's computer or a VM    

## Ports

The following external ports need to be opened on the following machines:

![CM_Ports](../img/CM_Ports.png)

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

### Port Usage

- 22 will be used by Cluster Manager to pull logs and make adjustments to the systems

- 80 and 443 are self explanatory. 443 must be open between the Load Balancer and the Gluu Server/oxAuth

- 1636, 4444 and 8989 are necessary for LDAP usage and replication. These should be open between Gluu Server nodes

- 30865 is the default port for csync2 file system replication

- 7777 and 8888 are for securing communication between the Proxy server and the Gluu servers with stunnel

### Proxy

If you're behind a proxy you'll have to configure it inside the container/chroot as well.

Log in to each gluu node and set http proxy in container/chroot to your proxy's url like so:

```

# /sbin/gluu-server-3.1.2 login

Gluu.root# vi /etc/yum.conf

```

insert into section [main]:

```

[main]
.
.
proxy=http://proxy.example.org:3128/

```

Save the file.

The following error will be shown in Cluster Manager if the proxy is not configured properly inside the chroot:

```
One of the configured repositories failed (Unknown), and yum doesn't have enough cached data to continue... etc.

Could not retrieve mirrorlist http://mirrorlist.centos.org/?release=7&arch=x86_64&repo=updates&infra=stock error was 14: curl#7 - "Failed to connect to 2604:1580:fe02:2::10: Network is unreachable"
```
## Installing Cluster Manager

### SSH & Keypairs

Give Cluster Manager the ability to establish an ssh connection to the servers in the cluster. This includes the NGINX/load-balancing server:

`ssh-keygen -t rsa`

- This will initiate a prompt to create a keypair. **Do not input a password here**. Cluster Manager must be able to open connections to the servers.

- Copy the key (default is `id_rsa.pub`) to the `/root/.ssh/authorized_keys` file of all servers in the cluster, including the NGINX server (unless another load-balancing service will be used). **This MUST be the root authorized_keys.**

### Install Dependencies  

Install the necessary dependencies on the Gluu Cluster Manager machine:

```
sudo apt-get update
sudo apt-get install python-pip python-dev libffi-dev libssl-dev python-ldap redis-server default-jre
sudo pip install --upgrade setuptools influxdb psutil
```
Default-jre is for license requirements. It is not necessary if Java is already installed.

### Install the Package

Install cluster manager using the following command:

```
pip install clustermgr
```

There may be a few innocuous warnings, but this is normal.

### Prepare Database

Prepare the database using the following commands:

```
# clustermgr-cli db upgrade
```

### Add License Validator 

Prepare the license validator by using the following commands:

```
mkdir -p $HOME/.clustermgr/javalibs
wget http://ox.gluu.org/maven/org/xdi/oxlicense-validator/3.2.0-SNAPSHOT/oxlicense-validator-3.2.0-SNAPSHOT-jar-with-dependencies.jar -O $HOME/.clustermgr/javalibs/oxlicense-validator.jar
```

!!! Note
    License files are not currently enforced, it's on the honor system! In future versions, a license file may be required.  

!!! Warning
    All Cluster Manager commands need to be run as root.


### Stop/Start Cluster-Mgr 

 - `clustermgr-cli stop`
 - `clustermgr-clie start`


!!! Note
    All the Cluster Manager logs will be presented in one terminal this way, which may make it difficult to find errors.

Sometimes you'll need to stop the Cluster Manager services, i.e. if you're upgrading to the latest version, so that changes can take effect. To do that run:

```

# ps aux | grep clustermgr | awk '{print $2}' | xargs kill -9

```

### Create Credentials

When Cluster Manager is run for the first time, it will prompt for creation of an admin username and password. This creates an authentication config file at `$HOME/.clustermgr/auth.ini`. 

### Install oxd (optional)

We recommend using the [oxd client software](../authentication/index.md) to leverage your Gluu Server(s) for authentication to Cluster Manager. After oxd has been installed and configured, default authentication can be disabled by removing the authentication config file [specified above](#create-credentials).

### Create New User
We recommend creating an additional "cluster" user, other than the one used to install and configure Cluster Manager. 

This is a basic security precaution, due to the fact that the user SSHing into this server has unfettered access to every server connected to cluster manager. By using a separate user, which will still be able to connect to localhost:5000, an administrator can give an operator limited access to a server, while still being able to take full control of Cluster Manager. 

```
ssh -L 5000:localhost:5000 cluster@<server>
```

### Log In

Navigate to the cluster-mgr web GUI on your local machine:

```
http://localhost:5000/
```

## Deploy Clusters
Next, move on to [deploy the Gluu cluster](../deploy/index.md). 

