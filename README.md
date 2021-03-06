# KodeKloud Linux Basics: Service Management with SYSTEMD

[KodeKloud Linux Basics Course Notes Table of Contents](https://github.com/pslucas0212/LinuxBasics)

### SYSTEMD and Services

You can use a shell script during a boot process to be a systemd service.  In this example we want to start a python script after the PostgresDB is up and running.  We created a service account for the systemd service to run under - project_mercury.  

If the application fails it will try to restart automatically and if there is a failure the app will try to restart after 10 seconds.  If doen't restart after 10 seconds then it won't start up.  All services events will be logged and the the app will load when booting into Graphical mode (traget).

To run in the background will we neet to create a service unit file.  In this example we call it project-mercury.service.  We locate the unit service file
```
/etc/systemd/system/project-mercury-service
```
File contents for the most basic way to start a service.  Since this is shell script we need the /bin/bash
```
[Service]
ExecStart= /bin/bash /usr/bin/project-mercury.sh
```
To start this service in the example, will we use sudo by issuing since it currently setup to run as root.
```
$ sudo systemctl start project-mercury.service
```
Check running service
```
$ systemctl status project-mercury.service
```

Another systemctl status example
```
$ systemctl status chrony
● chrony.service - chrony, an NTP client/server
   Loaded: loaded (/lib/systemd/system/chrony.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2022-06-07 16:17:07 CDT; 1 weeks 0 days ago
     Docs: man:chronyd(8)
           man:chronyc(1)
           man:chrony.conf(5)
  Process: 594 ExecStart=/usr/sbin/chronyd $DAEMON_OPTS (code=exited, status=0/SUCCESS)
  Process: 641 ExecStartPost=/usr/lib/chrony/chrony-helper update-daemon (code=exited, status=0/SUCCESS)
 Main PID: 636 (chronyd)
    Tasks: 2 (limit: 4915)
   CGroup: /system.slice/chrony.service
           ├─636 /usr/sbin/chronyd -F -1
           └─640 /usr/sbin/chronyd -F -1

Jun 15 09:37:18 ns01.example.com chronyd[636]: Selected source NMEA
...
```
Stop service with...
```
$ systemctl stop project-mercury.service
```

Enable service to start automatically at boot time.  We need to update the service unit file by adding an [Install] section. Use WantedBy directive for systemd or run target level
```
[Service]
ExecStart= /bin/bash /usr/bin/project-mercury.sh
[Install]
WantedBy graphical.target
```
Let's add the serivce account to start the service instead of root with User= and Restart and RestartSec for failures
```
[Service]
ExecStart= /bin/bash /usr/bin/project-mercury.sh
User=project_mercury
Restart=on-failure
RestartSec=10

[Install]
WantedBy  graphical.target
```
Since the Django Python services depends on the PostGress DB being ready add [Unit] section.  Description and documentation are optional
```
[Unit]
Description=Python Django for Project Mercury
Documentation=http://wiki.caleston-dev.ca/mercur
After=postgresql.service

[Service]
ExecStart= /bin/bash /usr/bin/project-mercury.sh
User=project_mercury
Restart=on-failure
RestartSec=10

[Install]
WantedBy  graphical.target
```

An example of the chronyd.service found /etc/systemd/system/chronyd.service:
```
$ cat chronyd.service 
[Unit]
Description=chrony, an NTP client/server
Documentation=man:chronyd(8) man:chronyc(1) man:chrony.conf(5)
Conflicts=systemd-timesyncd.service openntpd.service ntp.service ntpsec.service
After=network.target
ConditionCapability=CAP_SYS_TIME

[Service]
Type=forking
PIDFile=/run/chronyd.pid
EnvironmentFile=-/etc/default/chrony
ExecStart=/usr/sbin/chronyd $DAEMON_OPTS
ExecStartPost=-/usr/lib/chrony/chrony-helper update-daemon
PrivateTmp=yes
ProtectHome=yes
ProtectSystem=full

[Install]
Alias=chronyd.service
WantedBy=multi-user.target

```

We can now restart the service.  First run the daemon-reload command and then start the service
```
$ systemctl daemon-reload
$ systemctl start project-mercury.service
```



### SYSTEMD Tools
Two major tools for managing systemd targets are systemctl and jorunalctl
  
sytemctl main command to manage services on systemd manage server and can:
- manage system state
- start, stop, restart, reload
- enable/disable
- list and manage units
- list and update targets
  
journalctl can query the contents of the query systemd journal for status and troubleshooting


systemctl commonly used commands
```
$ systemctl start docker
$ systemctl stop docker
$ systemctl reastart docker - starts and stops service
$ systemctl reload docker - reloads service with out interrupting running service
$ systemctl enable docker - sets the service to load at startup
$ systemctl disable docker - removes the service from loading at startup
$ systemctl status docker - provids information about the status of the services
```

Service States   
State | Meaning
------|--------
Active | Service is running
Inactive | Service is stopped
Failed | The services has crashed/timedout/has an error, etc.

Running systemctl daemon-reload command after making changes to a service unit file, reloads system manager configuration making systemd aware of the changes.
```
$ systemctl daemon-reload - running this command reloads system manager configuration making systemd aware of the changes.
```
You can directly edit the unit service file. Units edit this way apply the changes right away with out running daemon-reload
```
$ systecmctl edit project-mercury.service --full
```

Manage state
```
$ systemctl get-default - shows current run target
$ systemctl set-default mutli-user.target - change run targets
$ systemctl list-units --all
```
To see only active units run
```
$ systemctl list-units
```
  
journalctl useful trouble shooting any serviceschecks journal or log enteries
```
$ journalctl
$ journalctl -b - logs from current boot
$ journalctl -u docker.service - logs from a particular service
```
                
The data from the service logs file can help with fixing a problem with a service
