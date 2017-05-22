#Cluster Manager Upgrade Guide

## Upgrade using apt-get
    
To upgrade Gluu Cluster Manager using apt-get commands

    ` #apt-get clean`
    
    ` #apt-get update`
    
    ` #apt-get install python-cluster-mgr`
    
To check for the latest policy use below command

    ` #apt-cache policy python-cluster-mgr`     
    
Restart the services for Cluster Manager using below commands.

    ` #systemctl restart gunicorn`
    
    ` #systemctl restart celery`
    
    ` #systemctl restart celerybeat`
    
Now your existing package has been upgraded with new build.

<!-- ## Manual Upgrade

To upgrade Gluu Cluster Manager manually, use below commands:

Navigate to the Cluster Manager install directory.

    ` #cd /usr/lib/python2.7/dist-packages/clustermgr`
    
Get the latest package and install

    ` #wget https://github.com/GluuFederation/cluster-mgr/raw/be2679d9fc7d53076df386847374f3ef68087c50/clustermgr/tasks.py -O tasks.py`
   
   ` #rm tasks.pyc`
   
Restart Cluster Manager Services

    ` #systemctl restart gunicorn`
   
    ` #systemctl restart celery`
   
    ` #systemctl restart celerybeat` -->
    
