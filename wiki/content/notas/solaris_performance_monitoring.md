---
title: "Solaris Performance Monitoring & Tuning - iostat , vmstat & netstat"
date: 2013-12-12T16:49:19-03:00
---

<sub>Original: <http://www.adminschoice.com/docs/iostat_vmstat_netstat.htm></sub>

## Introduction to iostat , vmstat and netstat

This document is primarily written with reference to solaris performance monitoring and tuning but these tools are available in other unix variants also with slight syntax difference.

iostat , vmstat and netstat are three most commonly used tools for performance monitoring . These comes built in with the operating system and are easy to use .iostat stands for input output statistics and reports statistics for i/o devices such as disk drives . vmstat gives the statistics for virtual Memory and netstat gives the network statstics .

## Input Output statistics ( iostat )

iostat reports terminal and disk I/O activity and CPU utilization. The first line of output is for the time period since boot & each subsequent line is for the prior interval . Kernel maintains a number of counters to keep track of the values.

iostat’s activity class options default to tdc (terminal, disk, and CPU). If any other option/s are specified, this default is completely overridden i.e. iostat -d will report only statistics about the disks.

### Syntax

**`iostat <options> interval count`**

<table>
<tbody>
<tr>
<td style="text-align: left;">option</td>
<td style="text-align: left;">let you specify the device for which information is needed like <strong>disk ( `-d` ) , cpu ( `-c` ) or terminal ( `-t` )</strong>.<br />
<strong>`-x`</strong> option gives the <strong>extended statistics</strong>.</td>
</tr>
<tr>
<td style="text-align: left;">interval</td>
<td style="text-align: left;">is time period in seconds between two samples. <strong>`iostat 4`</strong> will give data at each 4 seconds interval.</td>
</tr>
<tr>
<td style="text-align: left;">count</td>
<td style="text-align: left;">is the number of times the data is needed. <strong>`iostat 4 5`</strong> will give data at 4 seconds interval 5 times</td>
</tr>
</tbody>
</table>

### Example

```bash>
The fields have the following meanings:
^ disk  | name of the disk  |
^ r/s   | reads per second  |
^ w/s   | writes per second  |
^ Kr/s  | kilobytes read per second  |
^ Kw/s  | kilobytes written per second  |
^ wait  | average number of transactions waiting for service (Q length)  |
^ actv  | average number of transactions  actively being serviced (removed from the queue but not yet completed)  |
^ %w    | percent of time there are transactions waiting for service (queue non-empty)  |
^ %b    | percent of time the disk is busy (transactions in progress)  |

==== Results and Solutions: ====

The values to look from the iostat output  are:
  * Reads/writes  per second (r/s , w/s)
  * Percentage busy (%b)
  * Service time (svc_t)

If a disk shows consistently high reads/writes along with, the percentage busy (%b) of the disks is greater than 5 percent, and the average service time  (svc_t) is greater than 30 milliseconds, then one of the following action needs to be taken

  - Tune the application to use disk i/o more efficiently by modifying the disk queries and using available cache facilities of application servers.
  - Spread the file system of the disk on to two or more disk using disk striping feature of volume manager / disksuite etc.
  - Increase the system parameter values for inode cache, ufs_ninode, which is Number of inodes to be held in memory. Inodes are cached globally (for UFS), not on a per-file system basis 
  - Move the file system to another faster disk / controller or replace existing disk / controller to a faster one.
~~UP~~

===== Virtual Memory Statistics ( vmstat ) =====

vmstat - vmstat reports virtual memory statistics of process, virtual memory, disk, trap, and CPU activity.

On multicpu systems, vmstat averages the number of CPUs into the output. For per-process statistics. Without options, vmstat displays a one-line summary of the virtual memory activity since the system was booted.

==== Syntax ====

**''vmstat <options> interval count''**

| option  | let you specify the type of information needed such as **paging ( ''-p'' ) , cache ( ''-c'' ) , interrupt ( ''-i''    )** etc.\\ if no option is specified, information about process, memory, paging, disk, interrupts & cpu  is displayed.  |
| interval  | is time period in seconds between two samples. **''vmstat 4''** will give data at each 4 seconds interval.  |
| count  | is the number of times the data is needed. **''vmstat 4 5''** will give data at 4 seconds interval 5 times.  |

==== Example ====

The following command displays a summary of what the system is doing every five seconds.
<code bash>
example% vmstat 5

procs  memory          page             disk      faults        cpu
r b w swap  free re mf pi p fr de sr s0 s1 s2 s3  in  sy  cs us sy id
0 0 0 11456 4120 1  41 19 1  3  0  2  0  4  0  0  48 112 130  4 14 82
0 0 1 10132 4280 0   4 44 0  0  0  0  0 23  0  0 211 230 144  3 35 62
0 0 1 10132 4616 0   0 20 0  0  0  0  0 19  0  0 150 172 146  3 33 64
0 0 1 10132 5292 0   0  9 0  0  0  0  0 21  0  0 165 105 130  1 21 78
```

