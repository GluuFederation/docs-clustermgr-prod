# Building pyz Files
In this section we will explain how to build build self-contained, single-artifact executables, Python zipapps with all their dependencies included.


## Install dependencies

### on Ubuntu 18/20

```
apt install -y wget unzip python3 python3-pip make
pip3 install --upgrade pip
pip3 install --upgrade setuptools
pip3 install --upgrade shiv
```

### on CentOS/Rhel 7 and CentOS/Rhel 8

```
yum install -y wget unzip python3 python3-pip make
pip3 install --upgrade pip
pip3 install --upgrade setuptools
pip3 install --upgrade shiv
```

## Build zipapp

Download Cluster Manager archieve

```
wget https://github.com/GluuFederation/cluster-mgr/archive/refs/heads/4.4.zip
```

Unzip and build

```
unzip 4.4.zip
cd cluster-mgr-4.4/
make zipapp
```

## Execute zipapp

You can run Cluster Manager as

```
./clustermgr4-4.pyz run-clustermgr
```

Locate your browser to http://localhost:5000

To stop Cluster Manager press **Ctrl+C**
