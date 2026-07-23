---
title: "SQL for Tivoli Storage Manager"
date: 2012-02-13T13:59:03-03:00
---

*Useful SQL Statements for TSM*\
09/29/2011

This page has a collection of useful SQL statements for IBM Tivoli Storage Manager (TSM). Here you can find out a lot of selects that will help you to get information from TSM and to construct your own SQL statements.

If you have any selects to share or have any comments, please contact me at \<thobias (a) thobias org\>

# Database and Recovery Log

### List all information from db table

```
  tsm: SERVER1> SELECT * FROM db
  
     AVAIL_SPACE_MB: 85000
        CAPACITY_MB: 80000
   MAX_EXTENSION_MB: 5000
   MAX_REDUCTION_MB: 11808
          PAGE_SIZE: 4096
       USABLE_PAGES: 20480000
         USED_PAGES: 16856530
       PCT_UTILIZED: 82.3
   MAX_PCT_UTILIZED: 85.2
   PHYSICAL_VOLUMES: 17
    BUFF_POOL_PAGES: 65536
   TOTAL_BUFFER_REQ: 5555310
      CACHE_HIT_PCT: 98.6
     CACHE_WAIT_PCT: 0.0
     BACKUP_RUNNING: NO
        BACKUP_TYPE:
    NUM_BACKUP_INCR: 0
      BACKUP_CHG_MB:
     BACKUP_CHG_PCT: 14.5
   LAST_BACKUP_DATE: 2007-07-22 16:11:23.000000
       DB_REORG_EST:
  DB_REORG_EST_TIME:
```

### TSM database utilization (%)

```
  tsm: SERVER1> SELECT pct_utilized FROM db
  
  PCT_UTILIZED
  ------------
          82.3
```

### TSM log recovery utilization (%)

```
  tsm: SERVER1> SELECT pct_utilized FROM log
  
  PCT_UTILIZED
  ------------
           0.0
```

### Selecting specific columns from db table

```
  tsm: SERVER1> SELECT avail_space_mb,capacity_mb, pct_utilized, max_pct_utilized,last_backup_date FROM db
  
  AVAIL_SPACE_MB     CAPACITY_MB     PCT_UTILIZED     MAX_PCT_UTILIZED       LAST_BACKUP_DATE
  --------------     -----------     ------------     ----------------     ------------------
           85000           80000             82.3                 85.2             2007-07-22
                                                                              16:11:23.000000
```

### Number of database volumes not synchronized

```
  tsm: SERVER1> SELECT COUNT(*) FROM dbvolumes WHERE ( NOT copy1_status='Synchronized' OR NOT -
  copy2_status='Synchronized' OR NOT copy3_status='Synchronized' )
  
   Unnamed[1]
  -----------
            0
```

### Number of log volumes not synchronized

```
  tsm: SERVER1> SELECT COUNT(*) FROM logvolumes WHERE ( NOT copy1_status='Synchronized' OR NOT -
  copy2_status='Synchronized' OR NOT copy3_status='Synchronized' )
  
   Unnamed[1]
  -----------
            0
```

# Nodes

### Number of nodes

```
  tsm: SERVER1> SELECT SUM(num_nodes) FROM domains
  
   Unnamed[1]
  -----------
          165
  
  tsm: SERVER1> SELECT COUNT(*) FROM nodes
  
   Unnamed[1]
  -----------
          165
```

### Number of nodes per domain

```
  tsm: SERVER1> SELECT domain_name,num_nodes FROM domains
  
  DOMAIN_NAME              NUM_NODES
  ------------------     -----------
  AIX                             47
  EXCHANGE                         4
  NT                              69
  VMWARE                          10
```

### Number of nodes per platform

```
  tsm: SERVER1> SELECT platform_name,COUNT(*) FROM nodes GROUP BY platform_name
  
  PLATFORM_NAME         Unnamed[2]
  ----------------     -----------
  AIX                           20
  Linux86                       36
  TDP Domino                     2
  TDP MSSQL Win32                1
  WinNT                        100
```

### Nodes locked

```
  tsm: SERVER1> SELECT node_name FROM nodes WHERE locked='YES'
  
  NODE_NAME
  ------------------
  NODE_TEMP
  NODE99
```

### Number of nodes locked

```
  tsm: SERVER1> SELECT COUNT(*) FROM nodes WHERE locked='YES'
  
   Unnamed[1]
  -----------
            2
```

### Number of nodes sessions

```
  tsm: SERVER1> SELECT COUNT(*) FROM sessions WHERE session_type='Node'
  
   Unnamed[1]
  -----------
            3
```

### TSM clients version

```
  tsm: SERVER1> SELECT node_name, platform_name, domain_name, VARCHAR(client_version)||'.'||VARCHAR(client_release)||'.'||-
  VARCHAR(client_level)||'-'||VARCHAR(client_sublevel) as "TSM Client Version" FROM nodes
  
  NODE_NAME        PLATFORM_NAME       DOMAIN_NAME        TSM Client Version
  --------------   ----------------    ---------------    ------------------
  NODE_01          WinNT               STANDARD           6.2.3-1
  NODE_02          AIX                 STANDARD           6.2.3-1
  NODE_03          TDP Domino AIX      STANDARD           5.4.1-2
  NODE_04          TDP Dom LINUXZ64    STANDARD           6.1.4-0
  NODE_05          Linux390            STANDARD           6.1.0-2
  NODE_06          TDP Domino AIX      STANDARD           5.4.1-2
  NODE_07          AIX                 STANDARD           5.4.1-2
  NODE_08          AIX                 STANDARD           5.4.1-2
    ...
```

# Occupancy

### Number of files per client

```
  tsm: SERVER1> SELECT node_name, SUM(num_files) FROM occupancy GROUP BY node_name
  
  NODE_NAME               Unnamed[2]
  ------------------     -----------
  NODE01                          20
  NODE02                       18300
  NODE03                     1418470
  NODE04                      509837
  ...
```

### Space and number of files stored per client

```
  tsm: SERVER1> SELECT node_name,CAST(FLOAT(SUM(physical_mb)) / 1024 AS DEC(8,2))as "Space in GB", -
  SUM(num_files)as"Number of files" FROM occupancy GROUP BY node_name
  
  NODE_NAME              Space in GB     Number of files
  ------------------     -----------     ---------------
  SERVER-01                  1540.50             1260371
  SERVER-02                     9.60              130357
  SERVER-03                  3279.86             1318259
  SERVER-04                  5191.91              310516
  ...
```

### Data stored per client (GB)

```
  tsm: SERVER1> SELECT node_name,CAST(FLOAT(SUM(logical_mb)) / 1024 AS DEC(8,2)) FROM -
  occupancy GROUP BY node_name
  
  NODE_NAME              Unnamed[2]
  ------------------     ----------
  SERVER-01                  364.01
  SERVER-02                  227.52
  SERVER 03                 8338.89
  SERVER-04                 3341.81
  ...
```

### Storage space used per filespace for a specific node

```
  tsm: SERVER1> SELECT node_name, filespace_name, SUM(logical_mb) AS "Total MB" FROM occupancy WHERE node_name='NODEABC' -
  GROUP BY node_name, filespace_name ORDER BY "Total MB" DESC
  
  NODE_NAME      FILESPACE_NAME               Total MB
  ------------   ------------------   ----------------
  NODEABC        /db2archivelogs             219588.48
  NODEABC        /db2offlinebackup            76585.49
  NODEABC        /opt/sysadm                  40167.95
  NODEABC        /mksysbimg                    6836.47
  NODEABC        /download                     5419.22
  NODEABC        /opt/IBM/db2                  1441.47
  NODEABC        /opt/IBM/ITM                   440.48
  NODEABC        /db2onlinelogs                 319.02
  NODEABC        /opt/IBM/ldap                  211.43
  NODEABC        /opt                           192.14
  NODEABC        /home/idsccmdb                 175.37
  NODEABC        /usr                           149.52
  NODEABC        /opt/Tivoli                     96.63
  NODEABC        /opt/VSA                        84.23
  NODEABC        /home                           69.54
  NODEABC        /opt/IBM/SCM                    66.49
  ...
```

### Storage space used per filespace and per storage pool for a specific node

```
  tsm: SERVER1> SELECT node_name, filespace_name, stgpool_name, SUM(logical_mb) AS "Total MB" FROM occupancy WHERE node_name='NODE_XYZ' -
  GROUP BY node_name, filespace_name, stgpool_name ORDER BY filespace_name
  
  NODE_NAME      FILESPACE_NAME         STGPOOL_NAME                    Total MB
  -----------    ------------------     ---------------     --------------------
  NODE_XYZ      /DRMS                  S3584ARCH                         1173.44
  NODE_XYZ      /LDAPDB2B              S3584ARCH                         8015.72
  NODE_XYZ      /LDAPDB2B              ARCHIVEPOOL                        198.85
  NODE_XYZ      /db/db2ldap/db2ba-     S3584                             1024.86
  NODE_XYZ      /db/dbawork            S3584                                0.66
  NODE_XYZ      /home                  S3584                               75.36
  NODE_XYZ      /home                  BACKUPPOOL                           6.36
  NODE_XYZ      /home/db2ldap          S3584                                3.97
  NODE_XYZ      /mksysbimg             S3584                            10045.50
  NODE_XYZ      /notes/data            S3584                             1099.20
  NODE_XYZ      /opt/lotus             S3584                                2.74
  NODE_XYZ      /tmp                   S3584                                0.30
  NODE_XYZ      /usr                   S3584                                0.98
```

