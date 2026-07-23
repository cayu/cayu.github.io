---
title: "Auditoría en Linux"
date: 2012-08-24T15:29:46-03:00
---

Estas son algunas de las preguntas claves : **¿Cómo auditar los eventos de archivo como de lectura / escritura, etc? ¿Cómo puedo utilizar la auditoría para saber quién cambió un archivo en Linux?**

La respuesta es utilizar el sistema de auditoría del Kernel \>= 2.6. Las distribuciones actuales vienen con el demoniio auditd. Es el responsable de escribir los registros de auditoría en el disco. Durante el arranque, carga las reglas almacenadas en /etc/audit.rules

Para poder aprovechar las posibilidades de auditoria del Kernel Linux deberemos usar el comando =\> **auditctl**.

=\> **ausearch** - comando con el que se puede consultar el log de audit basado ​​en diferentes criterios de búsqueda.

=\> **aureport** - herramienta que genera reportes de los sumarios de los logs de audit.

El demonio auditd permite al administrador de un sistema Linux recibir la información de auditoría de seguridad que el núcleo genera, filtrarla y almacenarla en archivos.

Esta información tiene el siguiente formato:

- AUDIT_CONNECT pid ruid shost sport dhost dport
  - Conexión desde la máquina al host remoto dhost.
- AUDIT_ACCEPT pid ruid shost sport dhost dport
  - Conexión desde el host remoto dhost a la máquina.
- AUDIT_LISTEN pid ruid shost sport
  - El puerto indicado está esperando peticiones de servicio.
- AUDIT_OPEN pid ruid file
  - Se ha abierto el fichero file.
- AUDIT_SETUID pid old_ruid ruid euid
  - Se ha llamado con éxito a setuid(), modificando el UID de ruid a euid.
- AUDIT_EXEC pid ruid file
  - Se ha ejecutado el fichero file.
- AUDIT_MODINIT pid ruid file
  - Se ha insertado en el kernel el módulo file.

### Task: install audit package

The audit package contains the user space utilities for storing and searching the audit records generate by the audit subsystem in the Linux 2.6 kernel. CentOS/Red Hat and Fedora core includes audit rpm package. Use yum or up2date command to install package

```
# yum install audit
# up2date install audit
```

Auto start auditd service on boot

```
# ntsysv
# chkconfig auditd on
# /etc/init.d/auditd start
```

### How do I set a watch on a file for auditing?

Let us say you would like to audit a /etc/passwd file. You need to type command as follows:`# auditctl -w /etc/passwd -p war -k password-file`

Where,

- **-w /etc/passwd** : Insert a watch for the file system object at given path i.e. watch file called /etc/passwd
- **-p war** : Set permissions filter for a file system watch. It can be r for read, w for write, x for execute, a for append.
- **-k password-file** : Set a filter key on a /etc/passwd file (watch). The password-file is a filterkey (string of text that can be up to 31 bytes long). It can uniquely identify the audit records produced by the watch. You need to use password-file string or phrase while searching audit logs.

In short you are monitoring (read as watching) a /etc/passwd file for anyone (including syscall) that may perform a write, append or read operation on a file.

Wait for some time or as a normal user run command as follows:`$ grep 'something' /etc/passwd$ vi /etc/passwd`

Following are more examples:

### File System audit rules

Add a watch on “/etc/shadow” with the arbitrary filterkey “shadow-file” that generates records for “reads, writes, executes, and appends” on “shadow”

```
# auditctl -w /etc/shadow -k shadow-file -p rwxa
```

#### syscall audit rule

The next rule suppresses auditing for mount syscall exits`# auditctl -a exit,never -S mount`

#### File system audit rule

Add a watch “tmp” with a NULL filterkey that generates records “executes” on “/tmp” (good for a webserver)

```
# auditctl -w /tmp -p e -k webserver-watch-tmp
```

#### syscall audit rule using pid

To see all syscalls made by a program called sshd (pid - 1005):

```
# auditctl -a entry,always -S all -F pid=1005
```

### How do I find out who changed or accessed a file /etc/passwd?

