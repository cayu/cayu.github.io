---
title: "Dump some hardware info in DokuWiki format"
date: 2009-08-10T12:12:33-03:00
---

## Dump some hardware info in DokuWiki format

Script (sys2wiki.sh) um Hardware Infos unter linux in DokuWiki kompatible format auszulesen.

```
#!/bin/bash

#make sure tools work as expected
LANG=C

#this specifies which Headline level is used
HL='===='

CPU=`cat /proc/cpuinfo  | grep 'model name' | awk -F\: '{print $2}'| uniq | sed -e 's/ //'`
MHz=`cat /proc/cpuinfo | grep 'cpu MHz' | awk -F\: '{print $2}'| uniq | sed -e 's/ //'`
CPUCOUNT=`cat /proc/cpuinfo | grep ^processor | wc -l`
RAM=`cat /proc/meminfo | grep MemTotal | awk -F\: '{print $2}' | awk -F\  '{print $1 " " $2}'`
SWAP=`cat /proc/meminfo | grep SwapTotal | awk -F\: '{print $2}' | awk -F\  '{print $1 " " $2}'`
SYSTEM=`uname -sr`
HOSTNAME=`(hostname -f || hostname) 2>/dev/null`

# look for known Distributions
if [ -e /etc/debian_version ]; then
  OS="Debian `cat /etc/debian_version`"
elif [ -e /etc/redhat-release ]; then
  OS=`cat /etc/redhat-release`
elif [ -e /etc/SuSE-release ]; then
  OS=`cat /etc/SuSE-release |head -n1`
elif [ -e /etc/gentoo-release ]; then
  OS=`< /etc/gentoo-release`
elif [ -e /etc/slackware-version ]; then
  OS=`< /etc/slackware-version`
 else
  OS='unknown'
fi

echo "
$HL General $HL

^ Hostname | $HOSTNAME |
^ CPU      | $CPU      |
^ MHz      | $MHz      |
^ # CPU    | $CPUCOUNT |
^ RAM      | $RAM      |
^ Swap     | $SWAP     |
^ System   | $SYSTEM   |
^ OS       | $OS       |
"

echo -e "$HL Network $HL\n"
for DEV in `/sbin/ifconfig -a | grep '^\w'| awk '!/lo/{print $1}'`
do
  IP=`/sbin/ifconfig $DEV | awk -F\: '/inet / {print $2}'| awk '{print $1}'`
  echo "^ $DEV | $IP |"
done
echo

echo -e "$HL PCI $HL\n"
#lspci |sed 's/^/  /'
echo "^  Bus Address  ^  Device Class  ^  Details  ^"
/sbin/lspci |sed 's/\([0-9]\{2\}:[0-9a-z]\{2\}.[0-9]\)\([a-zA-Z0-9() ]\+\):\(.*\)$/|  \1  | \2  |  \3  |/g'
echo

echo -e "$HL Filesystems $HL\n"
df -hPT -x tmpfs | awk '{print "| " $1 " | " $2 " | " $3 " | " $7 " |"}'
echo

echo -e "$HL IDE devices $HL\n"

for DEV in `ls -1d /proc/ide/hd* | sed 's/.*\///'`
do
  MODEL=`cat /proc/ide/$DEV/model`
  if [ -e /proc/ide/$DEV/capacity ]; then
    SIZE=`cat /proc/ide/$DEV/capacity`
    SIZE=`expr $SIZE / 2097152`
  else
    if [ -e /sys/block/$DEV/size ]; then
      SIZE=`cat /sys/block/$DEV/size`
      SIZE=`expr $SIZE / 2097152`
    else
      SIZE='(unknown)'
    fi
  fi

  echo "| /dev/$DEV | $MODEL | $SIZE GB |"
done

if [ "$(ls -1d /sys/block/sd* 2> /dev/null)" ]; then
  echo -e "$HL SCSI devices $HL\n"
  for DEV in `ls -1d /sys/block/sd* |sed 's/.*\///'`
  do
    MODEL=`cat /sys/block/$DEV/device/model`
    SIZE=`cat /sys/block/$DEV/size`
    SIZE=`expr $SIZE / 2097152`

    echo "| /dev/$DEV | $MODEL | $SIZE GB |"
  done
  echo
fi
```

---
Hardware Infos unter AIX (getestet unter AIX 5.1)

