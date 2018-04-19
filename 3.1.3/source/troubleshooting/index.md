## Logging for Errors and Troubleshooting

Cluster Manager displays logs in the GUI about what's happening on the system it's interacting with.

There is also additional information in the terminals: `clustermgr-celery` and `clustermgr-cli run`. 

Here's a standard successful connection message:

```
INFO:werkzeug:127.0.0.1 - - [02/Feb/2018 08:11:12] "GET /log/0a4c3f1f-e2c2-4d0a-81ff-08c808cf6269 HTTP/1.1" 200 -
[2018-02-02 08:11:12,749: INFO/ForkPoolWorker-2] Connected (version 2.0, client OpenSSH_7.4)
[2018-02-02 08:11:13,083: INFO/ForkPoolWorker-2] Authentication (publickey) successful!
[2018-02-02 08:11:13,476: INFO/ForkPoolWorker-2] [chan 0] Opened sftp connection (server version 3)
```

Most of the time you get rudimentary status checks like this:

```
INFO:werkzeug:127.0.0.1 - - [02/Feb/2018 08:07:59] "GET /log/0a4c3f1f-e2c2-4d0a-81ff-08c808cf6269 HTTP/1.1" 200 -
127.0.0.1 - - [02/Feb/2018 08:08:00] "GET /log/0a4c3f1f-e2c2-4d0a-81ff-08c808cf6269 HTTP/1.1" 200 -
INFO:werkzeug:127.0.0.1 - - [02/Feb/2018 08:08:00] "GET /log/0a4c3f1f-e2c2-4d0a-81ff-08c808cf6269 HTTP/1.1" 200 -
127.0.0.1 - - [02/Feb/2018 08:08:01] "GET /log/0a4c3f1f-e2c2-4d0a-81ff-08c808cf6269 HTTP/1.1" 200 -
```

You'll get error messages if there's a problem in a process. Use the error messages to help with troubleshooting issues.

Patience is recommended, but sometimes the process does hang irreparably. Stop and restart the process to troubleshoot. You can do this by running the following command:

`ps aux | grep clustermgr | awk \'{print $2}\' | sudo xargs kill -9`

Then, restart the processes (in one terminal if needed):

`clustermgr-beat & clustermgr-celery & clustermgr-cli run`

If you have any issues, please open a ticket at [support.gluu.org](https://support.gluu.org/).
