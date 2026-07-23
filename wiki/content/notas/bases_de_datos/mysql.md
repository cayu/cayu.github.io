---
title: "Task MySQL"
date: 2021-01-21T14:47:58-03:00
---

## Administración

### Scripts

Script para listar las tablas fragmentadas y correr un optimize

```bash
#!/bin/sh
echo -n "MySQL username: " ; read username
echo -n "MySQL password: " ; stty -echo ; read password ; stty echo ; echo

mysql -u $username -p"$password" -NBe "SHOW DATABASES;" | grep -v 'lost+found' | while read database ; do
mysql -u $username -p"$password" -NBe "SHOW TABLE STATUS;" $database | while read name engine version rowformat rows avgrowlength datalength maxdatalength indexlength datafree autoincrement createtime updatetime checktime collation checksum createoptions comment ; do
  if [ "$datafree" -gt 0 ] ; then
   fragmentation=$(($datafree * 100 / $datalength))
   echo "$database.$name is $fragmentation% fragmented."
   mysql -u "$username" -p"$password" -NBe "OPTIMIZE TABLE $name;" "$database"
  fi
done
done
```

## Manejo de datos

### MySQL y datos CSV

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

```bash>
==== Unix Time ====
<code sql>
SELECT * , FROM_UNIXTIME( `dateTimeOrigination` ) AS Fecha
FROM `calldetails`
WHERE `finalCalledPartyNumber` = '7420'
```

```sql>
==== MySQL y Wordpress ====
<code sql>
UPDATE wp_posts SET guid = replace(guid, 'http://dominioviejo.com','http://dominionuevo.com');
UPDATE wp_posts SET post_content = replace(post_content, 'http://dominioviejo.com', 'http://dominionuevo.com');
UPDATE wp_links SET link_url = replace(link_url, 'http://dominioviejo.com', 'http://dominionuevo.com');
UPDATE wp_links SET link_image = replace(link_image, 'http://dominioviejo.com', 'http://dominionuevo.com');
UPDATE wp_postmeta SET meta_value = replace(meta_value, 'http://dominioviejo.com', 'http://dominionuevo.com');
UPDATE wp_usermeta SET meta_value = replace(meta_value, 'http://dominioviejo.com', 'http://dominionuevo.com');

/*UPDATE wp_options SET option_value = replace(option_value, 'http://dominioviejo.com', 'http://dominionuevo.com') WHERE option_name = 'home' OR option_name = 'siteurl' OR option_name = 'widget_text' OR option_name = 'dashboard_widget_options';*/
UPDATE wp_options SET option_value = replace(option_value, 'http://dominioviejo.com', 'http://dominionuevo.com');
```

### Tamaños de bases y tablas

```sql>
<code sql>
SELECT table_name AS "Table",
ROUND(((data_length + index_length) / 1024 / 1024), 2) AS "Size (MB)"
FROM information_schema.TABLES
WHERE table_schema = "database_name"
ORDER BY (data_length + index_length) DESC;
```

### Migrar una base a InnoDB

```
# mysql base < mig_innodb.sql
```

```sql>
==== Comprimir una tabla ====
<code sql>
ALTER TABLE syslog key_block_size=8 row_format=compressed;
```