Use ausearch command as follows:

```
# ausearch -f /etc/passwd
# ausearch -f /etc/passwd | less
# ausearch -f /etc/passwd -i | less
```

Where

- **-f /etc/passwd** : Only search for this file
- **-i** : Interpret numeric entities into text. For example, uid is converted to account name.

Output:

```
type=PATH msg=audit(03/16/2007 14:52:59.985:55) : name=/etc/passwd flags=follow,open inode=23087346 dev=08:02 mode=file,644 ouid=root ogid=root rdev=00:00
type=CWD msg=audit(03/16/2007 14:52:59.985:55) :  cwd=/webroot/home/lighttpd
type=FS_INODE msg=audit(03/16/2007 14:52:59.985:55) : inode=23087346 inode_uid=root inode_gid=root inode_dev=08:02 inode_rdev=00:00
type=FS_WATCH msg=audit(03/16/2007 14:52:59.985:55) : watch_inode=23087346 watch=passwd filterkey=password-file perm=read,write,append perm_mask=read
type=SYSCALL msg=audit(03/16/2007 14:52:59.985:55) : arch=x86_64 syscall=open success=yes exit=3 a0=7fbffffcb4 a1=0 a2=2 a3=6171d0 items=1 pid=12551 auid=unknown(4294967295) uid=lighttpd gid=lighttpd euid=lighttpd suid=lighttpd fsuid=lighttpd egid=lighttpd sgid=lighttpd fsgid=lighttpd comm=grep exe=/bin/grep
```

Let us try to understand output

- **audit(03/16/2007 14:52:59.985:55)** : Audit log time
- **uid=lighttpd gid=lighttpd** : User ids in numerical format. By passing **-i** option to command you can convert most of numeric data to human readable format. In our example user is lighttpd used grep command to open a file
- **exe=“/bin/grep”** : Command grep used to access /etc/passwd file
- perm_mask=read : File was open for read operation

So from log files you can clearly see who read file using grep or made changes to a file using vi/vim text editor. Log provides tons of other information. You need to read man pages and documentation to understand raw log format.

#### Script parser de audit.log

Script para parsear el contenido del audit.log y ver el timestamp en formato humano

```perl
#!/usr/bin/perl
use strict;

# what do I want to look for in the audit log.
my $pattern = $ARGV[0];

# Define the audit directory if the user doesn't provide one.
my $dir = '/var/log/audit';
$dir = $ARGV[1] if scalar(@ARGV) == 2;

# Strip any trailing slash
$dir =~ s/\/$//g;

# walk through the directory and save the list of files as an array.
# find is nice because it gives you full path + executable
my @files = `sudo find $dir`;
# strip new lines from the array.
chomp(@files);

# loop through each element in the array and do something.
for my $file (@files)
{
  # declare the empty array before use
  my @arr;

  # determine if we use zgrep or grep
  # zgrep is needed for gz and grep is for regular files
  if ( $file =~ /gz$/ )
  { 
    @arr = `sudo zgrep $pattern $file`;
  }
  else
  {
    @arr = `sudo grep $pattern $file`;
  }

  # print the filename only if we found something in the file
  print "\nFile: $file\n" if ( scalar(@arr) > 0 );
  
  # for each element in the array translate epoch to human readable
  foreach(@arr)
  {
    chomp;
    # do a little regex for easy matching
    if ( /(.*msg=audit\()(\d+)(\.\d+:\d+.*)/ )
    {
      convert epoch to human readable
      my $td = scalar localtime $2;
      print "$1$td$3\n";
    }
  }
}
```

Original : <http://www.linuxquestions.org/questions/linux-software-2/how-can-i-read-the-audit-time-stamp-msg%3Daudit-1213186256-105-20663-a-648547/>

O ni solución mucho mas simple :