### Storage space used per filespace and per backup/archive type for a specific node

```
  tsm: SERVER1> SELECT node_name, filespace_name, type, SUM(logical_mb) AS "Total MB" FROM occupancy WHERE node_name='NODE_XYZ' -
  GROUP BY node_name, filespace_name, type ORDER BY filespace_name
  
  NODE_NAME      FILESPACE_NAME         TYPE                   Total MB
  ----------     ------------------     ----------     ----------------
  NODE_XYZ       /DRMS                  Arch                    1173.44
  NODE_XYZ       /LDAPDB2B              Arch                     198.85
  NODE_XYZ       /LDAPDB2B              Bkup                    8015.72
  NODE_XYZ       /db/db2ldap/db2ba-     Bkup                    1024.86
  NODE_XYZ       /db/dbawork            Bkup                       0.66
  NODE_XYZ       /home                  Bkup                      75.36
  NODE_XYZ       /home/db2ldap          Bkup                       3.97
  NODE_XYZ       /mksysbimg             Bkup                   10045.50
  NODE_XYZ       /notes/b01acidb00-     Bkup                    1099.20
  NODE_XYZ       /opt/lotus             Bkup                       2.74
  NODE_XYZ       /tmp                   Bkup                       0.30
  NODE_XYZ       /usr                   Bkup                       0.98
```

# Schedules

### Nodes without associated schedules

```
  tsm: SERVER1> SELECT node_name FROM nodes WHERE node_name NOT IN (SELECT node_name FROM associations)
  
  NODE_NAME
  ------------------
  NODE_TEMP
  SERVER-04
  ...
```

### Number of nodes without associated schedules

```
  tsm: SERVER1> SELECT COUNT(*) FROM nodes WHERE node_name NOT IN (SELECT node_name FROM associations)
  
   Unnamed[1]
  -----------
           12
```

### Nodes with associated schedules

```
  tsm: SERVER1> SELECT node_name FROM nodes WHERE node_name IN (SELECT node_name FROM associations)
  
  NODE_NAME
  ------------------
  NODE01
  NODE02
  NODE03
  NODE04
```

### Number of nodes associated per schedules

```
  tsm: SERVER1> SELECT domain_name, schedule_name, count(*) FROM associations GROUP BY domain_name, schedule_name
  
  DOMAIN_NAME            SCHEDULE_NAME           Unnamed[3]
  ------------------     ------------------     -----------
  AIX                    DAILY                           24
  AIX                    WEEKLY                          17
  LINUX                  DAILY                           38
  ...
```

### Information about schedules and associations (2 tables)

```
  tsm: SERVER1> SELECT associations.domain_name, associations.node_name, associations.schedule_name, -
  client_schedules.description, client_schedules.action, client_schedules.options, -
  client_schedules.objects, client_schedules.starttime FROM associations associations, -
  client_schedules client_schedules WHERE associations.domain_name = client_schedules.domain_name -
  AND associations.schedule_name = client_schedules.schedule_name ORDER BY associations.domain_name, -
  associations.node_name, associations.schedule_name
  
    DOMAIN_NAME: AIX
      NODE_NAME: NODE01
  SCHEDULE_NAME: Schedule1
    DESCRIPTION: Backup Online of database XX
         ACTION: COMMAND
        OPTIONS:
        OBJECTS: /opt/tivoli/tsm/scripts/bkp_weekly.sh
      STARTTIME: 21:15:00
  
    DOMAIN_NAME: AIX
      NODE_NAME: NODE01
  SCHEDULE_NAME: Schedule2
    DESCRIPTION: Backup Incremental of Operating System
         ACTION: INCREMENTAL
        OPTIONS:
        OBJECTS: /usr/ /opt/ /var/ /etc/ /home/
      STARTTIME: 09:00:00
  ...
```

### Some cool information about node, associations and schedules

```
  tsm: SERVER1> SELECT associations.domain_name, associations.node_name, associations.schedule_name, -
  client_schedules.description, client_schedules.action, client_schedules.options, -
  client_schedules.objects, client_schedules.priority, client_schedules.startdate, -
  client_schedules.starttime, client_schedules.duration, client_schedules.durunits, -
  client_schedules.period, client_schedules.perunits, client_schedules.dayofweek, -
  client_schedules.expiration, client_schedules.chg_time, client_schedules.chg_admin, -
  client_schedules.profile, client_schedules.sched_style, client_schedules.enh_month, -
  client_schedules.dayofmonth, client_schedules.weekofmonth FROM associations associations, -
  client_schedules client_schedules WHERE associations.domain_name = client_schedules.domain_name -
  AND associations.schedule_name = client_schedules.schedule_name ORDER BY associations.node_name, -
  associations.domain_name, associations.schedule_name
  
    DOMAIN_NAME: AIX
      NODE_NAME: SERVER-01
  SCHEDULE_NAME: SERV01_ARC_APP_WEEKLY
    DESCRIPTION: Archive Weekly 
         ACTION: ARCHIVE
        OPTIONS: -archmc=MC_AIX_WEEKLY
        OBJECTS: /app2/
       PRIORITY: 5
      STARTDATE: 2006-05-01
      STARTTIME: 06:01:00
       DURATION: 1
       DURUNITS: HOURS
         PERIOD: 1
       PERUNITS: WEEKS
      DAYOFWEEK: TUESDAY
     EXPIRATION:
       CHG_TIME: 2007-07-03 10:35:12.000000
      CHG_ADMIN: ADMIN
        PROFILE:
    SCHED_STYLE: CLASSIC
      ENH_MONTH:
     DAYOFMONTH:
    WEEKOFMONTH:
  
    DOMAIN_NAME: NT
      NODE_NAME: SERVER-02
  SCHEDULE_NAME: BD_OFF_DOMINO_MONTHLY
         ACTION: COMMAND
        OPTIONS:
        OBJECTS: d:\tsm\tsmscripts\tdp_dom_offline_monthly.cmd
       PRIORITY: 2
      STARTDATE: 2006-05-01
      STARTTIME: 21:00:00
       DURATION: 1
       DURUNITS: HOURS 
         PERIOD:
       PERUNITS:
      DAYOFWEEK: Sun
     EXPIRATION:
       CHG_TIME: 2007-05-24 09:08:14.000000
      CHG_ADMIN: ADMIN
        PROFILE:
    SCHED_STYLE: ENHANCED
      ENH_MONTH: Any
     DAYOFMONTH: Any
    WEEKOFMONTH: First
  
  ...
```

# Drives and Paths

### Some information about paths

```
  tsm: SERVER1> SELECT source_name,source_type,destination_name,destination_type,library_name, -
  device FROM paths
  
  SOURCE_NAME       SOURCE_TYPE      DESTINATION_NAME      DESTINATION_TYPE    LIBRARY_NAME      DEVICE
  --------------    -------------    ------------------    ----------------    --------------    -----------
  TSM-SERVER1       SERVER           3584                  LIBRARY                               /dev/smc0
  TSM-SERVER1       SERVER           DRIVE01               DRIVE               3584              /dev/rmt0
  TSM-SERVER1       SERVER           DRIVE02               DRIVE               3584              /dev/rmt1
  TSM-SERVER1       SERVER           DRIVE03               DRIVE               3584              /dev/rmt2
  TSM-SERVER1       SERVER           DRIVE04               DRIVE               3584              /dev/rmt3
```

### Some information about drives

```
  tsm: SERVER1> SELECT library_name,drive_name,device_type,read_formats,write_formats,drive_state, -
  drive_serial FROM drives
  
   LIBRARY_NAME: 3584
     DRIVE_NAME: DRIVE01
    DEVICE_TYPE: LTO
   READ_FORMATS: ULTRIUM3C,ULTRIU
  WRITE_FORMATS: ULTRIUM3C,ULTRIU
    DRIVE_STATE: EMPTY
   DRIVE_SERIAL: 000782XXXX
  
   LIBRARY_NAME: 3584
     DRIVE_NAME: DRIVE02
    DEVICE_TYPE: LTO
   READ_FORMATS: ULTRIUM3C,ULTRIU
  WRITE_FORMATS: ULTRIUM3C,ULTRIU
    DRIVE_STATE: LOADED
   DRIVE_SERIAL: 000782XXXX
  
   LIBRARY_NAME: 3584
     DRIVE_NAME: DRIVE03
    DEVICE_TYPE: LTO
   READ_FORMATS: ULTRIUM3C,ULTRIU
  WRITE_FORMATS: ULTRIUM3C,ULTRIU
    DRIVE_STATE: LOADED
   DRIVE_SERIAL: 000782XXXX
```

