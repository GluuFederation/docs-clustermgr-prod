## Failure on LDAP Replication

On first try, replication between provider and consumer might be failed (an error message **"Test data is NOT replicated."** appeared in Cluster Manager web app). 

To investigate the issue:

1. Login to consumer server.

```
ssh root@<server>
```

2. Check OpenLDAP log.

```
tail -n 100 /var/log/openldap/ldap.log
```

3. If we can't find / unsure the cause of the error, restart the OpenLDAP.

```
service solserver restart
```

4. Retry the replication via Cluster Manager web app.

5. If the problem persists, stop OpenLDAP.

```
service solserver stop
```

6. Start OpenLDAP with debug mode (OpenLDAP will display logs)

```
service solserver start -d 1
```

7. Retry the replication via Cluster Manager web app while investigating the logs mentioned in step 6.

8. Do necessary action to fix the problem pointed in the logs.

9. If the problem is gone, make sure to stop OpenLDAP (using CTRL+C) then run `service solserver restart`.