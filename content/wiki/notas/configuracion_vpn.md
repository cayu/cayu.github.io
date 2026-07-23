---
title: "Configuración de acceso VPN desde Linux"
date: 2017-08-25T11:07:51-03:00
---

## PPTP

Ejemplos de configuración PPTP para el usuario cayu en el dominio CAYU

**/etc/ppp/peers/MIREDVPN**

```
pty "pptp vpn.cayu.com.ar --nolaunchpppd"
lock
noauth
refuse-pap
refuse-eap
refuse-chap
nobsdcomp
nodeflate
name cayu
remotename CAYU
ipparam CAYU
require-mppe-128
```

**/etc/ppp/chap-secrets**

```
scayuqueo CAYU "MicontraseñaDificil" *
```

Luego para conectarnos ejecutamos :

**\# pon CAYU**

**\# ifconfig**

```
lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:410873 errors:0 dropped:0 overruns:0 frame:0
          TX packets:410873 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:139300917 (132.8 MiB)  TX bytes:139300917 (132.8 MiB)

ppp0      Link encap:Point-to-Point Protocol  
          inet addr:10.1.91.21  P-t-P:10.15.71.11  Mask:255.255.255.255
          UP POINTOPOINT RUNNING NOARP MULTICAST  MTU:1396  Metric:1
          RX packets:11 errors:0 dropped:0 overruns:0 frame:0
          TX packets:8 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:3 
          RX bytes:314 (314.0 B)  TX bytes:80 (80.0 B)
```

Y finalmente creamos las reglas de ruteo de trafico

**\# route add -net 10.1.11.0/24 gw 10.15.91.21**

O tambien :

**\# route add -net 10.1.11.0 netmask 255.255.255.0 gw 10.15.91.21**

### Configuración de acceso VPN desde Linux con el Celular como punto de acceso

<img src="/notas/celular-como-modem.png" class="align-right" />

Para proceder a la Conexión de acceso VPN utilizando el móvil como método de conexion a Internet.

Si lo utilizamos como anclaje usb, o sea que nos levanta una interfaz de red usb0, deberemo modificar el MTU de esa interfaz, por default es 1500 y deberemos bajarlo a 1200

```
ifconfig usb0 mtu 1200 up
```

En **/etc/ppp/options** deberemos comentar esta línea :

```
#proxyarp
```

En **/etc/ppp/options.pptp** deberemos comentar estas líneas :

```
#nobsdcomp
#nodeflate
```

En **/etc/ppp/options.pptp** deberemos descomentar estas líneas :

```
require-mppe-128
mppe required,stateless
```

### IPTABLES

```
# Accept all packets via ppp* interfaces (for example, ppp0)
iptables -A INPUT -i ppp+ -j ACCEPT
iptables -A OUTPUT -o ppp+ -j ACCEPT

# Accept incoming connections to port 1723 (PPTP)
iptables -A INPUT -p tcp --dport 1723 -j ACCEPT

# Accept GRE packets
iptables -A INPUT -p 47 -j ACCEPT
iptables -A OUTPUT -p 47 -j ACCEPT
```