### Number of drives not online

```
  tsm: SERVER1> SELECT COUNT(*) FROM drives WHERE NOT online='YES'
  
   Unnamed[1]
  -----------
            0
```

### Number of drives not online in library 3584

```
  tsm: SERVER1> SELECT COUNT(*) FROM drives WHERE NOT online='YES' and library_name='3584'
  
   Unnamed[1]
  -----------
            0
```

### Number of paths not online

```
  tsm: SERVER1> SELECT COUNT(*) FROM paths WHERE NOT online='YES'
  
   Unnamed[1]
  -----------
            0
```

### Information about drives utilization

```
  tsm: SERVER1> SELECT library_name, drive_name, drive_state, volume_name, allocated_to, online FROM drives
  
  LIBRARY_NAME       DRIVE_NAME        DRIVE_STATE        VOLUME_NAME        ALLOCATED_TO       ONLINE
  ---------------    --------------    ---------------    ---------------    ---------------    --------
  LIBRARY3           DRIVE01           LOADED             TAPE86             libclient_1        YES
  LIBRARY3           DRIVE02           LOADED             TAPE17             libclient_3        YES
  LIBRARY3           DRIVE03           EMPTY                                                    YES
  LIBRARY3           DRIVE04           EMPTY                                                    YES
  LIBRARY3           DRIVE05           LOADED             TAPE73             libclient_2        YES
  LIBRARY3           DRIVE06           LOADED             TAPE28             libclient_1        YES
  LIBRARY3           DRIVE07           EMPTY                                                    YES
  LIBRARY3           DRIVE08           LOADED             TAPE66             libclient_3        YES
  ...
```

### Information about drives x paths

```
  tsm: SERVER1> SELECT b.source_name, a.library_name, a.drive_name, a.drive_serial, b.device FROM drives a, paths b WHERE a.drive_name=b.destination_name
  
  SOURCE_NAME    LIBRARY_NAME       DRIVE_NAME       DRIVE_SERIAL       DEVICE
  -----------    ---------------    -------------    ---------------    -------------
  TSM01          L3584              DRIVE1           000785YYXX         /dev/rmt0
  TSM01          L3584              DRIVE2           000785YYXX         /dev/rmt61
  TSM01          L3584              DRIVE3           000785YYXX         /dev/rmt50
  TSM01          L3584              DRIVE4           000785YYXX         /dev/rmt62
  TSM01          L3584              DRIVE5           000785YYXX         /dev/rmt3
  TSM02          L3584              DRIVE1           000785YYXX         /dev/rmt0
  TSM02          L3584              DRIVE2           000785YYXX         /dev/rmt49
  TSM02          L3584              DRIVE3           000785YYXX         /dev/rmt14
  TSM02          L3584              DRIVE4           000785YYXX         /dev/rmt50
  TSM02          L3584              DRIVE5           000785YYXX         /dev/rmt3
```

# Management class

### Management classes per domain

```
  tsm: SERVER1> SELECT domain_name, set_name, class_name, defaultmc FROM mgmtclasses
  
  DOMAIN_NAME            SET_NAME               CLASS_NAME             DEFAULTMC
  ------------------     ------------------     ------------------     ------------------
  AIX                    AIX                    DAILY                  Yes
  AIX                    AIX                    WEEKLY                 No
  AIX                    ACTIVE                 DAILY                  Yes
  AIX                    ACTIVE                 WEEKLY                 No
  LINUX                  LINUX                  ARCH1                  Yes
  LINUX                  ACTIVE                 ARCH1                  Yes
  ...
```

### Management classes per domain of policy set ACTIVE

```
  tsm: SERVER1> SELECT domain_name, class_name, defaultmc FROM mgmtclasses WHERE set_name='ACTIVE'
  
  DOMAIN_NAME            CLASS_NAME             DEFAULTMC
  ------------------     ------------------     ------------------
  AIX                    DAILY                  Yes
  AIX                    WEEKLY                 No
  LINUX                  ARCH1                  Yes
  ...
```

### Default management class per domain of policy set ACTIVE

```
  tsm: SERVER1> SELECT domain_name, class_name, defaultmc FROM mgmtclasses WHERE set_name='ACTIVE' AND defaultmc='Yes'
  
  DOMAIN_NAME            CLASS_NAME             DEFAULTMC
  ------------------     ------------------     ------------------
  AIX                    AIX                    Yes
  LINUX                  ARCH1                  Yes
  ...
```

### Management classes of a specifc domain of policy set ACTIVE

```
  tsm: SERVER1> SELECT domain_name, class_name, defaultmc FROM mgmtclasses WHERE set_name='ACTIVE' AND domain_name='AIX'
  
  DOMAIN_NAME            CLASS_NAME             DEFAULTMC
  ------------------     ------------------     ------------------
  AIX                    DAILY                  Yes
  AIX                    WEEKLY                 No
  ...
```

### Management classes of policy set ACTIVE that a specific node can use

```
  tsm: SERVER1> SELECT nodes.domain_name, nodes.node_name, mgmtclasses.class_name, mgmtclasses.defaultmc FROM nodes, mgmtclasses -
  WHERE nodes.domain_name=mgmtclasses.domain_name AND set_name='ACTIVE' AND node_name='NODE1'
  
  DOMAIN_NAME            NODE_NAME              CLASS_NAME             DEFAULTMC
  ------------------     ------------------     ------------------     ------------------
  AIX                    NODE1                  DAILY                  Yes
  AIX                    NODE1                  WEEKLY                 No
  ...
```

### Management classes with backup copy group information

```
  tsm: SERVER1> SELECT -
   mgmtclasses.domain_name, mgmtclasses.set_name, mgmtclasses.class_name, mgmtclasses.defaultmc, -
   bu_copygroups.verexists, bu_copygroups.verdeleted, bu_copygroups.retextra, bu_copygroups.retonly, bu_copygroups.destination -
  FROM -
   mgmtclasses mgmtclasses, bu_copygroups bu_copygroups -
  WHERE -
   mgmtclasses.domain_name = bu_copygroups.domain_name AND -
   mgmtclasses.set_name = bu_copygroups.set_name AND -
   mgmtclasses.class_name = bu_copygroups.class_name AND -
   mgmtclasses.set_name='ACTIVE' -
  ORDER BY -
   mgmtclasses.domain_name, mgmtclasses.set_name, mgmtclasses.class_name
  
  DOMAIN_NAME     SET_NAME      CLASS_NAME       DEFAULTMC      VEREXISTS   VERDELETED   RETEXTRA   RETONLY    DESTINATION
  -------------   -----------   --------------   ------------   ---------   ----------   --------   --------   -------------
  STANDARD        ACTIVE        STANDARD         Yes            2           1            30         60         BACKUPPOOL
  AIX             ACTIVE        MC_AIX_TDP       No             NOLIMIT     NOLIMIT      60         60         BACKUPPOOL
  AIX             ACTIVE        LOGBKUP          No             1           1            1          90         BACKUPPOOL
  AIX             ACTIVE        MC_AIX_DAILY     YES            1           0            14         30         S3584
  ...
```

### Management classes with archive copy group information

```
  tsm: SERVER1> SELECT -
   mgmtclasses.domain_name, mgmtclasses.set_name, mgmtclasses.class_name, mgmtclasses.defaultmc, -
   ar_copygroups.retver, ar_copygroups.destination -
  FROM -
   mgmtclasses mgmtclasses, ar_copygroups ar_copygroups -
  WHERE -
   mgmtclasses.domain_name = ar_copygroups.domain_name AND -
   mgmtclasses.set_name = ar_copygroups.set_name AND -
   mgmtclasses.class_name = ar_copygroups.class_name AND -
   mgmtclasses.set_name='ACTIVE' -
  ORDER BY -
   mgmtclasses.domain_name, mgmtclasses.set_name, mgmtclasses.class_name
  
  DOMAIN_NAME        SET_NAME          CLASS_NAME            DEFAULTMC          RETVER       DESTINATION
  ---------------    --------------    ------------------    ---------------    --------     ----------------
  STANDARD           ACTIVE            STANDARD              Yes                365          ARCHIVEPOOL
  AIX                ACTIVE            FOREVER               No                 NOLIMIT      S3584
  AIX                ACTIVE            MC_AIX_WEEKLY         Yes                30           BACKUPPOOL
  WINDOWS            ACTIVE            MC_WIN_WEEKLY         Yes                30           BACKUPPOOL
  ...
```

# Copy Groups

### Destination pool of each management class (type: archive copy group)

