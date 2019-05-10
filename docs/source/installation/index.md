# Cluster Manager Installation

## Prerequisites

- A minimum of four (4) machines:
    - Cluster Manager: One (1) machine running **Ubuntu 14 or 16** with at least 2GB of RAM for cluster manager, which will proxy TCP and HTTP traffic.
    - Load Balancer: One (1) machine running Ubuntu, CentOS, RHEL, or Debian with at least 1GB of RAM for the Nginx load balancer and Twemproxy. This server is not necessary if you are using your own load balancer **and** you use Redis Cluster on the Gluu Server installations.
    - Gluu Server(s): At least two (2) machines running Ubuntu 16 for Gluu Servers. All nodes should have same OS.
    - Redis Cache Server: One (1) machine running Ubuntu, CentOS, RHEL, or Debian with at least 4GB of RAM
    - Browser: We recommend using Firefox or Chrome.

## Ports

The following external ports need to be opened on the following machines if you're using a default Cluster Manager configuration:

![CM_Ports](../img/CM_Ports.png)

| Gluu Servers | Description |
| -- | -- |
| 22 | SSH |
| 443 | HTTPS |
| 30865 | Csync2 |
| 1636 | LDAPS |
| 4444 | LDAP Repl |
| 8989 | LDAP Repl |
| 16379 | Stunnel |

| Load Balancer | Description |
|--| --|
| 22 | SSH |
| 80 | HTTP |
| 443 | HTTPS |

!!! Note
    The Load Balancer is the only node that should be externally accessible through 80 and 443 from outside your cluster network.

| Redis Cache Server | Description |
|--| --|
| 16379 | Stunnel |

| Cluster Manager | Description|
| -- | --|
| 22 | SSH |
|1636| LDAPS |

### Port Usage

- 22 will be used by Cluster Manager to pull logs and make adjustments to the systems

- 80 and 443 are self-explanatory. 443 must be open between the Load Balancer and the Gluu Server

- 1636, 4444 and 8989 are necessary for LDAP usage and replication. These should be open between Gluu Server nodes

- 30865 is the default port for Csync2 file system replication

- 16379 is for securing the caching communication between Gluu servers and Redis Cache Server over Stunnel

<!--
### Proxy

If you're behind a proxy, you'll have to configure it inside the container/chroot as well.

Log into each Gluu node and set the HTTP proxy in the container/chroot to your proxy's URL like so:

```

# service gluu-server-3.1.6 login

Gluu.root# vi /etc/yum.conf

```

insert into the `[main]` section:

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
-->

## Installing Cluster Manager

### SSH & Keypairs

Give Cluster Manager the ability to establish an SSH connection to the servers in the cluster. This includes the NGINX/load-balancing server. A simple key generation example:

`ssh-keygen -t rsa -b 4096`

- This will initiate a prompt to create a key pair. Cluster Manager must be able to open connections to the servers.

!!! Note
    Cluster Manager now works with encrypted keys and will prompt you for the password any time Cluster Manager is restarted.

- Copy the public key (default is `id_rsa.pub`) to the `/root/.ssh/authorized_keys` file of all servers in the cluster, including the Load Balancer (unless another load-balancing service will be used) and Redis Cache Server. **This MUST be the root authorized_keys.**

### Install Dependencies

Install the necessary dependencies on the Gluu Cluster Manager machine:

```
sudo apt-get update
sudo apt-get install python-pip python-dev libffi-dev libssl-dev python-ldap redis-server default-jre
sudo pip install --upgrade setuptools influxdb psutil
```

Default-jre is for license requirements. It is not necessary if Java is already installed.

### Install the Package

Install Cluster Manager using the following command:

```
pip install clustermgr
```

There may be a few innocuous warnings, but this is normal.

<!--
### Add License Validator

Prepare the license validator by using the following commands:

```
mkdir -p $HOME/.clustermgr/javalibs
wget -q https://ox.gluu.org/maven/org/xdi/oxlicense-validator/3.1.4.Final/oxlicense-validator-3.1.4.Final-jar-with-dependencies.jar -O $HOME/.clustermgr/javalibs/oxlicense-validator.jar
```

!!! Note
    License files are not currently enforced, it's on the honor system! Please see the [Gluu Support License](https://github.com/GluuFederation/cluster-mgr/blob/master/LICENSE) to see if you're eligible to use Cluster Manager in production. In future versions, a license file may be required.

-->

!!! Warning
    All Cluster Manager commands need to be run as root.

 

### Add Key Generator

If automated key rotation is required, you'll need to download the keygen.jar. Prepare the OpenID Connect keys generator by using the following commands:

```
mkdir -p $HOME/.clustermgr/javalibs
wget -q https://ox.gluu.org/maven/org/xdi/oxauth-client/3.1.6.Final/oxauth-client-3.1.6.Final-jar-with-dependencies.jar -O $HOME/.clustermgr/javalibs/keygen.jar
```

Automated key rotation can be configured inside the Cluster Manager UI.

### Stop/Start/Restart Cluster Manager

The following commands will stop/start/restart all the components of Cluster Manager:

 - `clustermgr-cli stop`
 - `clustermgr-cli start`
 - `clustermgr-cli restart`


!!! Note
    All the Cluster Manager logs can be found in the `$HOME/.clustermgr/logs` directory

!!! Warning
    Cluster Manager need to be started by root or at least user should be able to run `sudo <command>` without password.

### Create Credentials

When Cluster Manager is run for the first time, it will prompt for creation of an admin username and password. This creates an authentication config file at `$HOME/.clustermgr/auth.ini`.


### Create New User

We recommend creating an additional "cluster" user, other than the one used to install and configure Cluster Manager.

This is a basic security precaution, due to the fact that the user SSHing into this server has unfettered access to every server connected to Cluster Manager. By using a separate user, which will still be able to connect to localhost:5000, an administrator can give an operator limited access to a server, while still being able to take full control of Cluster Manager.

```
ssh -L 5000:localhost:5000 cluster@<server>
```

### Log In

Navigate to the Cluster Manager web GUI on your local machine:

```
http://localhost:5000/
```

## Deploy Clusters
Next, move on to [deploy the Gluu cluster](../deploy/index.md).
