---
title: "Fortinet"
date: 2020-03-10T15:06:27-03:00
---

<img src="/redes/firewalls/fortinet_img.png" class="align-right" />

### Configuración Minicom

```ini>
==== Acceder sin password de admin ====
Prendemos el equipo y vamos a tener 30 segundos para logearnos por la terminal de consola. Luego de esos 30 segundos la información de login va a ser inválida.

El usuario es: **maintainer**

La contraseña es bcpb + número de serie.

Ejemplo: **bcpbFGT60C3G10016011**
==== Comandos ====
=== Equivalencias con CISCO ===
  * **show**
    * Show global or vdom config
  * **sh system interface**
    * Equivalent to show run interface
  * **diagnose hardware deviceinfo nic**
    * Equivalent to show interface
  * **get system status**
    * show version information
  * **sh firewall policy 6**
    * show firewall rule numer 6
  * **sh router policy**
    * Show Policy Routing rules
  * **diagnose system session list**
    * Show the excisting translations
  * **diagnose system session clear**
    * Clears all xlate/translations
  * **diagnose ip arp list**
    * Shows the arp table of connected hosts
  * **get router info routing-table all**
    * Equivalent to ‘show ip route’
  * **diagnose system top**
    * Show System Processes running with PIDs
  * **diagnose system kill 9 <id>**
    * Kill the specific PID
**Referencia :** https://routing-bits.com/2008/10/09/fortigate-commands/
=== Forti OS ===
== Reiniciar valores de fabrica ==
<code>
exec factoryreset
```

##### Ver configuración de interfaces

```
show system interface ?
```

##### Sniff de paquetes SNMP

Útil para verificar si el ruteo funciona correctamente

```
diag sniffer packet  any icmp
```

##### Ver tablas de ruteo

```
get router info routing-table details
```

##### Diánosticar VPN

```
get vpn ike gateway
```

```
get vpn ipsec tunnel details
```

```
diagnose vpn tunnel list
```

```
diagnose vpn ipsec status
```

## UTM

## WLC - Wireless Controller

Los controladores WiFi sirven para centralizar y simplificar la administración de puntos de acceso y a su vez mejorar la seguridad y experiencia de usuario al ir cambiando entre equipos. Fortinet incluye su solución WLC para administrar equipos FortiAP.

### Rutas

Ejemplo de como dar de alta rutas estáticas :

```bash>
==== Radius ====
Por ejemplo si tenemos una configuración de conexión VPN y nuestro Fortinet tiene problemas de ruteo interno y no llega hasta el Radius, pero si llegan los equipos detras del Firewall, podemos configurar que hacia el radius los mensajes salgan por la ip de la red local especificada en la phase1, ej si es 10.1.1.1
<code bash>
config user radius
    edit "AR-RADIUS"
        set server "10.1.1.80"
        set secret ENC zvVNNcRCNnFWl8wg==
        set auth-type ms_chap
        set source-ip "10.1.1.1"
    next
end
```

## VPN

**forticlientsslvpn_cli connect –server {ip}:{port} –vpnuser {user} –keepalive**

## SD-WAN

**SD-WAN** significa red de área amplia (o networking) definida por software. Una WAN es una conexión entre redes de área local (LAN) separadas por una distancia considerable, desde unos pocos hasta miles de kilómetros. El término definido por software implica que la WAN está configurada y administrada programáticamente. Por lo tanto puede ser fácil y rapidamente adaptada a las necesidades cambiantes de la empresa.

### Problemas

#### Recomendaciones personales

No hacer logging al SSD, ya que se vencen los ciclos de vida del almacenamiento flash :

```
config log gui-display
set location memory
end
```

#### BUGS

##### 467758

| FortiOS afectado | Corregido | BUG ID | Descripción |
|----|----|----|----|
| 5.6.3 | 5.6.4 | 467758 | Not able to pass data traffic when DTLS policy is set to clear text. |

Descripción del problema : *Cuando configuramos una red en modo túnel o en modo bridge con alguna opción especial como autenticación por Radius y la conexión al WLC es en formato clear text, podemos poder tener problemas aleatorios de desconexiones o intermitencias.*

Paliativo : Configurar el **AP Data Channel Security** con la opción DTLS Enabled. Dentro WLC en el perfil utilizado por el FortiAP deberemos agregar la siguiente opción :

```
set dtls-policy dtls-enabled
```