The fields of vmstat’s display are

| procs |  |
|:---|----|
| r | in run queue |
| b | blocked for resources I/O, paging etc. |
| w | swapped |
|  |  |
| memory (in Kbytes) |  |
| swap | amount of swap space currently available |
| free | size of the free list |
|  |  |
| page (in units per second) |  |
| re | page reclaims - see -S option for how this field is modified. |
| mf | minor faults - see -S option for how this field is modified. |
| pi | kilobytes paged in |
| po | kilobytes paged out |
| fr | kilobytes freed |
| de | anticipated short-term memory shortfall (Kbytes) |
| sr | pages scanned by clock algorithm |
|  |  |
| disk (operations per second) |  |
|  | There are slots for up to four disks, labeled with a single letter and number. |
|  | The letter indicates the type of disk (**s = SCSI, i = IPI**, etc). The number is the logical unit number. |
|  |  |
| faults |  |
| in | (non clock) device interrupts |
| sy | system calls |
| cs | CPU context switches |
|  |  |
| cpu - breakdown of percentage usage of CPU time. On multiprocessors this is an average across all processors. |  |
| us | user time |
| sy | system time |
| id | idle time |

~~UP~~

### Results and Solutions:

#### A. CPU issues:

Following columns has to be watched to determine if there is any cpu issue

1.  Processes in the run queue (procs r)
2.  User time (cpu us)
3.  System time (cpu sy)
4.  Idle time (cpu id)

<!-- -->

       procs      cpu
       r b w    us sy  id
       0 0 0    4  14  82
       0 0 1    3  35  62
       0 0 1    3  33  64
       0 0 1    1  21  78

##### Problem symptoms:

1.  If the number of processes in run queue (procs r) are consistently greater than the number of CPUs on the system it will slow down system as there are more processes then available CPUs .
2.  if this number is more than four times the number of available CPUs in the system then system is facing shortage of cpu power and will greatly slow down the processess on the system.
3.  If the idle time (cpu id) is consistently 0 and if the system time (cpu sy) is double the user time (cpu us) system is facing shortage of CPU resources.

##### Resolution :

Resolution to these kind of issues involves tuning of application procedures to make efficient use of cpu and as a last resort increasing the cpu power or adding more cpu to the system.

#### B. Memory Issues:

Memory bottlenecks are determined by the scan rate (sr). The scan rate is the pages scanned by the clock algorithm per second. If the scan rate (sr) is continuously over 200 pages per second then there is a memory shortage.

##### Resolution :

1.  Tune the applications & servers to make efficient use of memory and cache.
2.  Increase system memory.
3.  Implement priority paging in s in pre solaris 8 versions by adding line “set priority paging=1” in `/etc/system`. Remove this line if upgrading from Solaris 7 to 8 & retaining old /etc/system file.

~~UP~~

## Network Statistics (netstat)

netstat displays the contents of various network-related data structures in depending on the options selected.

### Syntax:

**`netstat  <option/s> <interval>`**

multiple options can be given at one time.

| Options |  |
|:---|----|
| -a | displays the state of all sockets. |
| -r | shows the system routing tables |
| -i | gives statistics on a per-interface basis. |
| -m | displays information from the network memory buffers. On Solaris, this shows statistics for STREAMS |
| -p \[proto\] | retrieves statistics for the specified protocol |
| -s | shows per-protocol statistics. (some implementations allow -ss to remove fileds with a value of 0 (zero) from the display.) |
| -D | display the status of DHCP configured interfaces. |
| -n | do not lookup hostnames, display only IP addresses. |
| -d (with -i) | displays dropped packets per interface. |
| -I \[interface\] | retrieve information about only the specified interface. |
| -v | be verbose |
|  |  |
| interval | number for continuous display of statictics. |