```
  tsm: SERVER1> SELECT domain_name, class_name, destination FROM ar_copygroups
  
  DOMAIN_NAME            CLASS_NAME             DESTINATION
  ------------------     ------------------     ------------------
  AIX                    MC_AIX_DAILY           AIX_DAILY
  AIX                    MC_AIX_MONTHLY         AIX_MONTHLY
  AIX                    MC_AIX_NOLIMIT         AIX_NOLIMIT
  ...
```

### Destination pool of each management class (type: backup copy group)

```
  tsm: SERVER1> SELECT domain_name, class_name, destination FROM bu_copygroups WHERE set_name='ACTIVE'
  
  DOMAIN_NAME            CLASS_NAME             DESTINATION
  ------------------     ------------------     ------------------
  AIX                    MC_AIX_DAILY           AIX_DAILY
  AIX                    MC_AIX_TDP             AIX_DAILY
  ...
```

### Some information about archive copy group

```
  tsm: SERVER1> SELECT domain_name,set_name,class_name,retver,destination FROM ar_copygroups
  
  DOMAIN_NAME            SET_NAME               CLASS_NAME             RETVER       DESTINATION
  ------------------     ------------------     ------------------     --------     ------------------
  AIX                    ACTIVE                 MC_AIX_DAILY           7            AIX_DAILY
  AIX                    ACTIVE                 MC_AIX_MONTHLY         365          AIX_MONTHLY
  AIX                    ACTIVE                 MC_AIX_NOLIMIT         NOLIMIT      AIX_NOLIMIT
  AIX                    STANDARD               MC_AIX_DAILY           7            AIX_DAILY
  AIX                    STANDARD               MC_AIX_MONTHLY         365          AIX_MONTHLY
  AIX                    STANDARD               MC_AIX_NOLIMIT         NOLIMIT      AIX_NOLIMIT
  ...
  
  tsm: SERVER1> SELECT domain_name,set_name,class_name,retver,destination FROM ar_copygroups -
  WHERE set_name='ACTIVE'
  
  DOMAIN_NAME            SET_NAME               CLASS_NAME             RETVER       DESTINATION
  ------------------     ------------------     ------------------     --------     ------------------
  AIX                    ACTIVE                 MC_AIX_DAILY           7            AIX_DAILY
  AIX                    ACTIVE                 MC_AIX_MONTHLY         365          AIX_MONTHLY
  AIX                    ACTIVE                 MC_AIX_NOLIMIT         NOLIMIT      AIX_NOLIMIT
  ...
```

### Some information about backup copy group

```
  tsm: SERVER1> SELECT domain_name,set_name,class_name,verexists,verdeleted,retextra,retonly,destination - 
  FROM bu_copygroups
  
  DOMAIN_NAME    SET_NAME      CLASS_NAME       VEREXISTS  VERDELETED  RETEXTRA  RETONLY   DESTINATION
  -------------  ------------  ---------------  ---------  ----------  --------  --------  --------------
  AIX            ACTIVE        MC_AIX_DAILY     2          1           7         15        AIX_DAILY 
  AIX            ACTIVE        MC_AIX_TDP       NOLIMIT    NOLIMIT     15        15        AIX_DAILY 
  AIX            STANDARD      MC_AIX_DAILY     2          1           7         15        AIX_DAILY 
  AIX            STANDARD      MC_AIX_TDP       NOLIMIT    NOLIMIT     15        15        AIX_DAILY 
  ...
```

# Activity Log

### Search in the activity log for missed schedules in the last 2 hours

```
  tsm: SERVER1> SELECT date_time,message FROM actlog WHERE originator='SERVER' AND -
  message LIKE'ANR2578W%' AND date_time>=current_timestamp-2 hours
  
           DATE_TIME     MESSAGE
  ------------------     ------------------
          2007-07-26     ANR2578W Schedule
     14:00:01.000000      ORACLE_HOME in
                          domain AIX for
                          node SERVER-1
                          has missed its
                          scheduled start
                          up window.
```

### Search in the activity log for messages with Error severity in the last 1 hour

```
  tsm: SERVER1> SELECT date_time,message FROM actlog WHERE originator='SERVER' AND severity='E' AND -
  date_time>current_timestamp-1 hours
  
           DATE_TIME     MESSAGE
  ------------------     ------------------
          2007-07-27     ANR2034E QUERY
     10:22:17.000000      SPACETRIGGER: No
                          match found using
                          this criteria.(
                          SESSION: 252982)
```

### Search in the activity log for successful, missed or failed schedules in the last 1 day

```
  tsm: SERVER1> SELECT date_time,severity,message FROM actlog WHERE originator='SERVER' AND -
  ( message LIKE'ANR2507I%' OR -
  message LIKE'ANR2751I%' OR -
  message LIKE'ANR2578W%' OR -
  message LIKE'ANR2579E%') AND -
  date_time>timestamp(current_date)-(1)days
  
           DATE_TIME               SEVERITY     MESSAGE
  ------------------     ------------------     -------------------
          2007-07-25                      I     ANR2507I Schedule
     00:14:48.000000                             IN_APP1 for domain
                                                 NT started at
                                                 07/24/07 22:30:00
                                                 for node SERVER-2
                                                 completed
                                                 successfully at
                                                 07/25/07
                                                 00:14:48.(SESSIO-
                                                 N: 233833)
  
          2007-07-25                      E     ANR2579E Schedule
     00:30:03.000000                             INC_APP2 in domain
                                                 NT for node
                                                 SERVER-3
                                                 failed (return
                                                 code 1).(SESSION:
                                                 234285)
  
          2007-07-25                      W     ANR2578W Schedule
     00:40:01.000000                             ORACLE_HOME in
                                                 domain AIX for 
                                                 node SERVER-1
                                                 has missed its
                                                 scheduled start
                                                 up window.
```

### Search in the activity log for a specific ANR in the last 1 day

```
  tsm: SERVER1> SELECT date_time,severity,message from actlog WHERE message LIKE'ANR8438I%' - 
  and date_time>timestamp(current_date)-(1)days
  
           DATE_TIME               SEVERITY     MESSAGE
  ------------------     ------------------     ------------------
          2007-07-27                      I     ANR8438I CHECKOUT
     09:21:19.000000                             LIBVOLUME for
                                                 volume R00135L3
                                                 in library 3584
                                                 completed
                                                 successfully.(SE-
                                                 SSION: 252515,
                                                 PROCESS: 470)
          2007-07-27                      I     ANR8438I CHECKOUT
     09:21:28.000000                             LIBVOLUME for
                                                 volume R00049L3
                                                 in library 3584
                                                 completed
                                                 successfully.(SE-
                                                 SSION: 252515,
                                                 PROCESS: 471)
```

# Summary

### Summary of archive operations in the last 7 days

```
  tsm: SERVER1> select cast(float(sum(bytes))/1024/1024/1024 as dec(8,2)) -
  as "Archive data in GB" from summary where - 
  activity='ARCHIVE' and end_time>timestamp(current_date)-(7)days
  
  Archive data in GB
  --------------------
              14508.09
```

### Summary of backup operations in a specific range

```
  tsm: SERVER1> SELECT CAST(FLOAT(SUM(bytes))/1024/1024/1024 AS DEC(8,2)) -
  AS "Backed up data in GB" FROm summary WHERE activity='BACKUP' -
  AND start_time >{ts '2007-06-01 00:00:00'} AND start_time <{ts '2007-07-01 00:00:00'} 
  
  Backed up data in GB
  --------------------
              38829.70
```

### Statistics of archive, backup, restore and retrieve operations per node in the last 7 days (GB)

```
  tsm: SERVER1> SELECT entity, activity, CAST(FLOAT(SUM(bytes)) / 1024 / 1024 / 1024 AS DECIMAL(8,2)) -
  FROM summary WHERE end_time>current_timestamp-(7)DAY and ( activity='ARCHIVE' OR - 
  activity='BACKUP' OR activity='RESTORE' OR activity='RETRIEVE' ) GROUP BY entity, activity
  
  ENTITY                 ACTIVITY               Unnamed[3]
  ------------------     ------------------     ----------
  SERVER-01              ARCHIVE                     81.14
  SERVER-01              BACKUP                     261.68
  SERVER-01              RESTORE                      2.91
  SERVER-02              ARCHIVE                    171.51
  SERVER-02              BACKUP                       0.00
  SERVER-03              ARCHIVE                     17.64
  SERVER-04              ARCHIVE                    168.32
  SERVER-04              BACKUP                     530.77
  ...
```

### Total of backup and archive per node in a specific date

```
  tsm: SERVER1> SELECT entity, CAST(FLOAT(SUM(bytes)) / 1024 / 1024 / 1024 AS DECIMAL(8,2)) as "GB" -
  FROM summary WHERE ( activity='ARCHIVE' OR activity='BACKUP' ) AND -
  start_time >{ts '2011-09-21 00:00:00'} AND start_time <{ts '2011-09-22 00:00:00'} -
  GROUP BY entity ORDER BY "GB"
  
  ENTITY                   GB
  ------------     ----------
  NODE01                 0.28
  NODE02                42.61
  NODE03                50.64
  NODE04               127.66
  NODE05               128.93
  NODE06               140.86
  NODE07               211.90
  ...
```

