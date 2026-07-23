---
title: "Ntop"
date: 2014-01-20T16:09:21-03:00
---

ntop (de Network Top) es una herramienta que permite monitorizar en tiempo real una red. Es útil para controlar los usuarios y aplicaciones que están consumiendo recursos de red en un instante concreto y para ayudarnos a detectar malas configuraciones de algún equipo, (facilitando la tarea ya que. justo al nombre del equipo, aparece sale un banderín amarillo o rojo, dependiendo si es un error leve o grave), o a nivel de servicio. Posee un microservidor web desde el que cualquier usuario con acceso puede ver las estadísticas del monitorizaje.

Los protocolos que es capaz de monitorizar son: TCP/UDP/ICMP, (R)ARP, IPX, DLC, Decnet, AppleTalk, Netbios, y ya dentro de TCP/UDP es capaz de agruparlos por FTP, HTTP, DNS, Telnet, SMTP/POP/IMAP, SNMP, NFS, X11.

<img src="/manuales/logo_ntop.png" class="align-right" />

## Uso

Dentro del microservidor web podemos escoger que ver, en menú de navegación principal se encuentra en el frame de arriba, que nos permite ver las siguientes opciones:

#### About

Muestra una explicación del programa, así como los créditos de las personas lo han hecho.

#### Data Rcvd, Data Sent

Nos enseña que datos se han recibido/transmitido. Las posibilidades para visualizarlo es agrupándolo por protocolos, por TCP/UDP, qué cantidad se ha tratado, la actividad de cada host, y netflows.

#### Stats

Es el apartado de estadísticas, en la que nos enseña información muy completa acerca del estado de la red. Nos enseña si es tráfico unicast, o multicast, la longitud de los paquetes, el Time To Live del paquete, y el tipo de tráfico que viaja (todo ello con porcentajes). También saca un listado de dominios, y qué plugins podemos activar o desactivar.

#### IP Traffic

Nos da información acerca del sentido del tráfico, si va de la red local a una red remota, o viecevera. también nos informa de algun error

#### IP Protos

Nos da la informacion sobre los interfaz que hay enganchados en la red

#### Admin

Sirve para poder cambiar la inerfaz de red, crear filtros, y un mantenimiento de usuarios.

## Start ntop

```
root@ntop:~# ntop -u root -i none -d -W 3001
```

## Files ntop

```
ntop files in /usr/local according to ./configure:Data files are in     /usr/local/share/ntop
Config files are in   /usr/local/etc/ntop
Run directory is      /usr/local/var/ntop
Plugin files are in   /usr/local/lib/ntop/plugins
Database files are in /usr/local/var/ntop
Libraries have been installed in: /usr/local/lib
```