```perl>
=== Archivo de configuración de ejemplo ===
**/etc/audit/auditd.conf**

<code>
log_file = /var/log/audit/audit.log
log_format = RAW
log_group = root
priority_boost = 4
flush = INCREMENTAL
freq = 20
num_logs = 4
disp_qos = lossy
dispatcher = /sbin/audispd
name_format = NONE
##name = mydomain
max_log_file_action = keep_logs
max_log_file_action = ROTATE
space_left = 75
space_left_action = SYSLOG
action_mail_acct = root
admin_space_left = 50
admin_space_left_action = SUSPEND
disk_full_action = SUSPEND
disk_error_action = SUSPEND
##tcp_listen_port = 
tcp_listen_queue = 5
##tcp_client_ports = 1024-65535
tcp_client_max_idle = 0
```

**/etc/audit/audit.rules**

```
# This file contains the auditctl rules that are loaded
# whenever the audit daemon is started via the initscripts.
# The rules are simply the parameters that would be passed
# to auditctl.

# First rule - delete all
-D

# Increase the buffers to survive stress events.
# Make this bigger for busy systems
-b 320

# Archivos de configuracion de audit
-w /etc/audit/auditd.conf -p wa -k audit_conf
-w /etc/audit/audit.rules -p wa -k audit_conf
-w /etc/libaudit.conf     -p wa -k audit_conf
-w /etc/sysconfig/auditd  -p wa -k audit_conf

# Feel free to add below this line. See auditctl man page
-w /etc/fstab   -p wa -k filesystems
-w /etc/hosts   -p wa -k network

-w /etc/sysctl.conf -p wa -k sysctl_parameters
-w /etc/login.defs  -p wa -k auth
-w /etc/securetty   -p wa -k auth
-w /etc/sudoers     -p wa -k auth
-w /etc/security/limits.conf -p wa -k auth

# Actividades de sistema
-a entry,always -S chmod -S fchmod -S chown -S fchown -S lchown -k attr
-a entry,always -S mkdir -S rmdir -k attr
#-a entry,always -S unlink -S rename -S link -S symlink -k attr
#-a entry,always -S setxattr -k attr
#-a entry,always -S lsetxattr -k attr
#-a entry,always -S fsetxattr -k attr
#-a entry,always -S removexattr -k attr
#-a entry,always -S lremovexattr -k attr
#-a entry,always -S fremovexattr -k attr
-a entry,always -S mknod -k mknod
-a entry,always -S mount -k mount
-a entry,always -S umount2 -k umount
-a exit,always  -S setdomainname -k network

-w /etc/inittab -p wa 
-w /etc/init.d/
-w /etc/init.d/auditd -p wa 

# Librerias de sistema
-w /etc/ld.so.conf -p wa
-w /etc/localtime -p wa 

# Configuracion de modulos
#-w /etc/modprobe.d/
#-w /etc/modprobe.conf.local -p wa 
#-w /etc/modprobe.conf -p wa 


# Configuración de CRON
-w /etc/cron.allow -p wa 
-w /etc/cron.deny -p wa 
-w /etc/cron.d/ -p wa 
-w /etc/cron.daily/ -p wa 
-w /etc/cron.hourly/ -p wa 
-w /etc/cron.monthly/ -p wa 
-w /etc/cron.weekly/ -p wa 
-w /etc/crontab -p wa
-w /var/spool/cron/root

# --- Recomendaciones de la NSA
#- Records Events that Modify Date and Time Information
-a always,exit -S settimeofday -k time-change
-a always,exit -S clock_settime -k time-change
-w /etc/localtime -p wa -k time-change
#- Record Events that Modify User/Group Information
-w /etc/group -p wa -k identity
-w /etc/passwd -p wa -k identity
-w /etc/gshadow -p wa -k identity
-w /etc/shadow -p wa -k identity
#- Record Events that Modify the System’s Network Environment
-a exit,always -S sethostname -S setdomainname -k system-locale
-w /etc/issue -p wa -k system-locale
-w /etc/issue.net -p wa -k system-locale
-w /etc/hosts -p wa -k system-locale
-w /etc/sysconfig/network -p wa -k system-locale
#- Record Attempts to Alter Logon and Logout Events
-w /var/log/faillog -p wa -k logins
-w /var/log/lastlog -p wa -k logins
#- Record Attempts to Alter Process and Session Initiation Information
-w /var/run/utmp -p wa -k session
-w /var/log/btmp -p wa -k session
-w /var/log/wtmp -p wa -k session
#- Ensure auditd Collects Information on Kernel Module Loading and Unloading
#-w /sbin/insmod -p x -k modules
#-w /sbin/rmmod -p x -k modules
#-w /sbin/modprobe -p x -k modules
#-a always,exit -S init_module -S delete_module -k modules
#- Make the auditd Conﬁguration Immutable
#Add the following as the last rule in /etc/audit/audit.rules in order to make the conﬁguration immutable:
#-e 2
#With this setting, a reboot will be required to change any audit rules.
```