### Example

```bash>
This shows the output on a Solaris machine who's IP address is 192.168.1.11 with a default router at 192.168.1.1
~~UP~~

==== Results and Solutions: ====

=== A. Network availability ===

The command as above is mostly useful in troubleshooting network accessibility issues . When outside network is not accessible from a machine check the following:
  - if the default router ip address is correct
  - you can ping it from your machine.
  - If router address is incorrect it can be changed with route add commnad. See man route for more info .

route command examples:
  $route add default <hostname>
  $route add 192.0.2.32  <gateway_name>

If the router address is correct but still you can't ping it, there may be some network cable /hub/switch problem and you have to try and eliminate the faulty component.

=== B. Network Response ===

<code bash>
$ netstat -i
Name 	Mtu 	Net/Dest 	Address 	Ipkts 	 Ierrs 	Opkts 	Oerrs 	Collis 	Queue
lo0 	8232 	loopback 	localhost 	77814 	 0 	77814 	0 	0 	0
hme0 	1500 	server1 	server1 	10658566 3 	4832511 0 	279257 	0
```

This option is used to diagnose the network problems when the connectivity is there but it is slow in response .

Values to look at:

- Collisions (Collis)
- Output packets (Opkts)
- Input errors (Ierrs)
- Input packets (Ipkts)

The above values will give information to workout

1.  Network collision rate as follows:\
    **Network collision rate = Output collision counts / Output packets**\
    ***Network-wide collision rate greater than 10 percent will indicate:***
- Overloaded network,
- Poorly configured network,
- Hardware problems.
2.  Input packet error rate as follows:\
    **Input Packet Error Rate = Ierrs / Ipkts**\
    ***If the input error rate is high (over 0.25 percent), the host is dropping packets.***\
    Hub/switch cables etc needs to be checked for potential problems.

~~UP~~

#### C. Network socket & TCP Cconnection state

Netstat gives important information about network socket and tcp state. This is very useful in finding out the open, closed and waiting network tcp connection .

Network states returned by netstat are following :

|              |                                                          |
|:-------------|:---------------------------------------------------------|
| CLOSED       | Closed. The socket is not being used.                    |
| LISTEN       | Listening for incoming connections.                      |
| SYN_SENT     | Actively trying to establish connection.                 |
| SYN_RECEIVED | Initial synchronization of the connection under way.     |
| ESTABLISHED  | Connection has been established.                         |
| CLOSE_WAIT   | Remote shut down; waiting for the socket to close.       |
| FIN_WAIT_1   | Socket closed; shutting down connection.                 |
| CLOSING      | Closed, then remote shutdown; awaiting acknowledgement.  |
| LAST_ACK     | Remote shut down, then closed ;awaiting acknowledgement. |
| FIN_WAIT_2   | Socket closed; waiting for shutdown from remote.         |
| TIME_WAIT    | Wait after close for remote shutdown retransmission.     |

Example:

```bash
#netstat -a

Local Address 	  Remote Address        Swind   Send-Q 	Rwind 	Recv-Q 	State 
*.* 	          *.* 	                0 	0 	24576 	0 	IDLE
*.22 	          *.* 	                0 	0 	24576 	0 	LISTEN
*.22 	          *.* 	                0 	0 	24576 	0 	LISTEN
*.* 	          *.* 	                0 	0 	24576 	0 	IDLE
*.32771 	  *.* 	                0 	0 	24576 	0 	LISTEN
*.4045 	          *.* 	                0 	0 	24576 	0 	LISTEN
*.25 	          *.* 	                0 	0 	24576 	0 	LISTEN
*.5987 	          *.* 	                0 	0 	24576 	0 	LISTEN
*.898 	          *.* 	                0 	0 	24576 	0 	LISTEN
*.32772 	  *.* 	                0 	0 	24576 	0 	LISTEN
*.32775 	  *.* 	                0 	0 	24576 	0 	LISTEN
*.32776 	  *.* 	                0 	0 	24576 	0 	LISTEN
*.* 	          *.* 	                0 	0 	24576 	0 	IDLE
192.168.1.184.22  192.168.1.186.50457 	41992 	0 	24616 	0 	ESTABLISHED
192.168.1.184.22  192.168.1.186.56806 	38912 	0 	24616 	0 	ESTABLISHED
192.168.1.184.22  192.168.1.183.58672 	18048 	0 	24616 	0 	ESTABLISHED
```

