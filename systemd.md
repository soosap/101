
# systemd

Ubuntu switched to `systemd as its service framework starting in 15.04. The recommended practice is to change your `upstart` jobs to `systemd` jobs.

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

# Check the status of the consul startup script.
# Useful for debugging; spits out logs.
$ systemctl status consul

```