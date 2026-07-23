---
title: "Red restringida"
date: 2011-07-08T11:10:58-03:00
---

A menudo en nuestra empresa necesitamos que clientes esporadicos se conecten temporalmente, pero no queremos que accedan a nuestra red corporativa, y para ello elaboramos lo que podriamos decir una red de visitas.

*Para ello podemos pensar un esquema simple en donde por ejemplo por medio de un equipo wireless damos señal y un equipo Linux se encarga de dar un DHCP propio y hacer de firewall/ruteador hacia nuestra red corporativa*

```
              ---------------------------
              |                         |    
              |      Linux Server       |
              |                         |    
              ---------------------------
                         |!!|
                       |      |
                       |  AP  |
                       |      |
                         ----

 |==========T=============T===========T==========T==========| Señal Wi Fi
            |             |           |          |
            |             |           |          |
          -----         -----       -----      -----
          |   |         |   |       |   |      |   |
          -----         -----       -----      -----
```

Ademas de todo queremos que tengan acceso a internet pero con un proxy propio para separarlo del proxy principal de la empresa, para ellos implementamos un proxy transparente con la ayuda de Squid, Frox, u2nl eh Iptables.

Software utilizado

|  |  |
|----|----|
| Frox a transparent ftp proxy | <http://frox.sourceforge.net/> |
| Squid Proxy Server | <http://www.squid-cache.org> |
| u2nl SSL redirector | <http://www.reitwiessner.de/programs/u2nl.html> |
| Iptables | <http://www.netfilter.org/projects/iptables/> |

```
iptables -t nat -A OUTPUT -p tcp -d ! <proxy host> -j REDIRECT --to-port <listen port>
```
