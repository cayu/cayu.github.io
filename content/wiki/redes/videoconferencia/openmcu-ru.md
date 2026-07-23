---
title: "OpenMCU-ru"
date: 2017-11-24T11:30:09-03:00
---

## TIPS

Para modificar la estética del titulo de cada video deberemos modificar el siguiente archivo *openmcu-ru/conference.h*

```cpp>
===== Contribs =====
==== Script de inicio y de monitoreo en caso de caída ====
I threw this service monitoring script together because occasionally the OpenMCU-ru process in the developmental versions can crash out on my Ubuntu Server from time to time, so I wanted it to auto-restart. The script could easily be modified and used for any other service you like, but it was written specifically for OpenMCU-ru and Debian based operating systems.

There are two pieces to this puzzle (which makes for an awfully pathetic and simplistic puzzle), the “/etc/init.d/mcu-monitor” startup script to run the monitoring script as a daemon and the “/usr/local/bin/mcu-monitor.sh” monitoring script itself, both are fairly simple and straight forward.

As usual, to run this service at startup you can issue:
<code>update-rc.d mcu-monitor defaults
```

```bash
#!/bin/bash
# OpenMCU-ru process monitor startup script
# Author: Nathan Thomas
# Date: 04/14/2015
#
### BEGIN INIT INFO
# Provides:          mcu-monitor
# Required-Start:    $network $syslog $named $local_fs $remote_fs
# Required-Stop:     $network $syslog $named $local_fs $remote_fs
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# X-Interactive:     true
# Short-Description: Start/stop OpenMCU-ru process monitor daemon
### END INIT INFO

PROG=mcu-monitor
DESC="Auto restarts the OpenMCU-ru service upon failure"
HOME_DIR=/usr/local/bin
DAEMON=${HOME_DIR}/${PROG}.sh
PIDFILE=/var/run/${PROG}.pid

pscheck() {  # return number of processes
	echo "$( ps aux | grep "${PROG}.sh" | grep -cv "grep" 2>&1 )"
	return 0
}

pidsmatch() {  # do the curtains match the drapes?
        if [ -e $PIDFILE ] ; then  # if pidfile exists
		if [ "$( pscheck )" -eq "1" ] ; then  # if num procs=1
			if [ "$( ps aux | grep "${PROG}.sh" | grep -v "grep" | awk '{ print $2 }' )" -eq "$( cat ${PIDFILE} )" ] ; then  # compare pids
				echo "yes"
				return 0
			fi
		elif [ "$( pscheck )" -gt "1" ] ; then
			return 1  # Hmm...multiple procs, we've got a problem
		else
			echo "no"
			return 0
		fi
	else
		return 1
	fi
}

rmpid() {  # say bye bye to mr piddlesworth
	if [ -e $PIDFILE ] ; then
		rm -f $PIDFILE 2>&1
                if [ "$?" -ne "0" ] ; then
                        return 1
                fi
	fi
	return 0
}

killpids() {  # if procCount>=1, squash em
        if [ "$( pscheck )" -ge "1" ] ; then
                kill $( pgrep "${PROG}.sh" 2>&1 ) 2>&1
		if [ "$?" -ne "0" ] ; then
			return 1
		fi
        fi
	return 0
}

start() {
	rmpid  # dump the pidfile
	killpids  # not kids
	# Start the service
	echo -n $"Starting ${PROG} service: "
	start-stop-daemon --start --chuid root --pidfile $PIDFILE --chdir $HOME_DIR --background --make-pidfile --exec $DAEMON || return 1 2>&1
	echo $"OK"
	return 0
}

stop() {
	echo -n $"Stopping ${PROG} service: "
        start-stop-daemon --stop --pidfile $PIDFILE || return 1 2>&1
	rmpid  # dump the pidfile
	if [ "$?" -eq "0" -a "$( pscheck )" -ne "0" ] ; then  # if pidfile is gone and somehow procs still alive
		killpids  # not kids
		if [ "$?" -ne "0" ] ; then
			echo $"FAILED"
			return 1
		fi
	fi
	echo $"OK"
	return 0
}

restart() {
        stop && start
}

status() {
	if [ "$( pscheck )" -eq "1" -a "$( pidsmatch )" == "yes" ] ; then
                echo $"${PROG} (pid $( cat $PIDFILE )) is running"
	elif [ "$( pscheck )" -eq "1" -a "$( pidsmatch )" == "no" ] ; then
                echo $"Strange, ${PROG} is running, but PID doesn't match pidfile, fixing..."
		restart
	elif [ "$( pscheck )" -gt "1" ] ; then
                echo $"Multiple instances of ${PROG} running, fixing..."
		restart
	else
                echo $"${PROG} is stopped"
        fi
	return 0
}

case "$1" in
        'start')
		start
		;;
        'stop')
		stop
                ;;
        'restart')
                restart
                ;;
        'status')
		status
                ;;
        *)
                echo $"Usage: $0 {start|stop|restart|status}"
                exit 1
                ;;
esac
exit $?
```

```bash
#!/bin/bash
# Auto restart OpenMCU-ru when it crashes
# Author: Nathan Thomas
# Date: 04/14/2015

while true
do
	unset var
	var=$( ps aux | grep "openmcu-ru" | grep -cv "grep" 2>&1 )
	if [ "${var}" -eq "1" ] ; then
		# It's aliiiiiiive...do nothing (bitchslap)
		:
	elif [ "${var}" -gt "1" ] ; then
		# Hmm, multiple instances but why? Comment out if your debugging OpenMCU
		# and there's a legitimate reason to have multiple  processes running simultaneously
		# Otherwise, Squish bug!
		kill $( pgrep "openmcu-ru" 2&>1 ) 2&>1
		/usr/bin/service openmcu-ru start 2>&1
	else
		# Balls, it's dead
		/usr/bin/service openmcu-ru start 2>&1
	fi
	sleep .5
done
```

<http://www.paperstreetonline.com/2015/04/16/bash-script-openmcu-ru-service-monitor-or-monitor-and-automatically-restart-a-service-upon-failure/>
