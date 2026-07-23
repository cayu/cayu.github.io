---
title: "Tools of the Trade -- Iostat, Vmstat, and Netstat"
date: 2013-12-12T18:33:10-03:00
---

# Tools of the Trade – Iostat, Vmstat, and Netstat

<sub>Original: [Unix Review - January 2006](http://www.unixreview.com/documents/s=9953/sam0511g/) Hemant Sharma</sub>

The performance of a system is dependent on the I/O, memory, CPU, and network. In most Unix environments, iostat, vmstat, and netstat are the commonly available tools for system reporting. These tools provide live data about the system’s memory, disks, and network and enable us to work out the possible problem source. In this article, I will revisit these tools and explain how to use them to maximize your understanding of system activities.

## Iostat

Iostat stands for input output statistics and it provides data about the input output devices such as disk, terminals, serial devices, and CPU, but we will use it here for disk-related data only.

### Syntax

The basic syntax is:

    iostat <options> interval count

option – This may differ among operating system. Refer to the man iostat on your system for supported options.

interval – Time period in seconds between two samples. iostat 5 will give data at each 5 seconds interval.

count – Number of times the data is needed. iostat 5 4 will give data at 5 seconds interval 4 times. If no count is specified, the samples continue indefinitely and must be terminated by pressing ^c. Commonly, the command is run without count, and samples are observed to get a feel of system state.

The very first iostat command gives a snapshot of activity since the last reboot. Subsequent statistics are for the given interval and correspond to the real-time data. Because the system state changes very quickly, the first line of output can be safely ignored as it doesn’t give the useful information about current system state.

These commands use system resources and, as such, the time interval should be used with care to avoid the impact on the results. Too little interval (fewer than 5 seconds) may add command overhead to the output results and may not reflect the correct system state.

### Example

A typical iostat command on a Solaris system would give the following output:

```
example% iostat -xtc 5 2
                   extended disk statistics      tty      cpu
disk r/s  w/s Kr/s Kw/s wait actv svc_t  %w  %b  tin tout us sy wt id
d0   6.2 0.0 21.5  0.0 0.0  0.1  24.1   0   15   0   84   4  94  2 0
d1   1.8 0.0 14.3  0.0 0.0  0.1  41.6   0    7
```

wait – Average number of transactions waiting for service (queue length).

actv – Average number of transactions actively being serviced (removed from the queue but not yet completed).

svc \_t – Average service time.

%w – Percent of time there are transactions waiting for service.

%b – Percent of time the disk is busy (transactions).

The columns r/s, w/s, kr/s, and kw/s denote read and write per seconds in bytes and KB, respectively. Similarly, in AIX, it would give the following output:

```
# iostat 5 2

tty:    tin     tout     cpu:   % user    % sys     % idle    % iowait
        0.0      0.0             23.1      9.0       65.9       2.0
     
Disks:      % tm_act   Kbps     tps    Kb_read   Kb_wrtn
hdisk0         2.4     6.4      1.6          0        32
cd0            0.0     0.0      0.0          0         0
```

% tm_act – Percentage of time that the physical disk was active.

Kbps – Amount of data transferred to the drive in KB per second.

tps – A transfer is an I/O request through the device driver level to the physical disk.

Kb_read, Kb_wrtn – Reports the total data (in KB) read/written from the physical volume during the measured interval.

### Results and Solutions

#### Solaris

Important columns to observe are svc_t, wait %w and %b – the higher the service time, the lower the performance. Together service time and %busy time gives an idea of whether a system is facing a disk I/O problem. If a disk is showing a constant disk busy state greater than 65% and service time greater than 50ms, then it is suffering from a performance bottleneck.

#### AIX

If the tps and the corresponding %tm_act are higher than expected, but the transfer rate is low and the busy rate is high, then the disk may have problems with the file system or the partition.

#### Actions for Disk Performance Issues

1\. Balance the load from one disk to multiple disks using striping and adding more disks.

2\. Distribute the swap on multiple disks. This may be relevant where swamping happens frequently.

3\. Related data (such as data files and index files) should be present in the same partition.

4\. The system should have sufficient memory so that unnecessary swapping does not occur, and system resources are free for application processing.

5\. Tune the application to use disk I/O more efficiently by modifying the disk queries and using available cache facilities of application servers.

6\. If disks have databases on them, tune the SQL queries to read tables efficiently and avoid full table scans. Regularly analyze tables to fix any errors.

7\. If the disk is suffering from 100% utilization, spread the file system onto two or more disks using disk volume management software.

8\. Move the file system to another, faster disk/controller or replace the existing disk/controller with a faster one.

## Vmstat

Vmstat reports virtual memory statistics of processes, virtual memory, disk, trap, and CPU activity. Vmstat is useful to correlate memory, processes, and CPU utilization of a system. On multi-processor systems, vmstat averages the number of CPUs into the output.

### Syntax

The basic syntax for vmstat is to use the command followed by the option and time interval:

    # vmstat <options> interval count

options – Options are used to get information about specific activity. A complete set of options can be seen by referring to man vmstat on your system.

interval – This is the time period in seconds between two samples. Too small an interval (\< 5 sec) may add command overhead to the output results, and the results may not reflect the true system state.

count – This is the number of times the data is needed. The first line of vmstat gives the average of data since the time of system reboot and can be ignored. Without the count option, the command produces continuous samples until terminated by ^c. As a common practice, the samples are observed for some time without count argument to get a feel of system state.

Without interval or count options, vmstat displays a one-line summary of the virtual memory activity since the system was booted.

### Solaris Example

```
example# vmstat 5

procs  memory          page             disk      faults        cpu
r b w swap  free re mf pi p fr de sr s0 s1 s2 s3  in  sy  cs us sy id
0 0 0 11456 4120 1  41 19 1  3  0  2  0  4  0  0  48 112 130  4 14 82
0 0 1 10132 4280 0   4 44 0  0  0  0  0 23  0  0 211 230 144  3 35 62
0 0 1 10132 4616 0   0 20 0  0  0  0  0 19  0  0 150 172 146  3 33 64
```

The relevant fields of vmstat’s display are:

procs

        r     in run queue
        b     blocked for resources I/O, paging etc.
        w     swapped

page (per second)

        re    page reclaims  
        mf    minor faults  
        pi    kilobytes paged in
        po    kilobytes paged out
        fr    kilobytes freed
        de    anticipated short-term memory shortfall (Kbytes)
        sr    pages scanned by clock algorithm

cpu – Percentage usage of CPU time. On multiprocessors, this is an average across all processors.

### AIX Example

```
% vmstat 5 3

kthr     memory             page              faults        cpu
----- ----------- ------------------------ ------------ ------------
r  b   avm   fre     re  pi  po  fr  sr  cy  in  sy    cs  us sy  id wa
2  1 504038 3489832  0   0   0   0    0   0 2760 8705 1730  4  3 92  0
1  0 503080 3490975  0   0   0   0    0   0 5676 8619 4277  0 15 85  0
0  0 503073 3490982  0   0   0   0    0   0 1937 2707 232   0  0 99  0
```

\* 1 page = 4096 bytes

r – Kernel threads placed on the run queue.

b – Kernel threads blocked.

avm – Active virtual memory pages.

fre – Free memory pages.

re – Pager input/output list.

wa – % CPU time waiting on I/O.

To diagnose CPU-related issues, we need to look at the processes in the run queue under the “proc” and “cpu” columns (procs r).

For Solaris:

procs cpu r b w us sy id 0 0 0 4 14 82 0 0 1 3 35 62 0 0 1 3 33 64

The output column denotes the following:

r – Average number of runnable kernel threads over the sampling interval. Runnable refers to threads that are ready but waiting to run.

b – Average number of kernel threads awaiting resource and input/output over the sampling interval.

w – Idle and swapped processes.

us – The percent of CPU time spent in user mode. When in user mode, a process executes in its application code and does not require kernel resources.

sy – Percentage of time the CPU spends executing a process in system mode, making system calls for accessing the kernel.

id – Percentage of time the CPU is idle, that is, there are no threads available for execution or the run queue is empty. Vmstat does not take into account iowait when calculating CPU idle time, whereas top and mpstat do. So, whenever there is a high iowait, vmstat and top will show different results.

wa – Percentage of time the CPU is idle with pending local disk and NFS-mounted disks operations.

### Problem Identification

Problems can be identified by observing the following points:

r – This value should be lower than 5 for non-SMP systems. For SMP systems, this value should be lower than 5 x (Ntotal - Nbind), where Ntotal is the total number of processors and Nbind is the number of processors that have been bound to processes.

b – This value should be near zero. If the run-queue value increases, the wait-queue also increases. If threads are awakened simultaneously during a 1-second interval, the run-queue could be high but still show low CPU utilization if the threads go right back to sleep. If processes are suspended due to memory, the blocked column (b) in the vmstat report indicates the increase in the number of threads rather than the run queue.

wa – A “wa” value of more than 25 percent indicates that the impacted disks are heavily loaded.

### Results and Solutions

1\. If the number of processes in the run queue column “r” under procs is consistently greater than the number of CPUs on the system, it will slow down the system because there are more processes than available CPUs.

2\. If this number is more than four times the number of available CPUs in the system, then the system is facing a shortage of CPU resources.

3\. If the idle time (cpu id) is consistently 0 and the system time (cpu sy) is double the user time (cpu us), the system is facing a shortage of CPU resources.

Resolution of these types of issues involves:

1\. Identifying and tuning heavy CPU-using processes.

2\. Tuning of application procedures to make efficient use of CPU.

3\. Upgrading to more powerful CPU or adding more/faster cpu to system.

4\. The CPU bottleneck may also result from memory, so if the scan rate is high, adding memory can relieve CPU bottlenecks.

5\. Adjusting process priorities so that lower-priority processes do not consume all the CPU resources.

6\. Control processor utilization to keep the CPU-intensive processes limited to certain processors.

7\. Adjust time slices so that the CPU provides a good response time. Time slices can be adjusted using kernel parameters.

### Memory Issues

Memory-related information can be obtained from the following vmstat columns:

pi – The number of pages paged in from virtual memory that reside on disk.

po – The number of pages paged out to paging space. When a process terminates normally, any paging space allocated to that process is freed.

fr – Number of pages that were freed per second. If I/O is not necessary, minimal system resources are required to free a page.

sr – Scan rate is the number of pages examined per second by the page-replacement algorithm. On systems with multiple processes using many different pages, the scan rate might greatly exceed the free rate.

de – In Solaris, the “de” column represents the anticipated short-term memory shortfall in Kbytes, which is used by the page scanner to free enough pages to satisfy requests.

### Results

1\. Memory is over-committed when the ratio of fr to sr (fr:sr) is high.

2\. An fr:sr ratio of 1:4 means that for every page freed, four pages had to be examined. We can’t determine memory constraint based on this ratio because it is workload/application dependent.

3\. Memory bottlenecks are determined by the scan rate (sr). The scan rate is the number of pages scanned by the clock algorithm per second. If the scan rate (sr) is continuously more than 200 pages per second, then there is a memory shortage.

4\. If the “de” column is non-zero, then the system is anticipating a memory shortfall, and free memory will be reclaimed in expectation of its use. For overall memory availability, “de” gives a good indication of whether the system is experiencing memory shortfall.

5\. If there are I/Os queued for the swap device, application paging is occurring. If there is heavy I/O to the swap device, memory upgrade may be needed.

6\. A zombie process is not swapped out, so it continues to run in the memory and occupies the memory.

Resolution of these types of issues involves:

1\. Gather more information about the processes using the highest amount of memory and investigate to reduce the memory consumption by the thread.

2\. Tune the Java memory allocation for Java server instances and stop the unnecessary instances.

3\. Tune the applications and servers to make efficient use of memory and cache.

4\. Increase system memory.

5\. Tune the paging process by implementing priority paging in pre-Solaris 8 versions by adding line “set priority paging=1” in /etc/system. Remove this line if upgrading from Solaris 7 to 8 and retaining old /etc/system file.

6\. Remove zombie processes from the system.

## Netstat

Netstat displays the contents of various network-related data structures depending on the options selected. Options are dependent on the operating system, so check man netstat on your system for all supported options.

### Syntax

    #netstat  <option/s>

Multiple options can be given at one time from the following list:

-a – Displays the state of all sockets.

-r – Shows the system routing tables.

-i – Gives statistics on a per-interface basis.

-v – Verbose mode.

-n – Show network addresses as numbers, IP addresses. By default, netstat tries to resolve IP addresses into name for display. This option applies to the -a, -i, -r, and -v options. Because the network is being debugged, it is possible that netstat may not be able to reach naming services and it may hang looking for names. It is always a good idea to use the -n option.

interval – Number for continuous display of static tics.

The different options for netstat provide data about different network aspects, such as network connectivity, network response, and network socket status.

### Network Routing and Connectivity

```
$netstat -rn

Routing Table: IPv4
  Destination           Gateway           Flags  Ref   Use   Interface
-------------------- -------------------- ----- ----- ------ ---------
192.168.1.0         192.168.1.11           U        1   1444      le0
224.0.0.0           192.168.1.11           U        1   0         le0
default             192.168.1.1            UG       1   68276
127.0.0.1           127.0.0.1              UH       1   10497     lo0
```

This shows the output on a Solaris machine whose IP address is 192.168.1.11 with a default router at 192.168.1.1. This command is mostly useful in troubleshooting network connectivity issues. When the outside network is not accessible, check the following:

1\. Make sure the default router IP address is correct.

2\. Make sure you can ping the router from your machine.

3\. If router address is incorrect, it can be changed with route add command. See man route for more information.

4\. If the router address is correct but still you can’t ping it, there may be some network cable hub/switch problem and you will need to eliminate the faulty component.

### Network Response

The -i option to netstat gives the packet transmission and collision data about network interfaces:

```
$ netstat -i

Name  Mtu Net/Dest  Address    Ipkts  Ierrs  Opkts  Oerrs  Collis Queue
lo0  8232 loopback localhost   77814  0      77814    0         0   0
hme0 1500 server1  server1  10658566  3    4832511    0    279257   0
```

Values to note in the above example are:

* Collisions (Collis)
* Output packets (Opkts)
* Input errors (Ierrs)
* Input packets (Ipkts) 

The value in the collision column depends on the type of network. A network using the switches for connectivity will not have collisions as compared to hub-based network.

### Results and Resolutions

The values in the netstat -i output can be used to make an intelligent guess about network problem areas, as follows:

1\. (Collis+Ierrs+Oerrs)/(Ipkts+Opkts) \> 2% – This may indicate a network hardware issue.

2\. (Collis/Opkts) \> 10% – Indicates that the interface is overloaded and network load must be redistributed.

3\. (Ierrs/Ipkts) \> 25% – Indicates packets dropped by the host as a result of an overloaded network or host.

4\. If there are more than 120 collisions/second, the network is overloaded.

5\. If the sum of input and output packets is higher than about 600 for a 10-Mbs interface or 6000 for a 100-Mbs interface, the network segment is too busy and needs a redistribution of load.

6\. A high number of errors in the “Ierrs” and “Oerrs” columns indicates a transmitting/receiving problem. The most common reason may be that your source and destination have different network settings, like full-duplex and half-duplex.

### Network Socket and TCP Connection State

netstat -a gives important information about network socket and TCP state. Network states returned by netstat -a are as follows:

CLOSED – Closed; the socket is not being used.

LISTEN – Listening for incoming connections.

SYN_SENT – Actively trying to establish connection.

SYN_RECEIVED – Initial synchronization of the connection underway.

ESTABLISHED – Connection has been established.

CLOSE_WAIT – Remote shutdown; waiting for the socket to close.

FIN_WAIT_1 – Socket closed; shutting down connection.

CLOSING – Closed, then remote shutdown; awaiting acknowledgement.

LAST_ACK – Remote shutdown, then closed; awaiting acknowledgement.

FIN_WAIT_2 – Socket closed; waiting for shutdown from remote.

TIME_WAIT – Wait after close for remote shutdown retransmission.

#### Example

```
#netstat -a

Local Address Remote Address Swind    Send-Q Rwind  Recv-Q State
*.*           *.*            0        0      24576  0     IDLE
*.22          *.*            0        0      24576  0     LISTEN
*.32775       *.*            0        0      24576  0     LISTEN
*.32776       *.*            0        0      24576  0     LISTEN
*.*           *.*            0        0      24576  0     IDLE
192.168.1.184.22 192.168.1.186.56806 38912 0 24616  0     ESTABLISHED
192.168.1.184.22 192.168.1.183.58672 18048 0 24616  0     ESTABLISHED
```

If you see lots of connections in FIN_WAIT state, TCP/IP parameters need to be tuned because the connections are not being closed and are accumulating. After some time, the system may run out of resources. TCP parameters can be tuned to define a timeout so that connections can be released and used by new connections.

## Conclusion

This article provided knowledge about the tools of trade in Unix, and I hope it will help you troubleshoot some of your system performance issues.

Hemant Sharma has more than 10 years of experience in Unix systems administration and application management. He runs his own Web site (<http://www.adminschoice.com>) for sys admins and writes articles on Unix systems administration. He can be contacted at: hemantsharma@adminschoice.com.
