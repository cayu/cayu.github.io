---
title: "How to: Prevent a fork bomb by limiting user process"
date: 2014-06-03T11:06:08-03:00
---

## How to: Prevent a fork bomb by limiting user process

Limiting user processes is important for running a stable system. To limit user process just add user name or group or all users to **/etc/security/limits.conf** file and impose process limitations.

### Understanding /etc/security/limits.conf file

Each line describes a limit for a user in the form: \<domain\> \<type\> \<item\> \<value\> Where:

- **domain** can be:
  - an user name
  - a group name, with @group syntax
  - the wildcard \*, for default entry
  - the wildcard %, can be also used with %group syntax, for maxlogin limit
- **type** can have the two values:
  - “soft” for enforcing the soft limits
  - “hard” for enforcing hard limits
- **item** can be one of the following:
  - core - limits the core file size (KB)
- **value** can be one of the following:
  - core - limits the core file size (KB)
  - data - max data size (KB)
  - fsize - maximum filesize (KB)
  - memlock - max locked-in-memory address space (KB)
  - nofile - max number of open files
  - rss - max resident set size (KB)
  - stack - max stack size (KB)
  - cpu - max CPU time (MIN)
  - **nproc - max number of processes**
  - as - address space limit
  - maxlogins - max number of logins for this user
  - maxsyslogins - max number of logins on the system
  - priority - the priority to run user process with
  - locks - max number of file locks the user can hold
  - sigpending - max number of pending signals
  - msgqueue - max memory used by POSIX message queues (bytes)
  - nice - max nice priority allowed to raise to
  - rtprio - max realtime priority
  - chroot - change root to directory (Debian-specific)

Login as the root and open configuration file:

```
# vi /etc/security/limits.conf
```

Following will prevent a “fork bomb”:

```
vivek hard nproc 300
@student hard nproc 50
@faculty soft nproc 100
@pusers hard nproc 200
```

Above will prevent anyone in the student group from having more than 50 processes, faculty and pusers group limit is set to 100 and 200. Vivek can create only 300 process. Please note that KDE and Gnome desktop system can launch many process.

Save and close the file. Test your new system by dropping a form bomb:

```
$ :(){ :|:& };:
```

### Fuente

<http://www.cyberciti.biz/tips/linux-limiting-user-process.html>
