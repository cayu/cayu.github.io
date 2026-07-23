---
title: "Bases De Datos"
date: 2013-09-13T11:21:35-03:00
---

## Equivalencias MySQL - PostgreSQL

### Perl DBI

| **MySQL** | **PostgreSQL** |
|----|----|
| \$db=DBI-\>connect(“dbi:mysql:database= … )\|\$db=DBI-\>connect(”dbi:Pg:database= … ) |  |

### Sintaxis y lenguaje

| **MySQL** | **PostgreSQL** |
|----|----|
| SHOW FULL PROCESSLIST / SHOW PROCESSLIST | SELECT \* FROM pg_stat_activity |
| \# | – |
| … WHERE lastname=“smith” | … WHERE lower(lastname)=‘smith’ |
| DESCRIBE tablename | \d tablename |
| DROP TABLE IF EXISTS | DROP TABLE IF EXISTS |
| SELECT … INTO OUTFILE ‘/var/tmp/outfile’ | COPY ( SELECT … ) TO ‘/var/tmp/outfile’ |
| SHOW DATABASES | \l |
| SHOW TABLES | \dt |

### Tipos de datos

| MySQL | PostgreSQL | comments |
|-------|------------|----------|

\|

```
TINYINT
SMALLINT
MEDIUMINT
BIGINT
```

\|

```
SMALLINT
SMALLINT
INTEGER
BIGINT
```

