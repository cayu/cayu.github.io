---
title: "Cisco Call Manager"
date: 2019-11-20T12:21:16-03:00
---

## Administración

### Teléfonos

#### Habilitar SSH

<https://community.cisco.com/t5/collaboration-voice-and-video/how-to-collect-syslog-and-debug-info-from-9971-9951-8961-model/ta-p/3122096>

### Internos

#### Tiempo de espera antes de perder la llamada

Para modificar el tiempo de espera, desde la configuración del interno, deberemos modificar la casilla **No Answer Ring Duration**.

## Extras

### Libreta de direcciones por LDAP

#### Problemas comunes

Si tenemos usuarios en estado *Inactive LDAP Synchronized User* y necesitamos pasarlos a estado *Active LDAP Synchronized User*. Deberemos ejecutar lo siguiente :

```sql>
<code sql>
run sql update enduser set status=1 where status = "2"
```

### Extraer datos por SQL

Listas Nombre de telefono, Descripcion, DN

```sql
run sql select d.name, d.description, n.dnorpattern as DN
from device as d,numplan as n, devicenumplanmap as dnpm
where dnpm.fkdevice = d.pkid and dnpm.fknumplan = n.pkid and d.tkclass = 1
```

### Scripts para importar LOGS de Cisco Call Manager en Mysql o PostgreSQL

[cdr.tgz](/notas/cdr.tgz)
