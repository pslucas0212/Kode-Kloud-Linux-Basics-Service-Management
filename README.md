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


#### SYSTEMD Tools
Two major tools are systemctl and jorunalctl
  
sytemctl can 
- manage system state
- start, stop, restart, reload
- enable/disable
- list and manage units
- list and update targets
  
                             
                             
systemctl commands
```
$ systemctl start docker
$ systemctl stop docker
$ systemctl reastart docker
$ systemctl reload docker
$ systemctl enable docker
$ systemctl disable docker
$ systemctl status docker
$ systemctl daemon-reload
$ systecmctl edit project-mercury.service --full
```
Manage state
```
$ systemctl get-default
$ systemctl set-default mutli-user.target
$ systemctl list-units --all
```
  
journalctl - checks journal or log enteries
```
$ journalctl
$ journalctl -b
$ journalctl -u docker.service
```
                