|  |
|----|
| see [\[2](http://www.postgresql.org/docs/8.1/interactive/datatype.html#DATATYPE-INT)\]; `integer` size in PostgreSQL is 4 Bytes signed (-2147483648 – +2147483647) |

\|

```
TINYINT UNSIGNED
SMALLINT UNSIGNED
MEDIUMINT UNSIGNED
INT UNSIGNED
BIGINT UNSIGNED
```

\|

```
SMALLINT
INTEGER
INTEGER
BIGINT
NUMERIC(20)
```

|                                                        |
|--------------------------------------------------------|
| SQL doesn’t know `UNSIGNED`, all numbers are signed. |

\|

```
ZEROFILL
```

|     |                 |
|-----|-----------------|
|     | No replacement. |

\|

```
DOUBLE
```

\|

```
DOUBLE PRECISION
```

|     |
|-----|
|     |

\|

```
BOOLEAN
```

\|

```
BOOLEAN
```

|  |
|----|
| MySQL Booleans are an alias for TINYINT(1); PostgreSQL doesn’t auto-convert numbers into booleans. |

\|

```
DATE
TIME
DATETIME
```

\|

```
DATE
TIME
TIMESTAMP
```

|     |
|-----|
|     |

\|

```
TIMESTAMP NOT NULL DEFAULT NOW()
```

\|

```
TIMESTAMP NOT NULL DEFAULT NOW()
```

|     |
|-----|
|     |

\|

```
LONGTEXT
MEDIUMTEXT
BLOB
```

\|

```
TEXT
TEXT
BYTEA
```

\|

```
TEXT
```

\| \|

```
columnname INT AUTO_INCREMENT
```

\|

```
columnname SERIAL
```

equals to:

```
CREATE SEQUENCE tablename_columnname_seq;
CREATE TABLE tablename ( 
  columnname INT4 NOT NULL
    DEFAULT nextval('tablename_columnname_seq')
);
```

|  |
|----|
| SERIAL = 1 – 2147483647BIGSERIAL = 1 – 9223372036854775807 SERIAL is in fact an entity named SEQUENCE. It exists independently on the rest of your table. If you want to cleanup your system after dropping a table, you also have to `DROP SEQUENCE //tablename//_//columnname//_seq`. [More on that topic...](http://www.sitepoint.com/article/site-mysql-postgresql-1/3) |

\|

```
field ENUM (val_1, val_2, ...)
```

\|

```
field VARCHAR(255) NOT NULL,
CHECK (field IN (val_1, val_2, ...))
```

OR

```
CREATE TYPE mood AS ENUM ('sad','ok','happy');
  CREATE TABLE person ( current_mood mood ...
```

|  |
|----|
| PostgreSQL doesn’t have the ENUM types prior to 8.3, so you need to simulate it with contraints when using \< 8.3. |

**Ref.:** <http://en.wikibooks.org/wiki/Converting_MySQL_to_PostgreSQL>

## Tips MySQL

Borrar todas las tablas de una base de datos

```
MYSQL="mysql -h HOST -u USERNAME -pPASSWORD -D DB_NAME"
$MYSQL -BNe "show tables" | awk '{print "set foreign_key_checks=0; drop table `" $1 "`;"}' | $MYSQL
unset MYSQL
```

También podemos usar la sintaxys LIKE para borrar solo algunas tablas que concuerden con algún patrón, útil cuando tenemos un programa que genera muchas tablas con un timestamp añadido

```
show tables like 'ifvolume_1%'
```

### Replicacion facil

```
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

old_passwords=1
log_bin = /var/log/mysql/mysql-bin.log
relay-log = /var/log/mysql/mysql-relay.log
max_binlog_size = 100M

expire_logs_days        = 2
max_binlog_size         = 100M

log-bin
binlog-do-db=proxyauth
skip-slave-start

server-id=1
replicate-do-db=base
replicate-do-table=base.tabla

master-host=10.1.1.1
master-user=root
master-password=contraseña
master-port=3306

[mysql.server]
user=mysql
basedir=/var/lib 
```

```
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

old_passwords=1
log_bin = /var/log/mysql/mysql-bin.log
relay-log = /var/log/mysql/mysql-relay.log
max_binlog_size = 100M

expire_logs_days        = 2
max_binlog_size         = 100M

log-bin
binlog-do-db=proxyauth
skip-slave-start

server-id=2
replicate-do-db=base
replicate-do-table=base.tabla

master-host=10.1.1.2
master-user=root
master-password=contraseña
master-port=3306

[mysql.server]
user=mysql
basedir=/var/lib 
```

**skip-slave-start** *Es para tener que hacer el start slave; manualmente (por si las dudas).*

### Importar/Exportar CSV

Importacion :

```sql>
Exportacion :
<code sql>
SELECT * FROM usuarios ORDER BY date DESC
INTO OUTFILE "usuarios.csv"
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\r\n';
```

```bash
mysql -u root -p  cdr -B -e  "select * from \`calldetails\` limit 2;" | sed 's/\t/","/g;s/^/"/;s/$/"/;s/\n//g'
```

## Oracle

### Oracle XE issues

Oracle xe must be the buggiest free counterpart of a commercial product ever released.Here’s a list of issues and solutions encountered on Ubuntu 10.04 so far.Few config tips first.

- I don’t set up it to be started upon boot (don’t want this bag of bugs eat my memory)
- change the default apex port from 8080 to smth else
- use 8+ characters for sys/system password
- check status of the DB: \$ sudo /etc/init.d/oracle-xe status
- start the DB - I use restart just to be sure: \$ sudo /etc/init.d/oracle-xe restart

Q: After installing and sourcing /usr/lib/oracle/xe/app/oracle/product/10.2.0/server/bin/oracle_env.sh I was getting /usr/lib/oracle/xe/app/oracle/product/10.2.0/server/bin/nls_lang.sh: 114: `[[`: not found each time starting a shell

A: Change the nls_lang.sh to use your shell `(/`bin/bash) instead of /bin/sh on top of the script.

Q: after installing oracle xe I cannot login to apex (oracle web admin) under sys or system. It won’t accept my login/password. Keeps saying access denied although I’m sure the password is correct (the one used during /etc/init.d/oracle-xe configure)

A: Use a longer password! It won’t complain but seems like it doesn’t like short passwords. Use at least 8 chars.Q:After some time your oracle connected application runs into

> ORA-12519, TNS:no appropriate service handler found

A: Run the following statement from sqlplus or web admin (you should have it accessible due to above tips)

> ALTER SYSTEM SET PROCESSES=150 SCOPE=SPFILE;

Found [here](http://en.newinstance.it/2007/06/01/ora-12519-tnsno-appropriate-service-handler-found/)

Q: How to remove oracle user from ubuntu desktop login screen.

A: Change the oracle’s UID to smth below 1000.

\$ sudo usermod –gid 999 oracle

After that change the ownership of the files in /var/tmp/.

oracle\$ sudo chown -R oracle:dba /var/tmp/.oracle/

or you won’t be able to start the DB anymore :)

Q: Cannot open Oracle web interface, Page cannot be displayed. Used to work before, and the DB is working.

A: Might happen especially if you selected to start the DB manually rather than on each boot. The symptoms might be a bit different, the DB might be started or might be not. Some services just fail to start.

Try restart :

\$ sudo /etc/init.d/oracle-xe restart or \$ sudo /etc/init.d/oracle-xe force-reload

Fuente: <http://ubuntu-answers.blogspot.com/2010/10/oracle-xe-issues.html>