### Summary of Operations in the Last 24 Hours (GB)

```
  tsm: SERVER1> SELECT activity, cast(float(sum(bytes))/1024/1024/1024 as dec(8,2)) as -
  "GB" FROM summary WHERE activity<>'TAPE MOUNT' AND activity<>'EXPIRATION' -
  AND end_time>current_timestamp-24 hours GROUP BY activity
  
  ACTIVITY                       GB
  ------------------     ----------
  BACKUP                     858.56
  FULL_DBBACKUP                1.15
  MIGRATION                  496.28
  RECLAMATION                652.14
  STGPOOL BACKUP             496.10
```

### Summary of Operations in a specific date (GB)

```
  tsm: SERVER1> SELECT activity, cast(float(sum(bytes))/1024/1024/1024 as dec(8,2)) as -
  "GB" FROM summary WHERE activity<>'TAPE MOUNT' AND activity<>'EXPIRATION' -
  AND start_time >{ts '2011-09-21 00:00:00'} AND start_time <{ts '2011-09-22 00:00:00'} GROUP BY activity
  
  ACTIVITY                       GB
  ------------------     ----------
  ARCHIVE                     60.35
  BACKUP                    5743.76
  FULL_DBBACKUP               73.13
  MIGRATION                 2704.77
  RECLAMATION                701.67
  RESTORE                      2.48
  RETRIEVE                     1.81
```

### Volumes reclaimed in the last 48 Hours

```
  tsm: SERVER1> SELECT start_time, end_time-start_time AS ELAPTIME, activity, number, entity, mediaw, successful -
  FROM summary WHERE activity='RECLAMATION' AND end_time>current_timestamp-48 hours
  
         START_TIME               ELAPTIME ACTIVITY            NUMBER ENTITY                MEDIAW     SUCCESSFUL
  ----------------- ---------------------- --------------- ---------- ------------------ --------- --------------
         2008-11-20      0 00:22:31.000000 RECLAMATION            704 DAILY  (VOL076L4)         15            YES
    12:00:15.000000
         2008-11-20      0 00:23:01.000000 RECLAMATION            704 DAILY  (VOL066L4)         13            YES
    12:22:46.000000
         2008-11-20      0 00:13:40.000000 RECLAMATION            704 WEEKLY (VOL008L4)         16            YES
    12:45:48.000000
         2008-11-22      0 00:40:18.000000 RECLAMATION            715 DAILY  (VOL092L4)         51            YES
    12:00:29.000000
         2008-11-22      0 00:29:51.000000 RECLAMATION            715 DAILY  (VOL100L4)         21            YES
    12:40:47.000000
```

### Volumes reclaimed in the last 48 Hours (better date format?!)

```
  tsm: SERVER1> SELECT substr(char(start_time),1,19) AS START_TIME, -
  substr(char(end_time - start_time),1,10) AS "ELAPTIME (D HHMMSS)", -
  activity, number, entity, mediaw, successful FROM summary WHERE -
  activity='RECLAMATION' AND end_time>current_timestamp-48 hours
  
  START_TIME       ELAPTIME (D HHMMSS)  ACTIVITY             NUMBER  ENTITY                  MEDIAW      SUCCESSFUL
  ---------------  -------------------  ---------------  ----------  ------------------  -----------  --------------
  2008-11-20       0 00:22:31           RECLAMATION             704  DAILY  (VOL076L4)           15             YES
   12:00:15
  2008-11-20       0 00:23:01           RECLAMATION             704  DAILY  (VOL066L4)           13             YES
   12:22:46
  2008-11-20       0 00:13:40           RECLAMATION             704  WEEKLY (VOL008L4)           16             YES
   12:45:48
  2008-11-22       0 00:40:18           RECLAMATION             715  DAILY  (VOL092L4)           51             YES
   12:00:29
  2008-11-22       0 00:29:51           RECLAMATION             715  DAILY  (VOL100L4)           21             YES
   12:40:47
```

### Admin tasks information

```
  tsm: SERVER1> SELECT activity, substr(char(start_time),1,16) AS START_TIME, number, -
  substr(char(end_time - start_time),1,10) AS "ELAPTIME (D HHMMSS)", successful FROM summary WHERE -
  ( activity='EXPIRATION' OR activity='MIGRATION' OR activity='FULL_DBBACKUP' OR activity='RECLAMATION' OR activity='STGPOOL BACKUP' ) -
  AND start_time >{ts '2011-09-21 00:00:00'} AND start_time <{ts '2011-09-22 00:00:00'} 
  
  ACTIVITY               START_TIME               NUMBER     ELAPTIME (D HHMMSS)         SUCCESSFUL
  ------------------     ------------------     --------     -------------------     --------------
  MIGRATION              2011-09-21 10:00           6028     0 03:55:49                         YES
  STGPOOL BACKUP         2011-09-21 10:11           6029     0 04:38:27                         YES
  FULL_DBBACKUP          2011-09-21 12:15           6030     0 01:24:01                         YES
  EXPIRATION             2011-09-21 16:00           6032     0 01:39:47                         YES
  RECLAMATION            2011-09-21 17:11           6033     0 01:47:02                         YES
  MIGRATION              2011-09-21 20:54           6034     0 03:35:50                         YES
```

# Volumes

### Number of scratch volumes

```
  tsm: SERVER1> SELECT COUNT(*) FROM libvolumes WHERE status='Scratch'
  
   Unnamed[1]
  -----------
           18
```

### Number of scratch volumes in library 3584

```
  tsm: SERVER1> SELECT COUNT(*) FROM libvolumes WHERE status='Scratch' and library_name='3584'
  
   Unnamed[1]
  -----------
           18
```

### Number of scratch volumes for each library

```
  tsm: SERVER1> SELECT library_name,COUNT(*) FROM libvolumes WHERE status='Scratch' GROUP BY library_name
  
  LIBRARY_NAME            Unnamed[2]
  ------------------     -----------
  3584                            18
```

### Number of volumes per device class

```
  tsm: SERVER1> SELECT devclass_name, COUNT(*) FROM volumes GROUP BY devclass_name
  
  DEVCLASS_NAME           Unnamed[2]
  ------------------     -----------
  3584                           133
  DISK                             6
```

### Number of volumes per storage pool

```
  tsm: SERVER1> SELECT stgpool_name,COUNT(*) FROM volumes GROUP BY stgpool_name
  
  STGPOOL_NAME            Unnamed[2]
  ------------------     -----------
  AIX_ANUAL                        4
  AIX_ARCH1                        2
  AIX_ARCH2                        2
  AIX_DAILY                       20
  AIX_MONTHLY                      4
  AIX_NOLIMIT                      1
  NT_DAILY                        41
  NT_MONTHLY                      22
```

### Number of volumes unavailable

```
  tsm: SERVER1> SELECT COUNT(*) FROM volumes WHERE access='UNAVAILABLE'
  
   Unnamed[1]
  -----------
            0
```

### Number of volumes in error state

```
  tsm: SERVER1> SELECT COUNT(*) FROM volumes WHERE error_state='YES'
  
   Unnamed[1]
  -----------
            1
```

### Volumes with write or read errors in the library

```
  tsm: SERVER1> SELECT volumes.volume_name, volumes.stgpool_name, volumes.pct_utilized, volumes.status, -
  volumes.write_errors, volumes.read_errors FROM volumes, libvolumes WHERE -
  volumes.volume_name=libvolumes.volume_name AND ( volumes.write_errors>0 OR volumes.read_errors>0 )
  
  VOLUME_NAME           STGPOOL_NAME          PCT_UTILIZED    STATUS                WRITE_ERRORS    READ_ERRORS
  ------------------    ------------------    ------------    ------------------    ------------    -----------
  P10128                AIX_DAILY                     27.1    FILLING                          1              0
  P10129                AIX_DAILY                      8.2    FULL                             2              0
  P10135                NT_MONTHLY                    22.3    FILLING                          0              1
  ...
```

### Number of volumes per library

```
  tsm: SERVER1> SELECT library_name,COUNT(*) FROM libvolumes GROUP BY library_name
  
  LIBRARY_NAME            Unnamed[2]
  ------------------     -----------
  3584                            72
```

### Volume information ordered by (%) reclaim

