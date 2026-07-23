---
title: "Squid + Red Hat Cluster Suite"
date: 2010-08-23T11:52:24-03:00
---

Supongamos que tenemos dos servidores proxy 10.1.2.103 y 10.1.2.105 y ambos atienden por una ip virtual 10.1.2.106

## LVS

Configuracion de la utilidad IPVS de Linux Virtual Server

##### /etc/ipvsadm.rules

```
-A -t 10.1.2.106:8080 -s wlc
-a -t 10.1.2.106:8080 -r 10.1.2.105:8080 -g -w 1
-a -t 10.1.2.106:8080 -r 10.1.2.103:8080 -g -w 1
```

##### ipvsadm -L

```
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Forward Weight ActiveConn InActConn
TCP  lvs-proxy.cayu.com.ar:       web wlc
  -> proxy2.cayu.com.ar:web Route            1      0          2         
  -> proxy1.cayu.com.ar:web Local            1      1          1         
```

El sistema balancea por medio del algoritmo wlc.

# Piranha

Piranha es un producto de clustering de Red Hat, incluye el demonio de control de LVS de Red Hat, y una herramienta de configuracion web del cluster.

La herramienta de monitoreo de piranha tiene dos caracteristicas principales :

* Soporte integrado de Heartbeat.
* Chequeo de disponibilidad de servicios en los servidores.

La herramienta de monitoreo de piranha usa el proceso heartbeat por medio de mensajes UDP entre los nodos balanceados y un servicio que se encarga de monitorear que ciertos procesos esten corriendo.

Configuracion de Piranha en el servidor primario

##### /etc/sysconfig/ha/lvs.cf

```java>
Configuracion de Piranha en el servidor secundario

== /etc/sysconfig/ha/lvs.cf ==
 

<code java>
serial_no = 55
primary = 10.1.2.103
service = lvs
backup_active = 1 
backup = 10.1.2.105
heartbeat = 1
heartbeat_port = 539
keepalive = 4
deadtime = 18
network = direct
debug_level = NONE
monitor_links = 0
virtual proxy {
     active = 1
     address = 10.1.2.106 eth0:0
     vip_nmask = 255.255.255.0
     port = 8080
     pmask = 255.255.255.0
     send = "GET cache_object://localhost/info"
     use_regex = 0
     load_monitor = none
     scheduler = wlc
     protocol = tcp
     timeout = 2
     quiesce_server = 0 
     server proxy1 {
         address = 10.1.2.103
         active = 1
         weight = 1
     }
     server proxy2 {
         address = 10.1.2.105
         active = 1
         weight = 1
     }
}
```

Se agrego la configuracion para la interface virtual lo:0 /etc/sysconfig/network-scripts/ifcfg-lo:0

```
DEVICE=lo:0
IPADDR=10.1.2.106
NETMASK=255.255.255.255
#NETWORK=127.0.0.0
# If you're having problems with gated making 127.0.0.0/8 a martian,
# you can change this to something else (255.255.255.255, for example)
#BROADCAST=127.255.255.255
ONBOOT=yes
NAME=lvsIP
```
