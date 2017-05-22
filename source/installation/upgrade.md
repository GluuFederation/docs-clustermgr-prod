# Cluster Manager Upgrade Guide

## Upgrade using apt-get

To upgrade Gluu Cluster Manager using `apt-get` commands:

    apt-get clean
    apt-get update
    apt-get install gluu-cluster-mgr

Make sure database schema is updated:

    APP_MODE=prod clustermgr-cli db upgrade
    chown -R gluu:gluu /opt/gluu-cluster-mgr/

Restart the services for Cluster Manager using commands below:

    # for Ubuntu Trusty
    service gunicorn restart
    service celery restart
    service celerybeat restart

    # for Ubuntu Xenial
    systemctl restart gunicorn
    systemctl restart celery
    systemctl restart celerybeat

Now your existing package has been upgraded with new build.
