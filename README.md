# KodeKloud Linux Basics: Service Management with SYSTEMD

[KodeKloud Linux Basics Course Notes Table of Contents](https://github.com/pslucas0212/LinuxBasics)

### SYSTEMD and Services

You can use a shell script during a boot process to be a systemd service.  In this example we want to start a python script after the PostgresDB is up and running.  We created a service account for the systemd service to run under - project_mercury.  

If the application fails it will try to restart automatically and if there is a failure the app will try to restart after 10 seconds.  If doen't restart after 10 seconds then it won't start up.  All services events will be logged and the the app will load when booting into Graphical mode (traget).

To run in the background will we neet to create a service unit file.  In this example we call it project-mercury.service.
```
/etc/systemd/system/project-mercury-service
```
File contents
```
[Service]
ExecStart=
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
                
