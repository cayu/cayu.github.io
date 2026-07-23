---
title: "Nagios Network Analyzer"
date: 2017-11-28T11:59:50-03:00
---

Nagios Network Analyzer es dashboard gráfico para el análisis de datos de nfdump, soportando netflow y sflow.

## Funcionamiento

Básicamente le definimos fuentes de datos desde la interfaz web y se levantan procesos como estos :

```
nfcapd -I 1 -l /usr/local/nagiosna/var/Europa/flows -p 2058 -x /usr/local/nagiosna/bin/reap_files.py %d %f %i -P /usr/local/nagiosna/var/Europa/2058.pid -D -e -w -z
nfcapd -I 2 -l /usr/local/nagiosna/var/Argentina/flows -p 2055 -x /usr/local/nagiosna/bin/reap_files.py %d %f %i -P /usr/local/nagiosna/var/Argentina/2055.pid -D -e -w -z
nfcapd -I 3 -l /usr/local/nagiosna/var/Peru/flows -p 2056 -x /usr/local/nagiosna/bin/reap_files.py %d %f %i -P /usr/local/nagiosna/var/Peru/2056.pid -D -e -w -z
nfcapd -I 4 -l /usr/local/nagiosna/var/Transportes/flows -p 2057 -x /usr/local/nagiosna/bin/reap_files.py %d %f %i -P /usr/local/nagiosna/var/Transportes/2057.pid -D -e -w -z
nfcapd -I 6 -l /usr/local/nagiosna/var/Claro/flows -p 2059 -x /usr/local/nagiosna/bin/reap_files.py %d %f %i -P /usr/local/nagiosna/var/Claro/2059.pid -D -e -w -z
sfcapd -I 7 -l /usr/local/nagiosna/var/Fortigate/flows -p 2060 -x /usr/local/nagiosna/bin/reap_files.py %d %f %i -P /usr/local/nagiosna/var/Fortigate/2060.pid -D -e -w -z
```

Dicha interfaz web ejecuta como proceso de fondo el comando **nfdump** y lo muestra en el navegador interpretando un JSON :

```
http://190.1.1.10/nagiosna/api/graphs/queryviz?sid=2&q[begindate]=-2%20hours&q[enddate]=-1%20second&q[aggregate_csv]=srcip,dstip,dstport&q[rawquery]=net%2010.0.0.0/8&agg1=dstip&agg2=srcip&sortby=bytes
```

```shell>
Tabien le podemos agregar opciones extra al comando si lo ejecutamos desde la terminal, como :
<code>
-O bytes -o csv
```

## TIPS

Si las consultas personalizadas se vuelven muy lentas deberemos modificar estas variables de configuración.

**/etc/php.ini**

```ini>
= 50000
memory_limit >= 512M
max_execution_time >= 120
max_input_time >= 120
```

**/etc/httpd/conf/httpd.conf**

```
LimitRequestLine 1000000
```
