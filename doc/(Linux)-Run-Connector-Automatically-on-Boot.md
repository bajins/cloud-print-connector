# Systems using Systemd (including Ubuntu 15 and later, and Raspbian)

It is assumed that:

1. system user/group `cloud-print-connector` exists
2. `gcp-cups-connector` is located at `/opt/cloud-print-connector/gcp-cups-connector`
3. `cloud-print-connector` user has execute rights to `/opt/cloud-print-connector/gcp-cups-connector`
4. `gcp-cups-connector.config.json` is located at `/opt/cloud-print-connector/gcp-cups-connector.config.json`
5. `cloud-print-connector` user has read rights to `/opt/cloud-print-connector/gcp-cups-connector.config.json`

Start with the [example systemd service file](https://github.com/google/cloud-print-connector/blob/master/systemd/cloud-print-connector.service) and install it:

    wget https://raw.githubusercontent.com/google/cloud-print-connector/master/systemd/cloud-print-connector.service

    sudo install -o root -m 0664 cloud-print-connector.service /etc/systemd/system

Enable Google Cloud Print CUPS Connector service:

    sudo systemctl enable cloud-print-connector.service

Start Google Cloud Print CUPS Connector service:

    sudo systemctl start cloud-print-connector.service

Check the status of the Google Cloud Print CUPS Connector service:

    sudo systemctl status cloud-print-connector.service

Stop the Google Cloud Print CUPS Connector service:

    sudo systemctl stop cloud-print-connector.service

# Systems using Upstart (including Ubuntu 14 and earlier)
Create an unprivileged `cloud-print-connector` system user:

    sudo useradd -s /usr/sbin/nologin -r -M cloud-print-connector

Change the owner of the binaries to gcp:

    sudo chown cloud-print-connector:cloud-print-connector /opt/cloud-print-connector/gcp-cups-connector
    sudo chown cloud-print-connector:cloud-print-connector /opt/cloud-print-connector/gcp-connector-util

Change the owner of the gcp config to cloud-print-connector:

    sudo chown cloud-print-connector:cloud-print-connector /opt/cloud-print-connector/gcp-cups-connector.config.json

Create a file `/etc/init/gcp.conf` with the following contents:

```sh
description "Google Cloud Print daemon to forward requests to CUPS"
author      "LinuxLover <linux@klomp.ca>"

start on (local-filesystems and net-device-up and started cups)
stop on runlevel [!2345]

respawn
exec su -l -s /bin/sh -c "/opt/cloud-print-connector/gcp-cups-connector -config-filename /opt/cloud-print-connector/gcp-cups-connector.config.json" cloud-print-connector
```

# Other Systems

Add the following lines to `/etc/rc.local` just before the `exit 0` line at the end.  Note that this assumes gcp-cups-connector is installed at /home/pi/cups-connector/gcp-cups-connector.

```sh
# CUPS Connector:
#   sleep:                                      hack to wait for the network interface to come up
#   su ... pi:                                  run as user "pi"
#   --login:                                    environment similar to "pi" instead of "root"
#   --command:                                  run this thing
#   /home/pi/cups-connector/gcp-cups-connector: thing to run
#   &:                                          run the command "in the background"
sleep 60
su --login --command /home/pi/cups-connector/gcp-cups-connector pi &
```

# Debian init.d aka start-stop-daemon

An example startup script as below should work well in SysV init.d/ folder.
Assumptions 2 and 4 differ in location of binaries and config files:

2. binary is located at `/usr/local/bin/gcp-cups-connector`

4. config file is located at `/etc/gcp/gcp-cups-connector.config.json` 

A pid file is created at the canonical location /var/run/gcp-cups-connector/gcp-cups-connector.pid .

```
#! /bin/sh
### BEGIN INIT INFO
# Provides:          cloud-print-connector
# Required-Start:    $syslog
# Required-Stop:     $syslog
# Should-Start:      $network avahi-daemon
# Should-Stop:       $network
# X-Start-Before:
# X-Stop-After:
# Default-Start:     2 3 4 5
# Default-Stop:      1
# Short-Description: CUPS Cloud Print Connector
# Description:       Poll for Cloud Print Jobs
### END INIT INFO

# Author: Ulrich Hahn

PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
OPTS='--config-filename /etc/gcp/gcp-cups-connector.config.json'
DAEMON=/usr/local/bin/gcp-cups-connector
NAME=gcp-cups-connector
PIDFILE=/var/run/$NAME/$NAME.pid
DESC="Google Cloud Print Connector"
SCRIPTNAME=/etc/init.d/cloud-print-connector
USER=cloud-print-connector

unset TMPDIR

# Exit if the package is not installed
test -x $DAEMON || exit 0

. /lib/lsb/init-functions

# Get the timezone set.
if [ -z "$TZ" -a -e /etc/timezone ]; then
    TZ=`cat /etc/timezone`
    export TZ
fi


case "$1" in
  start)
        log_daemon_msg "Starting $DESC" "$NAME"

        mkdir -p `dirname "$PIDFILE"`

        start-stop-daemon --make-pidfile --background --chuid $USER --start --oknodo --pidfile "$PIDFILE" --exec $DAEMON --\
 $OPTS
        status=$?
        log_end_msg $status
        ;;
  stop)
        log_daemon_msg "Stopping $DESC" "$NAME"
        start-stop-daemon --stop --quiet --retry 5 --oknodo --exec $DAEMON
        status=$?
        log_end_msg $status
        ;;
  reload|force-reload)
       log_daemon_msg "Reloading $DESC" "$NAME"
       start-stop-daemon --stop --quiet --exec $DAEMON --signal 1
       status=$?
       log_end_msg $status
       ;;
  restart)
        log_daemon_msg "Restarting $DESC" "$NAME"
        if start-stop-daemon --stop --quiet --retry 5 --oknodo --exec $DAEMON -- $OPTS; then
                start-stop-daemon --chuid $USER --start --quiet --pidfile "$PIDFILE" --exec $DAEMON $OPTS &
        fi
        status=$?
        log_end_msg $status
        ;;
  status)
        status_of_proc -p "$PIDFILE" "$DAEMON" "$NAME" && exit 0 || exit $?
        ;;
  *)
        echo "Usage: $SCRIPTNAME {start|stop|restart|force-reload|status}" >&2
        exit 3
        ;;
esac

exit 0
```