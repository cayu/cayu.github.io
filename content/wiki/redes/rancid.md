---
title: "Rancid (Really Awesome New Cisco confIg Differ)"
date: 2018-05-10T16:25:03-03:00
---

Podemos utilizar Rancid para hacer backup de las configuraciones de nuestros routers, switches o firewalls

| Dispositivo | Descripción |
|----|----|
| alteon | Alteon WebOS switches. |
| baynet | Bay Networks router. |
| cat5 | Cisco catalyst series 5000 and 4000 switches (i.e.: running the catalyst OS, not IOS). |
| cisco | Cisco router, PIX, or switch such as the 3500XL or 6000 running IOS (or IOS-like) OS. |
| css | Cisco content services switch. |
| enterasys | An enterasys NAS. This is currently an alias for the riverstone device type. |
| erx | A Juniper E-series edge router. |
| Extreme | An Extreme switch. |
| ezt3 | An ADC-Kentrox EZ-T3 mux. |
| force10 | A Force10 router. |
| fortigate | Equipos Fortigate |
| foundry | A Foundry router, switch, or router-switch. This includes HP Procurve switches that are OEMs of Foundry products, such as the HP9304M. |
| hitachi | A Hitachi routers. |
| h3c | 3Com SuperStack 4, H3C, HP Networking (‘A’ & some ‘E’ portfolio, post 2010 3Com acquitision), some Huawei equipment. |
| hp | A HP Procurve switch such as the 2524 or 4108 procurve switches. Also see the foundry type. |
| mrtd | A host running the (merit) MRTd daemon. |
| netscalar | A Netscalar load balancer. |
| netscreen | A Netscreen firewall. |
| redback | A Redback router, NAS, etc. |
| tnt | A lucent TNT. |
| zebra | Zebra routing software. |
| riverstone | A Riverstone NAS or Cabletron (starting with version ~9.0.3) router. |
| juniper | A Juniper router. |

## Implementación

### Intalación

Para instalar Rancid en Debian 8, basta con instalar el paquete rancid

```
apt-get install rancid
```

## Referencias y notas

Particularmente utilizo Rancid para realizar Backup de las configuraciones de :

- Routers / Switches / Firewalls
  - Cisco
  - 3Com y H3C
  - Fortinet
  - Aruba

Donde conseguir mas información :

- Ritio oficial : <http://www.shrubbery.net/rancid/>
- Agregado para soporte H3C : <https://sites.google.com/site/jrbinks/code/rancid/h3c>
