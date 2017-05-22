# Cluster Manager Installation Procedure

## Minimum Requirements

**Server:** Ubuntu 14.04 (Trusty) or Ubuntu 16.04 (Xenial)

Prepare a server with Ubuntu 14.04 (Trusty) or Ubuntu 16.04 (Xenial) already installed.
Minimum recommendation resource:

|CPU Unit  |    RAM     |   Disk Space      | Processor Type |
|----------|------------|-------------------|----------------|
|       2  |    4GB     |   40GB            |  64 Bit

## Installation

Login via SSH to remote server for Cluster Manager installation.

```
# for Ubuntu Trusty
echo "deb https://repo.gluu.org/ubuntu/ trusty-devel main" > /etc/apt/sources.list.d/gluu-repo.list

# for Ubuntu Xenial
echo "deb https://repo.gluu.org/ubuntu/ xenial-devel main" > /etc/apt/sources.list.d/gluu-repo.list

curl https://repo.gluu.org/ubuntu/gluu-apt.key | apt-key add -
apt-get update
apt-get install -y gluu-cluster-mgr
```
## Generate Public and Private Keys

!!!Note:

    SSH trust between Cluster Manager server (3) and the Gluu Servers (1) and (2) via `ssh_keys` is necessary for it to run operations remotely.

Cluster Manager runs as `gluu` user. Hence in order to run operation remotely WITHOUT a password prompt,
the public key (`/home/gluu/.ssh/id_rsa.pub`) of Cluster Manager
should be added to the `/root/.ssh/authorized_keys` of all the servers it will communicate with.

To generate public and private key pair:

```bash
sudo -u gluu mkdir /home/gluu/.ssh
sudo -u gluu ssh-keygen -t rsa -b 4096 -C 'cluster-mgr'
```

Make sure **we're NOT USING any passphrase** when prompted.

Copy the public key (`/home/gluu/.ssh/id_rsa.pub`) into local computer:

```bash
scp root@<cluster-mgr-server>:/home/gluu/.ssh/id_rsa.pub </path/in/local/computer>
```

If using Windows machine and ssh using putty, you could use any scp app
like winscp to copy files to your local computer
From local computer, copy the content of downloaded public key and append it to `authorized_keys`
of Gluu CE server:

```bash
cat </path/in/local/computer> | ssh root@<gluu-server> 'cat >> .ssh/authorized_keys'
```

### Message Consumer

Login back to Cluster Manager server, then add new user and grant the
privileges to newly created user by login into MySQL console:

    mysql -u root -p

Type command below after successful MySQL console login:

    CREATE USER 'gluu'@'localhost' IDENTIFIED BY '<my-secret-password>';
    GRANT ALL PRIVILEGES ON gluu_log.* TO 'gluu'@'localhost';

Note, change the `<my-secret-password>` to use our own password.
Afterwards, exit from MySQL console login.

Iniatilize database for Message Consumer:

    cd /opt/message-consumer/conf
    mysql -u gluu -p < mysql_schema.sql

Modify lines below in `/opt/message-consumer/conf/prod.properties` file using text editor:

    spring.mysql.datasource.username=gluu
    spring.mysql.datasource.password=<my-secret-password>

Restart `message-consumer` service to make sure Message Consumer loads updated configuration:

    # for Ubuntu Trusty
    service message-consumer stop
    service message-consumer start

    # for Ubuntu Xenial
    systemctl restart message-consumer
    systemctl enable message-consumer

### Cluster Manager

Sync database schema (will create new db if not exist):

    APP_MODE=prod clustermgr-cli db upgrade

The command above will create a database `/opt/gluu-cluster-mgr/clustermgr.db`.
To make sure Cluster Manager webapp has sufficient access required files and directorues, run command below:

    chown -R gluu:gluu /opt/gluu-cluster-mgr/

Generate random unique string:

    cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 32 | head -n 1

!!!Note

    The command above will print out string in terminal. Copy the value and keep it somewhere else for later use (i.e. backup plan).

Create `/opt/gluu-cluster-mgr/instance/config.py` and put line below:

    SECRET_KEY = "<random-unique-string>"

Note, change the `<random-unique-string>` to use our own unique string.

Restart `gunicorn` service:

    # for Ubuntu Trusty
    service gunicorn restart

    # for Ubuntu Xenial
    systemctl restart gunicorn
    systemctl enable gunicorn

Restart `celery` service to make sure background jobs runner connected to correct database:

    # for Ubuntu Trusty
    service celery restart

    # for Ubuntu Xenial
    systemctl restart celery
    systemctl enable celery

Restart `celerybeat` service to make scheduled background jobs runner connected to correct database:

    # for Ubuntu Trusty
    service celerybeat restart

    # for Ubuntu Xenial
    systemctl restart celerybeat
    systemctl enable celerybeat

Now logout from remote server.

Note, Cluster Manager webapp is bind to `localhost:6000` in remote server.
We can use SSH tunneling to access it from web browser.

    ssh -L 8080:localhost:6000 root@<cluster-mgr-server>

Afterwards, type `localhost:8080` in web browser address bar.

### oxEleven Application (optional)

As an alternative of using JKS (the default backend) for storing oxAuth keys, we can also use [oxEleven](https://github.com/GluuFederation/oxEleven).

First things first, install docker as we need it for building oxEleven image:

    curl -fsSL https://raw.githubusercontent.com/GluuFederation/cluster-tools/master/get_docker.sh | sh

Build oxEleven docker image:

    cd ~
    git clone https://github.com/GluuFederation/gluu-docker.git
    cd gluu-docker
    git checkout ce-3
    docker build --rm=true --force-rm=true --tag=gluuox11 ubuntu/14.04/oxeleven

Generate random token (in this example, we will use UUID):

    $ cat /proc/sys/kernel/random/uuid

Keep the token generated from process above for later use.

Run oxEleven and bind it at `http://<host>:8190`:

    docker run -d --name=ox11 --env OXUUID=random-token -p 8190:8080 --restart=always gluuox11

Note, the access to oxEleven APIs will be protected by random token.