```
  tsm: SERVER1> SELECT volume_name,devclass_name,stgpool_name,pct_reclaim,pct_utilized,status,access FROM volumes order by pct_reclaim
  
  
  VOLUME_NAME     DEVCLASS_NAME     STGPOOL_NAME     PCT_RECLAIM PCT_UTILIZED STATUS         ACCESS
  --------------- ----------------- ---------------- ----------- ------------ -------------- -------------
  TA0148L4        D3584             DAILY                    0.0          9.7 FILLING        READWRITE
  TA0149L4        D3584             DAILY                    0.0         13.5 FILLING        READWRITE
  TA0045L4        D3584             DAILY                    0.1          0.1 FILLING        READWRITE
  TA0144L4        D3584             DAILY                    0.1         24.0 FILLING        READWRITE
  TA0122L4        D3584             WEEKLY                   0.2         23.3 FILLING        READWRITE
  TA0172L4        D3584             DAILY                    0.2          0.0 FILLING        READWRITE
  TA0023L4        D3584             DAILY                    0.3          0.0 FILLING        READWRITE
  TA0125L4        D3584             WEEKLY                   0.3         99.6 FULL           READWRITE
  ...
```

### Full volumes with utilization (%) less than XX

```
  tsm: SERVER1> SELECT volume_name,devclass_name,stgpool_name,pct_reclaim,pct_utilized FROM volumes -
  WHERE status='FULL' AND pct_utilized < 10
  
  VOLUME_NAME         DEVCLASS_NAME          STGPOOL_NAME         PCT_RECLAIM     PCT_UTILIZED
  ---------------     ------------------     ----------------     -----------     ------------
  R00010L3            3584                   NT_DAILY                   94.9              5.2
  R00015L3            3584                   AIX_DDAILY                 99.9              0.0
  R00026L3            3584                   NT_DAILY                   94.2              6.0
  R00028L3            3584                   AIX_DAILY                  99.9              0.0
  ...
```

### Full volumes with reclaimable space (%) greater than XX

```
  tsm: SERVER1> SELECT volume_name,devclass_name,stgpool_name,pct_reclaim,pct_utilized FROM volumes -
  WHERE status='FULL' AND pct_reclaim >90
  
  VOLUME_NAME         DEVCLASS_NAME          STGPOOL_NAME         PCT_RECLAIM     PCT_UTILIZED
  ---------------     ------------------     ----------------     -----------     ------------
  R00010L3            3584                   NT_DAILY                   94.9              5.2
  R00015L3            3584                   AIX_DAILY                  99.9              0.0
  R00026L3            3584                   NT_DAILY                   94.2              6.0
  R00028L3            3584                   AIX_DAILY                  99.9              0.0
  ...
```

### Full volumes with reclaimable space (%) greater than XX in the library

```
  tsm: SERVER1> SELECT volumes.volume_name, volumes.stgpool_name, volumes.pct_utilized, volumes.pct_reclaim, - 
  volumes.status, volumes.access FROM volumes, libvolumes WHERE volumes.volume_name=libvolumes.volume_name -
  AND volumes.status='FULL' AND volumes.pct_reclaim>80 ORDER BY stgpool_name
  
  VOLUME_NAME         STGPOOL_NAME        PCT_UTILIZED  PCT_RECLAIM  STATUS              ACCESS
  ------------------  ------------------  ------------  -----------  ------------------  ------------------
  256AFB              NIGHTLY                     12.4         87.5  FULL                READWRITE
  295AFB              NIGHTLY                     11.3         88.6  FULL                READWRITE
  ...
```

### Volumes in a specific storage pool with reclaimable space (%) greater than XX

```
  tsm: SERVER1> SELECT volume_name,devclass_name,stgpool_name,pct_reclaim,pct_utilized FROM volumes -
  WHERE pct_reclaim>80 AND stgpool_name='OFFSITE'
  
  VOLUME_NAME            DEVCLASS_NAME          STGPOOL_NAME           PCT_RECLAIM     PCT_UTILIZED
  ------------------     ------------------     ------------------     -----------     ------------
  tape11                 LTO                    OFFSITE                       99.9              0.0
  tape84                 LTO                    OFFSITE                       85.0             15.0
  tape86                 LTO                    OFFSITE                       90.3              9.6
  tape90                 LTO                    OFFSITE                       90.3              9.6
  ...
```

### Number of tapes per storage pool in the library

```
  tsm: SERVER1> SELECT volumes.stgpool_name, count(*) FROM volumes, libvolumes WHERE -
  volumes.volume_name=libvolumes.volume_name GROUP BY stgpool_name
  
  STGPOOL_NAME            Unnamed[2]
  ------------------     -----------
  AIX_DAILY                      338
  AIX_ARCH1                       22
  ...
```

### False private tapes

```
  tsm: SERVER1> SELECT volume_name FROM libvolumes WHERE status='Private' AND last_use IS NULL AND -
  volume_name NOT IN (SELECT volume_name FROM volumes )
  
  VOLUME_NAME
  ---------------
  TAPE01L4
  TAPE01L4
  TAPE01L4
  TAPE01L4
```

### Some information about volumes in the library

```
  tsm: SERVER1> SELECT volume_name, stgpool_name, pct_utilized, pct_reclaim, status, access FROM volumes - 
    WHERE volume_name IN ( SELECT volume_name FROM libvolumes )
  
  
  VOLUME_NAME       STGPOOL_NAME      PCT_UTILIZED  PCT_RECLAIM  STATUS          ACCESS
  ----------------  ----------------  ------------  -----------  --------------  ------------
  290AFB            AIX_DAILY                 59.3         41.2  FILLING         READWRITE
  241AFB            AIX_DAILY                 59.8         40.1  FULL            READWRITE
  265AFB            NT_MONTHLY                 0.4          0.1  FILLING         READWRITE
  365AFB            AIX_ARCH1                 47.7          0.0  FILLING         READWRITE
  ...
```

### Some information about volumes in the library - another way

```
  tsm: SERVER1> SELECT volumes.volume_name, volumes.stgpool_name, volumes.pct_utilized, volumes.pct_reclaim, volumes.status, -
  volumes.access FROM volumes, libvolumes WHERE volumes.volume_name=libvolumes.volume_name ORDER BY stgpool_name
  
  
  VOLUME_NAME         STGPOOL_NAME        PCT_UTILIZED  PCT_RECLAIM  STATUS              ACCESS
  ------------------  ------------------  ------------  -----------  ------------------  ------------------
  290AFB              AIX_DAILY                   59.3         41.2  FILLING             READWRITE
  241AFB              AIX_DAILY                   59.8         40.1  FULL                READWRITE
  265AFB              NT_MONTHLY                   0.4          0.1  FILLING             READWRITE
  365AFB              AIX_ARCH1                   47.7          0.0  FILLING             READWRITE
  ...
```

### Nodes that have data stored in a specifc volume

```
  tsm: SERVER1> SELECT DISTINCT node_name, volume_name, stgpool_name FROM volumeusage WHERE volume_name='TAPE10'
  
  NODE_NAME              VOLUME_NAME            STGPOOL_NAME
  ------------------     ------------------     ------------------
  NODE45                 TAPE10                 DAILY
  NODE10                 TAPE10                 DAILY
  NODE33                 TAPE10                 DAILY
  NODE20                 TAPE10                 DAILY
```

### Number of nodes that have data stored per volume

```
  tsm: SERVER1> SELECT volume_name, stgpool_name, COUNT(DISTINCT node_name) AS "Number of Nodes" FROM -
  volumeusage GROUP BY volume_name, stgpool_name
  
  VOLUME_NAME           STGPOOL_NAME          Number of Nodes
  -----------------     -----------------     ---------------
  TA0016L4              DAILY                              31
  TA0017L4              DAILY                               1
  TA0018L4              WEEKLY                             30
  TA0019L4              DAILY                              44
  TA0023L4              DAILY                               1
  ...
```

### Number of volumes in the library per owner (useful in a library manager environment)

```
  tsm: SERVER1> SELECT owner,count(*) FROM libvolumes WHERE status<>'Scratch' GROUP BY owner
  
  OWNER                   Unnamed[2]
  ------------------     -----------
  library_client_1               141
  library_client_2               105
  library_client_3                53
  library_client_4               101
  library_server                 257
```

# Storage Pools

### Compare size and number of files between two storage pools

```
  tsm: SERVER1> SELECT stgpool_name,SUM(logical_mb)AS Logical_MB,SUM(num_files)AS Num_Files FROM -
  occupancy WHERE stgpool_name='DAILY' OR stgpool_name='COPY_DAILY' GROUP BY stgpool_name
  
  STGPOOL_NAME                        LOGICAL_MB       NUM_FILES
  ----------------     -------------------------     -----------
  DAILY                               1277890.99          350851
  COPY_DAILY                          1246583.48          350639
```

### Utilization (%) of storage pool disk_pool

```
  tsm: SERVER1> SELECT pct_utilized FROM stgpools WHERE stgpool_name='DISK_POOL'
  
  PCT_UTILIZED
  ------------
          20.9
```

### Maximum scratch volumes allowed and number of volumes used per stgpool (needs tsm version +5.3)

```
  tsm: SERVER1>SELECT stgpool_name,devclass,maxscratch,numscratchused FROM stgpools
  
  STGPOOL_NAME           DEVCLASS                MAXSCRATCH     NUMSCRATCHUSED
  ------------------     ------------------     -----------     --------------
  DAILY                  3584                          1100                521
```