```
#!/bin/ksh

#make sure tools work as expected
LANG=C

#this specifies which Headline level is used
HL='===='

#CPU
CPU_NAME=$(/usr/sbin/lsdev -C | grep Available | grep -i proc | awk '{print $1}')
CPU=`/usr/sbin/lsattr -l $CPU_NAME -E | grep type | awk '{print $2}'`

#MHz
MHz=`/usr/sbin/lsattr -l $CPU_NAME -E | grep frequency | awk '{print $2/1000000}'`
CPUCOUNT=`/usr/sbin/lsdev -C | grep Available | grep -i proc | awk '{print $1}' | wc -l`

#RAM
MEM_NAME=$(/usr/sbin/lsdev -C | grep Available | grep -i mem | awk '{print $1}')
RAM=`/usr/sbin/lsattr -l $MEM_NAME -E | grep Total | awk '{print $2}'`
# SWAP
SWAP=0
for i in $(/usr/sbin/lsvg -o)
 do
	if lsvg -l $i | grep paging 2>&1 1>/dev/null
	 then
		PAGEDEV=$(lsvg -l $i | grep paging | awk '{print $1}')
		PP_SIZE=$(lslv $PAGEDEV | grep "PP SIZE" | awk '{print $6}')
		LP_COUNT=$(lslv $PAGEDEV | grep "^LPs" | awk '{print $2}')
		let SWAP=$SWAP+$PP_SIZE*$LP_COUNT
	fi
done
OS=`printf "$(/usr/bin/uname) $(/usr/bin/oslevel)"` # erledigt
ML=`/usr/bin/oslevel -r | awk -F- '{print $2}'`
HOSTNAME=`/usr/bin/hostname`

echo "
$HL General $HL

^ Hostname | $HOSTNAME |
^ CPU      | $CPU      |
^ MHz      | $MHz      |
^ # CPU    | $CPUCOUNT |
^ RAM      | $RAM      |
^ Swap     | $SWAP     |
^ OS       | $OS       |
^ Maintenance-Level       | $ML       |
"

echo "$HL Network $HL\n"
for DEV in `/usr/sbin/ifconfig -a | egrep -v "^lo|inet|ether|sendspace" | awk '{print $1}' | sed -e 's/://' | uniq`
do
  IP=`/usr/sbin/ifconfig $DEV | grep inet | awk '{print $2}'`
  echo "^ $DEV | $IP |"
done
echo

echo "$HL Devices $HL\n"
echo "^  Name  ^  Slot  ^  Description  ^"
/usr/sbin/lsdev -C -S Available -F "name location description" |sort | sed -e '/^[ ]*$/d' | awk -F' \{2,\}' '{print "| " $1 " | " $2 " | " $3 " |"}'
echo

echo "$HL Volumegroups & Filesystems $HL\n"
echo "^ Volumegroup ^ Filesystem ^ Size (in KB) ^ Mountpoint ^"
for i in $(/usr/sbin/lsvg -o)
 do
	for j in $(lsvg -l $i | egrep -v "$i:|TYPE|closed|paging|jfslog|jfs2log" | awk '{print $1}')
	 do
		echo "| $i $(df -k | grep "$j " | awk '{print "| " $1 " | " $2 " | " $NF " |"}')"
	done
done
echo
```

---
Hardware Infos unter Solaris (getestet unter Solaris 9/10 SPARC)

```
#!/bin/bash

#make sure tools work as expected
LANG=C

#this specifies which Headline level is used
HL='===='

CPU=`/usr/bin/kstat cpu_info | grep implementation | sed -e 's/\([^ *]implementation[ *]\)//g' | sed -e 's/[ ]\{2,\}//g' |  sed -n '1p'` # erledigt
MHz=`/usr/bin/kstat cpu_info | grep clock_MHz | sed -e 's/\([^ *]clock_MHz[ *]\)//g' | sed -e 's/[ ]\{2,\}//g' | sed -n '1p'` # erledigt
CPUCOUNT=`/usr/bin/kstat cpu_info | grep implementation | wc -l` # erledigt
RAM=`/usr/sbin/prtconf | grep Memory | awk '{print $3*1024}'` # erledigt
SWAP=`SWAP=0; for i in $(/usr/sbin/swap -l | egrep -v swapfile | awk '{print $5}'); do let SWAP=$SWAP+$i; done; echo $SWAP` # erledigt
OS=`uname -sr` # erledigt
HOSTNAME=`/usr/bin/hostname`

echo "
$HL General $HL

^ Hostname | $HOSTNAME |
^ CPU      | $CPU      |
^ MHz      | $MHz      |
^ # CPU    | $CPUCOUNT |
^ RAM      | $RAM      |
^ Swap     | $SWAP     |
^ OS       | $OS       |
"

echo -e "$HL Network $HL\n"
for DEV in `/usr/sbin/ifconfig -a | egrep -v "^lo|inet|ether" | awk '{print $1}' | sed -e 's/://' | uniq`
do
  IP=`/sbin/ifconfig $DEV | grep inet | awk '{print $2}'`
  echo "^ $DEV | $IP |"
done
echo

echo -e "$HL Bus $HL\n"
echo "^  Board  ^  Bus-Type  ^  MHz  ^  Slot  ^  Name  ^  Model  ^"
/usr/sbin/prtdiag | sed -n -e '/^     Bus/,/======/ p' | egrep -v "Freq|---|System|===|Slot" | sed -e '/^[ ]*$/d' | awk -F' \{2,\}' '{print "| " $1 " | " $2 " | " $3 " | " $4 " | " $5 " | " $NF " |"}'
echo

echo -e "$HL Filesystems $HL\n"
/usr/bin/df -h | egrep -v "/proc|mnttab|/dev/fd|swap|/devices|ctfs|objfs" | awk '{print "| " $1 " | " $2 " | " $6 " |"}'
echo
```

## Beispiel

Kann man z.B wie folgt aufrufen