# Reportes

Mostar un reporte de los que se loguearon entre ayer y hoy :

```
# aureport -l -i -ts yesterday -te today
```

Generar un conteo de las actividades auditadas :

```
# aureport --key --summary
```

Violaciones de acceso :

```
# ausearch --key access --raw | aureport --file --summary
```

Violaciones de acceso al /etc/shadow :

```
# ausearch --key access --file /etc/shadow --raw | aureport --user --summary -i
```

Sumario de ejecuciones auditadas :

```
# ausearch --key access --raw | aureport -x --summary
```

Reporte de anomalias :

```
# aureport --anomaly
```

Search for events with date and time stamps. if the date is omitted, today is assumed. If the time is omitted, now is assumed. Use 24 hour clock time rather than AM or PM to specify time. An example date is 10/24/05. An example of time is 18:00:00.

```
# ausearch -ts today -k password-file
# ausearch -ts 3/12/07 -k password-file
```

Search for an event matching the given executable name using -x option. For example find out who has accessed /etc/passwd using rm command:

```
# ausearch -ts today -k password-file -x rm
# ausearch -ts 3/12/07 -k password-file -x rm
```

Search for an event with the given user name (UID). For example find out if user vivek (uid 506) try to open /etc/passwd:

```
# ausearch -ts today -k password-file -x rm -ui 506
# ausearch -k password-file -ui 506
```

```
auditctl -w /etc/fstab -p war -k someconfig-exclude -F auid!=0
```

To see all syscalls made by a specific program:

```
auditctl -a entry,always -S all -F pid=1005
```

To see files opened by a specific user:

```
auditctl -a exit,always -S open -F auid=510
```

To see unsuccessful open call’s:

```
auditctl -a exit,always -S open -F success=0
```

To watch a file for changes (2 ways to express):

```
auditctl -w /etc/shadow -p wa
auditctl -a exit,always -F path=/etc/shadow -F perm=wa
```

To recursively watch a directory for changes (2 ways to express):

```
auditctl -w /etc/ -p wa
auditctl -a exit,always -F dir=/etc/ -F perm=wa
```

## Referencias útiles

<http://www.ibm.com/developerworks/linux/library/l-security-audit.html>

<http://www.debian.org/doc/manuals/securing-debian-howto/ch-sec-services.en.html>

<http://www.redhat.com/mailman/listinfo/linux-audit>

<http://www.nsa.gov/ia/_files/os/redhat/rhel5-guide-i731.pdf>

<http://www.nsa.gov/ia/_files/os/redhat/rhel5-pamphlet-i731.pdf>

<http://www.cyberciti.biz/tips/linux-audit-files-to-see-who-made-changes-to-a-file.html>

<http://manpages.ubuntu.com/manpages/gutsy/man8/auditctl.8.html>

<http://www.puschitz.com/SecuringLinux.shtml>

<http://www.cyberciti.biz/faq/linux-kernel-etcsysctl-conf-security-hardening>

[Linux kernel IP sysctl documentation.](http://www.cyberciti.biz/files/linux-kernel/Documentation/networking/ip-sysctl.txt)

<http://www.linuxinsight.com/proc_sys_net_ipv4.html>

<http://www.novell.com/documentation/sled10/pdfdoc/audit_sp2/audit_sp2.pdf>

<http://www.cyberciti.biz/tips/linux-audit-files-to-see-who-made-changes-to-a-file.html>
