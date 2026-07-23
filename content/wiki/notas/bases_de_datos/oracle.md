---
title: "Oracle"
date: 2017-08-17T12:07:38-03:00
---

- [Hugepages en Linux](/notas/linux/manejo_de_memoria_en_linux/hugepages/)
- [An Introduction to Linux Shell Scripting for DBAs](/manuales/an_introduction_to_linux_shell_scripting_for_dbas/)
- [Oracle 11g: Usage of hugepages on Linux](/notas/linux/manejo_de_memoria_en_linux/hugepages/)

## Notas de Oracle

List all users/schema

```sql>
 
List ONLY your tables
<code sql>
         SET PAGESIZE 80
         COL TABLE__NAME FORMAT a30;
         COL TABLE_TYPE FORMAT a30;
         SELECT * FROM CAT;
```

List the Oracle Tablespaces and free space

```sql>
 
List the free space for all Oracle Tablespaces within a Database
<code sql>
         SELECT * from DBD_FREE_SPACE where TABLESPACE_NAME = 'IATOR';
```

List the space ALREADY USED for a specific Oracle Tablespace

```sql>
 
List extents for segments
<code sql>
         SELECT TABLESPACE_NAME, count(*), max(blocks), sum(blocks) from dba_free_space group by TABLESPACE_name;
```

See all the tables you have access to:

```sql>
 
See your TABLESPACE
<code sql>
         SELECT TABLESPACE_NAME from ALL_TABLES where TABLE_NAME = '<your_table>'
```

Consulta para calcular el espacio total, el ocupado y el espacio libre de un TABLESPACE :

```sql>
Consulta para conocer sesiones activas
<code sql>
select count(SES.SID)
from  v\$session SES, V\$SQLAREA SQL, V\$SESSION_WAIT WA, V\$PROCESS P
where SES.STATUS='\''ACTIVE'\''
AND SES.SQL_ID=SQL.SQL_ID
AND SES.SID=WA.SID
and SES.paddr=p.addr
and SES.USERNAME not in ('\''SYS'\'','\''SYSMAN'\'','\''MDSYS'\'')
```

Otorgar acceso de lectura a todas las tablas a determinado usuario

```sql>
Volcar un último archive log
<code sql>
SQL> alter system archive log current; 
```

## Referencias oficiales

- DB_BLOCK_SIZE
  - 11g - <http://docs.oracle.com/cd/E11882_01/server.112/e40402/initparams050.htm#REFRN10031>
  - 12c - <http://docs.oracle.com/database/121/REFRN/GUID-6DD6D804-D03D-4C57-A764-826EE0F3836B.htm#REFRN10031>