```
fw-failover-2 tmp # ./sys2wiki.sh > fw-failover-2.txt
```

und das ganze sieht dan so aus

---
### General

| Hostname | fw-failover-2.mustermann.lan      |
|----------|-----------------------------------|
| CPU      | Intel(R) Pentium(R) 4 CPU 2.80GHz |
| MHz      | 2800.662                          |
| \# CPU   | 1                                 |
| RAM      | 510068 kB                         |
| Swap     | 1000312 kB                        |
| System   | Linux 2.6.14-hardened-r5          |
| OS       | Gentoo Base System version 1.6.14 |

### Network

| eth0   | 192.168.10.241  |
|--------|-----------------|
| eth0:0 | 192.168.10.4    |
| eth1   | 212.xx.xxx.xxx  |
| eth2   | 192.168.100.1   |
| eth3   | 212.xx.xxx.xx   |
| ippp0  | 192.168.200.100 |
| ippp1  | 192.168.0.153   |
| ippp2  | 192.168.5.100   |
| ipsec0 | 212.xx.xxx.xx   |
| ipsec1 | 212.xx.xxx.xxx  |

### PCI

| Bus Address | Device Class | Details |
|:--:|:--:|:--:|
| 00:00.0 | Host bridge | Intel Corporation 82845G/GL\[Brookdale-G\]/GE/PE DRAM Controller/Host-Hub Interface (rev 03) |
| 00:01.0 | PCI bridge | Intel Corporation 82845G/GL\[Brookdale-G\]/GE/PE Host-to-AGP Bridge (rev 03) |
| 00:1d.0 | USB Controller | Intel Corporation 82801DB/DBL/DBM (ICH4/ICH4-L/ICH4-M) USB UHCI Controller \#1 (rev 02) |
| 00:1d.1 | USB Controller | Intel Corporation 82801DB/DBL/DBM (ICH4/ICH4-L/ICH4-M) USB UHCI Controller \#2 (rev 02) |
| 00:1d.2 | USB Controller | Intel Corporation 82801DB/DBL/DBM (ICH4/ICH4-L/ICH4-M) USB UHCI Controller \#3 (rev 02) |
| 00:1d.7 | USB Controller | Intel Corporation 82801DB/DBM (ICH4/ICH4-M) USB2 EHCI Controller (rev 02) |
| 00:1e.0 | PCI bridge | Intel Corporation 82801 PCI Bridge (rev 82) |
| 00:1f.0 | ISA bridge | Intel Corporation 82801DB/DBL (ICH4/ICH4-L) LPC Interface Bridge (rev 02) |
| 00:1f.1 | IDE interface | Intel Corporation 82801DB (ICH4) IDE Controller (rev 02) |
| 00:1f.3 | SMBus | Intel Corporation 82801DB/DBL/DBM (ICH4/ICH4-L/ICH4-M) SMBus Controller (rev 02) |
| 01:00.0 | VGA compatible controller | nVidia Corporation NV11 \[GeForce2 MX/MX 400\] (rev b2) |
| 02:05.0 | Ethernet controller | Realtek Semiconductor Co., Ltd. RTL-8139/8139C/8139C+ (rev 10) |
| 02:09.0 | Ethernet controller | 3Com Corporation 3c905C-TX/TX-M \[Tornado\] (rev 78) |
| 02:0b.0 | Ethernet controller | 3Com Corporation 3c905C-TX/TX-M \[Tornado\] (rev 78) |
| 02:0c.0 | Ethernet controller | 3Com Corporation 3c905C-TX/TX-M \[Tornado\] (rev 78) |
| 02:0d.0 | Ethernet controller | 3Com Corporation 3c905C-TX/TX-M \[Tornado\] (rev 78) |
| 02:0e.0 | Network controller | AVM Audiovisuelles MKTG & Computer System GmbH Fritz!PCI v2.0 ISDN (rev 02) |

### Filesystems

|            |          |      |         |
|------------|----------|------|---------|
| Filesystem | Type     | Size | Mounted |
| /dev/md/1  | reiserfs | 1.9G | /       |
| /dev/md/0  | reiserfs | 96M  | /boot   |
| /dev/md/2  | reiserfs | 2.4G | /usr    |
| /dev/md/4  | reiserfs | 3.8G | /var    |
| /dev/drbd0 | reiserfs | 9.6G | /shared |

### IDE devices

|          |                          |       |
|----------|--------------------------|-------|
| /dev/hda | WDC WD400BB-00JHA0       | 37 GB |
| /dev/hdc | ST320413A                | 18 GB |
| /dev/hdd | TSSTcorpDVD-ROM SH-D162C | 0 GB  |

---
Originalscript zu finden unter <http://wiki.splitbrain.org/sys2wiki.sh>\
Denn “lspci” ausgabe abschnitt habe ich bischen angepasst.

Die Ausgabe für den Bereich AIX & Solaris sieht entsprechend der unterschiedlichen Systemarchitektur und Betriebssystemmittel anders aus als der Linux-Bereich. Der Aufruf und die Verarbeitung funktioniert aber analog.

## Original

Extraido de <http://www.nagios-wiki.de/nagios/howtos/wiki_hardware>