# Volume History

### Number of full tsm db backups in the last 24 hours

```
  tsm: SERVER1> SELECT COUNT(*) FROM volhistory WHERE -
  type='BACKUPFULL' AND date_time>=current_timestamp-24 hours
  
   Unnamed[1]
  -----------
            1
```

### Number of full or incremental tsm db backups in the last 24 hours

```
  tsm: SERVER1> SELECT COUNT(*) FROM volhistory WHERE ( type='BACKUPFULL' OR type='BACKUPINCR' ) -
  AND date_time>=current_timestamp-24 hours
  
   Unnamed[1]
  -----------
            2
```

### Information about tsm db backups in the last 48 hours

```
  tsm: SERVER1> SELECT date_time, type, backup_series, volume_seq, devclass, volume_name FROM volhistory WHERE -
  ( type='BACKUPFULL' OR type='BACKUPINCR' OR type='DBSNAPSHOT' ) AND date_time>=current_timestamp-48 hours
  
          DATE_TIME     TYPE               BACKUP_SERIES     VOLUME_SEQ     DEVCLASS           VOLUME_NAME
  -----------------     --------------     -------------     ----------     --------------     --------------
         2008-11-19     BACKUPFULL                  3878              1     3584               TAPE10
    04:01:55.000000
         2008-11-20     BACKUPFULL                  3879              1     3584               TAPE48
    04:02:20.000000
```

# DRM

### Information about drm volumes

```
  tsm: SERVER1> SELECT drmedia.volume_name, volumes.stgpool_name, drmedia.state, drmedia.voltype, volumes.status, -
  volumes.pct_utilized FROM drmedia, volumes WHERE drmedia.volume_name=volumes.volume_name ORDER BY drmedia.state
  
  VOLUME_NAME         STGPOOL_NAME        STATE               VOLTYPE       STATUS              PCT_UTILIZED
  ------------------  ------------------  ------------------  ------------  ------------------  ------------
  tape06              OFFSITE             COURIERRETRIEVE     CopyStgPool   EMPTY                        0.0
  tape18              OFFSITE             VAULT               CopyStgPool   FILLING                     50.6
  tape38              OFFSITE             VAULT               CopyStgPool   FILLING                     80.9
  tape79              OFFSITE             VAULT               CopyStgPool   FILLING                     91.0
  ...
```

### Information about drm volumes in the library

```
  tsm: SERVER1> SELECT drmedia.volume_name, drmedia.state, drmedia.voltype FROM drmedia, libvolumes WHERE -
  drmedia.volume_name=libvolumes.volume_name ORDER BY voltype
  
  VOLUME_NAME            STATE                  VOLTYPE
  ------------------     ------------------     ------------
  tape48                 MOUNTABLE              CopyStgPool
  tape59                 MOUNTABLE              CopyStgPool
  ...
```

### Information about drm volumes in the library (another way)

```
  tsm: SERVER1> SELECT volume_name, state, voltype FROM drmedia WHERE -
  volume_name IN ( SELECT volume_name FROM libvolumes ) ORDER BY voltype
  
  VOLUME_NAME            STATE                  VOLTYPE
  ------------------     ------------------     ------------
  tape48                 MOUNTABLE              CopyStgPool
  tape59                 MOUNTABLE              CopyStgPool
  ...
```

### Information about drm volumes in the library with state different from “MOUNTABLE”

```
  tsm: SERVER1> SELECT drmedia.volume_name, drmedia.state, drmedia.voltype FROM drmedia, libvolumes WHERE -
  drmedia.volume_name=libvolumes.volume_name AND drmedia.state<>'MOUNTABLE'
  
  VOLUME_NAME            STATE                  VOLTYPE
  ------------------     ------------------     ------------
  tape36                 COURIER                CopyStgPool
  tape82                 COURIER                CopyStgPool
  ...
```

### Drm volumes with tsm db backups

```
  tsm: SERVER1> SELECT volume_name, state, upd_date, location, voltype FROM drmedia -
  WHERE voltype='DBBackup' OR voltype='DBSnapshot'
  
  VOLUME_NAME            STATE                            UPD_DATE     LOCATION               VOLTYPE
  ------------------     ------------------     ------------------     ------------------     ------------
  tape10                 VAULT                          2008-03-05     Iron Mountain          DBBackup
                                                   11:00:00.000000
  tape15                 VAULT                          2008-03-04     Iron Mountain          DBBackup
                                                   11:00:00.000000
  tape45                 VAULT                          2008-03-03     Iron Mountain          DBBackup
  ...
```

### Number of Volumes per DRM State

```
  tsm: SERVER1> SELECT state,count(*) as "Number of volumes" FROM drmedia GROUP BY state
  
  STATE                  Number of volumes
  ------------------     -----------------
  COURIERRETRIEVE                       26
  MOUNTABLE                              2
  VAULT                                 76
  VAULTRETRIEVE                          1
```

# Sessions

### Number of nodes sessions

```
  tsm: SERVER1> SELECT COUNT(*) FROM sessions WHERE session_type='Node'
   
     Unnamed[1]
    -----------
             16
```

### Number of nodes sessions in Media Wait state

```
  tsm: SERVER1> SELECT COUNT(*) FROM sessions WHERE session_type='Node' AND state='MediaW'
  
   Unnamed[1]
  -----------
            1
```

### Nodes sessions in Media Wait state

```
  tsm: SERVER1> SELECT client_name, session_id, start_time, state, mount_point_wait, input_mount_wait, input_vol_wait -
  FROM sessions WHERE state='MediaW'
  
  CLIENT_NAME     SESSION_ID           START_TIME    STATE       MOUNT_POINT_WAIT     INPUT_MOUNT_WAIT    INPUT_VOL_WAIT
  -------------   -----------  ------------------   ---------  ------------------   ------------------  ----------------
  NODE23              1577742          2008-11-21   MediaW                            ,F00827,81
                                  11:26:03.000000
  NODE15              1581236          2008-11-21   MediaW
                                  11:37:06.000000
```

### Nodes using tapes (drives)

```
  tsm: SERVER1> SELECT client_name, session_id, start_time, state, bytes_sent, bytes_received, input_vol_access, output_vol_access -
  FROM sessions WHERE ( input_vol_access is not NULL OR output_vol_access is not NULL )
  
  CLIENT_NAME    SESSION_ID         START_TIME STATE         BYTES_SENT     BYTES_RECEIVED INPUT_VOL_ACCESS   OUTPUT_VOL_ACCESS
  ------------- ----------- ------------------ --------- -------------- ------------------ ------------------ ------------------
  NODE10            1578627         2008-11-21 RecvW                476         2913518005                     ,3M0922,1214
                               08:37:41.000000
  NODE25            1578776         2008-11-21 RecvW                540          123087561                     ,F01091,117
                               08:46:52.000000
```

### Information about sessions from a specific node

```
  tsm: SERVER1> SELECT session_id, start_time, commmethod, state, wait_seconds, CAST(bytes_sent/1024/1024 AS DEC(8,2)) AS "MB_Sent", -
  CAST(bytes_received/1024/1024 AS DEC(8,2)) AS "MB_Rcvd", mount_point_wait FROM sessions WHERE client_name='MY_NODE'
  
   SESSION_ID           START_TIME   COMMMETHOD         STATE         WAIT_SECONDS      MB_Sent      MB_Rcvd   MOUNT_POINT_WAIT
  -----------   ------------------   ----------------   -----------   ------------   ----------   ----------   ------------------
      1569587           2008-11-20   Tcp/Ip             RecvW                    0         0.00      1648.92
                   10:23:37.000000
```

### Performance of nodes sessions

```
  tsm: SERVER1> SELECT client_name,session_id, current_timestamp-start_time AS ElapTime, commmethod, state, -
  CAST(bytes_sent/1024/1024 AS DEC(8,2)) AS "MB_Sent", CAST(bytes_received/1024/1024 AS DEC(8,2)) AS "MB_Rcvd", -
  cast((cast(bytes_sent as dec(18,0))/cast((current_timestamp-start_time) seconds as decimal(18,0))) / 1024 / 1024 AS DEC (18,2)) AS "Sent_MB/s", -
  cast((cast(bytes_received as dec(18,0))/cast((current_timestamp-start_time) seconds as decimal(18,0))) / 1024 / 1024 AS DEC (18,2)) AS "Rcvd_MB/s" -
  FROM sessions WHERE session_type='Node'
  
  CLIENT_NAME    SESSION_ID              ELAPTIME COMMMETHOD      STATE        MB_Sent    MB_Rcvd    Sent_MB/s     Rcvd_MB/s
  ------------- ----------- --------------------- --------------- --------- ---------- ---------- ------------ -------------
  NODE10              76499     0 20:53:40.000000 Tcp/Ip          Run             0.03  402998.64         0.00          5.35
  NODE34              76500     0 20:53:40.000000 Tcp/Ip          RecvW           0.03  398363.23         0.00          5.29
  NODE28              76501     0 20:52:18.000000 Tcp/Ip          RecvW           0.02  370801.49         0.00          4.93
  NODE79              76502     0 20:52:01.000000 Tcp/Ip          Run             0.03  443600.35         0.00          5.90
  ...
```

