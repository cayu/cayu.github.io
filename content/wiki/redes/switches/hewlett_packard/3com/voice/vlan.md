---
title: "VLAN de voz"
date: 2016-08-17T12:15:40-03:00
---

Mediante la familia de direcciones MAC, podemos asignarle una VLAN a los equipos aunque la boca a la que esté conectada pertenezca a otra VLAN. De esta manera podremos conectar un teléfono IP y otro equipo en la misma boca del switch, y que cada uno siga perteneciendo a la VLAN que corresponda.En este caso se presenta con los telefonos Cisco, que son menos flexibles a la hora de configurarlos.

En este caso la VLAN de voz es la 7.

```
voice vlan mac-address 0013-1900-0000 mask ffff-ff00-0000 description Cisco 7960 phone
voice vlan mac-address 0015-2b00-0000 mask ffff-ff00-0000 description Cisco 7940 phone
voice vlan mac-address 001f-6c00-0000 mask ffff-ff00-0000 description Cisco 7941 phone
voice vlan mac-address 5c50-1500-0000 mask ffff-ff00-0000 description Cisco 7941 phone
voice vlan mac-address c062-6b00-0000 mask ffff-ff00-0000 description Cisco 7941 phone
voice vlan 7 enable
```
