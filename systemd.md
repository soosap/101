
# systemd

Ubuntu switched to `systemd` as its service framework starting in 15.04. The recommended practice is to change your `upstart` jobs to `systemd` jobs.

## systemd-analyze
```sh
# Use systemd-analyse to see how long the system took to startup
$ systemd-analyze

# Further allocate the startup time to individual services
$ systemd-analyze blame
```

## systemctl
```sh
# Use plain systemctl to list all available services
$ systemctl

# Ensure consul server started/not started on system boot
$ systemctl enable consul
$ systemctl disable consul

# Manually start consul server
$ systemctl start consul

# Check status of consul service
$ systemctl status consul

# More thorough logs on a particular unit can be obtained using journalctl
$ journalctl -u consul

# Show the 100 latest lines of logs of the consul service
$ journalctl -an100 -u consul

# Show all journal logs in reverse order (latest first)
$ journalctl -xe

# Show all readable properties of the service
$ sytemctl show consul
```


## Write systemd custom service
One can write custom systemd services that can be started, stopped, or started on boot (enable). An example would be a service to manage consul.

In the particular case below we wait until the network section is booted and available. We then use an external `consul.start.sh` script to bring up the service. If it is just a one-liner one could directly specify the startup command through `ExecStart`.

```
[Unit]
Description=Consul Agent
Requires=network-online.target
After=network-online.target

[Service]
Environment="GOMAXPROCS=`nproc`"
Restart=on-failure
User={{ consul_user }}
Group={{ consul_group }}
ExecStart=/tmp/consul.start.sh
ExecReload=/bin/kill -HUP $MAINPID
KillSignal=SIGINT

[Install]
WantedBy=multi-user.target
```

### Machine ip or hostname in systemd service
Integrating the ip or the hostname of the machine into a systemd service file is quite a hustle.

Check this [link](http://superuser.com/questions/968561/how-to-get-the-machine-ip-address-in-a-systemd-service-file) for reference.

### Environment directive
Environment variables can be specified in a static form via:
`Environment="DATACENTER=ffm"`

They can also be dynamically set using systemctl's 'set-environment' as mentioned [here](https://gist.github.com/nickjacob/9909574):
```
[Service]
ExecStartPre=/usr/bin/bash -c "/usr/bin/systemctl set-environment MYVAR=$(( 2 + 2 ))"
ExecStart=/usr/bin/ech "2 + 2 = ${MYVAR}"
```