If you see a lots of connections in FIN_WAIT state tcp/ip parameters have to be tuned because the connections are not being closed and they gets accumulating. After some time system may run out of resource. TCP parameter can be tuned to define a time out so that connections can be released and used by new connection. ~~UP~~

## Next Steps

This article tried to cover the performance tuning aspects in unix. There are not many books available in this subject but I have found some good books which will prove to be helpful in performance management.

Following are the list of books recommended for performance tuning in unix, solaris, HPUX, AIX, Linux, oracle & SAP.

- [UNIX Performance Tuning; Sys Admin-Essential Reference Series](http://www.amazon.com/exec/obidos/ASIN/0879304707/adminschoicecom?dev-t=DBNSEW91FK8PS%26camp=2025%26link_code=xm2)
- [System Performance Tuning, 2nd Edition (O'Reilly System Administration)](http://www.amazon.com/exec/obidos/ASIN/059600284X/adminschoicecom?dev-t=DBNSEW91FK8PS%26camp=2025%26link_code=xm2)
- [Techniques for Optimizing Applications: High Performance Computing](http://www.amazon.com/exec/obidos/ASIN/0130934763/adminschoicecom?dev-t=DBNSEW91FK8PS%26camp=2025%26link_code=xm2)
- [Sun Performance and Tuning: Sparc & Solaris](http://www.amazon.com/exec/obidos/ASIN/0131496425/adminschoicecom?dev-t=DBNSEW91FK8PS%26camp=2025%26link_code=xm2)
- [Accelerating AIX : Performance Tuning for Programmers and Systems Administrators](http://www.amazon.com/exec/obidos/ASIN/0201633825/adminschoicecom?dev-t=DBNSEW91FK8PS%26camp=2025%26link_code=xm2)
- [AIX Performance Tuning Guide](http://www.amazon.com/exec/obidos/ASIN/0133867072/qid=1126933346/sr=1-28/ref=sr_1_28/002-1010807-1293633?v=glance&s=books)
- [HP-UX 11i Tuning and Performance (Hewlett-Packard Professional Books (Paperback))](http://www.amazon.com/exec/obidos/ASIN/0131433490/adminschoicecom?dev-t=DBNSEW91FK8PS%26camp=2025%26link_code=xm2)
- [HP-UX Tuning and Performance: Concepts, Tools and Methods](http://www.amazon.com/exec/obidos/ASIN/0131027166/adminschoicecom?dev-t=DBNSEW91FK8PS%26camp=2025%26link_code=xm2)
- [Linux Performance Tuning and Capacity Planning](http://www.amazon.com/exec/obidos/ASIN/0672320819/adminschoicecom?dev-t=DBNSEW91FK8PS%26camp=2025%26link_code=xm2)
- [Performance Tuning for Linux(R) Servers](http://www.amazon.com/exec/obidos/ASIN/013144753X/adminschoicecom?dev-t=DBNSEW91FK8PS%26camp=2025%26link_code=xm2)
- [Oracle Wait Interface: A Practical Guide to Performance Diagnostics & Tuning (Osborne ORACLE Press Series)](http://www.amazon.com/exec/obidos/ASIN/007222729X/adminschoicecom?dev-t=DBNSEW91FK8PS%26camp=2025%26link_code=xm2)
- [Oracle9i Performance Tuning Tips & Techniques](http://www.amazon.com/exec/obidos/ASIN/0072224738/adminschoicecom?dev-t=DBNSEW91FK8PS%26camp=2025%26link_code=xm2)
- [Oracle High Performance Tuning for 9i and 10g](http://www.amazon.com/exec/obidos/ASIN/1555583059/adminschoicecom?dev-t=DBNSEW91FK8PS%26camp=2025%26link_code=xm2)
- [SAP Performance Optimization Guide, Third Edition](http://www.amazon.com/exec/obidos/ASIN/1592290221/adminschoicecom?dev-t=DBNSEW91FK8PS%26camp=2025%26link_code=xm2)

~~UP~~