# Backups

### Search a specific file from a Node

```
  tsm: SERVER1> SELECT * FROM backups WHERE node_name='MY_NODE' AND ll_name='dsm.opt'
  
        NODE_NAME: MY_NODE
   FILESPACE_NAME: /opt
     FILESPACE_ID: 6
            STATE: ACTIVE_VERSION
             TYPE: FILE
          HL_NAME: /tivoli/tsm/client/ba/bin/
          LL_NAME: dsm.opt
        OBJECT_ID: 8395325
      BACKUP_DATE: 2008-11-03 19:02:35.000000
  DEACTIVATE_DATE:
            OWNER: root
       CLASS_NAME: DEFAULT
  
        NODE_NAME: MY_NODE
   FILESPACE_NAME: /opt
     FILESPACE_ID: 6
            STATE: ACTIVE_VERSION
             TYPE: FILE
          HL_NAME: /tivoli/tsm/client/domino/bin/domdsmc_notesb/
          LL_NAME: dsm.opt
        OBJECT_ID: 8091124
      BACKUP_DATE: 2008-10-27 19:14:35.000000
  DEACTIVATE_DATE:
            OWNER: notesuser
       CLASS_NAME: DEFAULT
  
        NODE_NAME: MY_NODE
   FILESPACE_NAME: /opt
     FILESPACE_ID: 6
            STATE: INACTIVE_VERSION
             TYPE: FILE
          HL_NAME: /tivoli/tsm/client/ba/bin/
          LL_NAME: dsm.opt
        OBJECT_ID: 8091063
      BACKUP_DATE: 2008-10-27 19:14:34.000000
  DEACTIVATE_DATE: 2008-11-03 19:02:35.000000
            OWNER: root
       CLASS_NAME: DEFAULT
```

### Search a specific file from a node with more details

```
  tsm: SERVER1> SELECT * FROM backups WHERE node_name='MY_NODE' AND filespace_name='/opt' -
  AND hl_name='/tivoli/tsm/client/ba/bin/' AND ll_name='dsm.opt'
  
        NODE_NAME: MY_NODE
   FILESPACE_NAME: /opt
     FILESPACE_ID: 6
            STATE: ACTIVE_VERSION
             TYPE: FILE
          HL_NAME: /tivoli/tsm/client/ba/bin/
          LL_NAME: dsm.opt
        OBJECT_ID: 8395325
      BACKUP_DATE: 2008-11-03 19:02:35.000000
  DEACTIVATE_DATE:
            OWNER: root
       CLASS_NAME: DEFAULT
  
        NODE_NAME: MY_NODE
   FILESPACE_NAME: /opt
     FILESPACE_ID: 6
            STATE: INACTIVE_VERSION
             TYPE: FILE
          HL_NAME: /tivoli/tsm/client/ba/bin/
          LL_NAME: dsm.opt
        OBJECT_ID: 8091063
      BACKUP_DATE: 2008-10-27 19:14:34.000000
  DEACTIVATE_DATE: 2008-11-03 19:02:35.000000
            OWNER: root
       CLASS_NAME: DEFAULT
```

### Objects backed up of a specific node in the last 24 hours

```
  tsm: SERVER1> SELECT backup_date,filespace_name,type,hl_name,ll_name,owner, class_name FROM backups -
  WHERE node_name='MY_NODE' AND backup_date>=current_timestamp-24 hours
  
       BACKUP_DATE  FILESPACE_NAME           TYPE  HL_NAME          LL_NAME             OWNER        CLASS_NAME
  ----------------  -----------------  ----------  ---------------  ------------------  -----------  -------------
        2008-11-19  /                        FILE  /etc/            mtab                root         DEFAULT
   19:04:08.000000
        2008-11-19  /                        FILE  /etc/            showdasd.list       root         DEFAULT
   19:04:08.000000
        2008-11-19  /                        FILE  /etc/            sudoers             root         DEFAULT
   19:04:08.000000
       2008-11-19   /home                    FILE  /support/        .bash_history       support      DEFAULT
   19:03:25.000000
```

# Processes

### Information about the currently running processes

```
  tsm: SERVER1> SELECT process_num, process, -
  substr(char(start_time),1,19) AS START_TIME, -
  substr(char(current_timestamp - start_time),1,10) AS "ELAPTIME (D HHMMSS)", -
  cast(float(bytes_processed) /1024/1024 AS DEC(8,2)) AS MB, -
  cast((cast(bytes_processed as dec(18,0))/cast((current_timestamp-start_time) seconds as decimal(18,0))) / 1024 / 1024 AS DEC (18,2)) AS "MB/s" -
  FROM processes
  
  PROCESS_NUM   PROCESS              START_TIME        ELAPTIME (D HHMMSS)           MB         MB/s
  -----------   ------------------   ---------------   -------------------   ----------   ----------
           27   Space Reclamation    2008-11-22        0 02:28:26              58925.78         6.61
                                      12:00:29
           28   Migration            2008-11-22        0 00:23:01              46425.55        33.61
                                      14:05:54
           29   Migration            2008-11-22        0 00:23:01              37984.68        27.50
                                      14:05:54
           30   Migration            2008-11-22        0 00:23:01              41261.84        29.87
                                      14:05:54
           31   Migration            2008-11-22        0 00:23:01              39817.22        28.83
                                      14:05:54
           32   Migration            2008-11-22        0 00:23:01              41910.42        30.34
                                      14:05:54
           33   Migration            2008-11-22        0 00:23:01              43771.08        31.69
                                      14:05:54
```

# Other

### Total client data stored (TB)

```
  tsm: SERVER1> SELECT CAST(FLOAT(SUM(logical_mb)) / 1024 / 1024 AS DEC(8,2)) FROM occupancy
  
  Unnamed[1]
  ----------
       73.04
```

### Total client data stored (TB) (another way - auditocc is updated by audit lic command, take care)

```
  tsm: SERVER1> SELECT CAST(FLOAT(SUM(total_mb)) / 1024 / 1024 AS DEC(8,2)) FROM auditocc
  
  Unnamed[1]
  ----------
       72.46
```

### Some TSM Server information

```
  tsm: SERVER1> SELECT server_name, platform, - 
  VARCHAR(version)||'.'||VARCHAR(release)||'.'||VARCHAR(level)||'-'||VARCHAR(sublevel), -
  server_hla, server_lla, server_url, logmode, crossdefine, licensecompliance FROM status
  
        SERVER_NAME: TSM-SERVER1
           PLATFORM: AIX-RS/6000
         Unnamed[3]: 5.3.3-2
         SERVER_HLA: 10.10.10.5
         SERVER_LLA: 1500
         SERVER_URL:
            LOGMODE: NORMAL
        CROSSDEFINE: ON
  LICENSECOMPLIANCE: VALID
```

### SQL Table Catalog

```
  tsm: SERVER1>SELECT tabschema,tabname,remarks FROM tables
  
  TABSCHEMA     TABNAME                REMARKS
  ---------     ------------------     ------------------
  ADSM          ACTLOG                 Server activity log
  ADSM          ADMINS                 Server administrators
  ADSM          ADMIN_SCHEDULES        Administrative command schedules
  ADSM          ARCHIVES               Client archive files
  ADSM          AR_COPYGROUPS          Management class archive copy groups
  ADSM          ASSOCIATIONS           Client schedule associations
  ADSM          AUDITOCC               Server audit occupancy results
  ADSM          BACKUPS                Client backup files
  ADSM          BACKUPSETS             Backup Set
  ADSM          BU_COPYGROUPS          Management class backup copy
  ...
```

**Other links about this topic:**

- <http://www.lascon.co.uk/d005104.htm>
- <http://tsmwiki.com/tsmwiki/SelectStatements/>
- <http://tsm-symposium.oucs.ox.ac.uk/2003/papers/AndyLauraRobert.pdf>
- <http://www.general-storage.de/tsmsqlbt01.html>
- <http://www.general-storage.de/tsmsqlat01.html>
- <http://adsm.org/forum/forumdisplay.php?f=16>

**Selects V6.x**

- [SELECT command V6.x - known problems](https://www-304.ibm.com/support/docview.wss?uid=swg21380830)
- [SELECT command changes in V6.1](http://publib.boulder.ibm.com/infocenter/tsminfo/v6/topic/com.ibm.itsm.srv.upgrd.doc/r_srv_upgrd_cmd_select.html)
- [SELECT command changes in V6.2](http://publib.boulder.ibm.com/infocenter/tsminfo/v6r2/topic/com.ibm.itsm.srv.upgrd.doc/r_srv_upgrd_cmd_select.html)
