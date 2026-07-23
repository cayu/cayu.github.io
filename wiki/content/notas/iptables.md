---
title: "TIPs varios de IPTables"
date: 2015-04-20T17:50:16-03:00
---

## Syslog

Restringir el tráfico syslog en el servidor central

- Sólo permitir que sus equipos envíen logs

```bash>
===== Servidor Web =====
Tengo ips que me molesta y no tengo tiempo/ganas de instalar fail2ban, pongo DROP, si ponemos REJECT el otro va a obtener una respuesta mas intuitiva y se va a dar cuenta de que lo estamos filtrando, con DROP es un poco mas discreto (si el del otro lado no tiene muchos conocimientos, pero si los tiene de alguna manera se va a dar cuenta)

**/etc/rc.local/**

<code bash>
/etc/rc.local 
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.
iptables -A INPUT -s 195.189.140.23  	-j DROP
iptables -A INPUT -s 41.223.248.9 	-j DROP
iptables -A INPUT -s 89.149.195.31 	-j DROP 
iptables -A INPUT -s 66.216.122.81 	-j DROP 
iptables -A INPUT -s 84.38.28.10 	-j DROP 
iptables -A INPUT -s 190.200.47.217 	-j DROP 
iptables -A INPUT -s 202.164.175.186 	-j DROP
iptables -A INPUT -s 219.142.3.250 	-j DROP 
iptables -A INPUT -s 208.64.253.246 	-j DROP
iptables -A INPUT -s 190.200.47.217 	-j DROP
iptables -A INPUT -s 200.46.218.163 	-j DROP
iptables -A INPUT -s mail.pnuma.org  	-j DROP
iptables -A INPUT -s 58.181.37.130	-j DROP
iptables -A INPUT -s 218.77.97.163	-j DROP
iptables -A INPUT -s 195.154.70.138	-j DROP
iptables -A INPUT -s 218.19.140.21	-j DROP
iptables -A INPUT -s 65.75.189.67 	-j DROP
iptables -A INPUT -s 122.224.131.107 	-j DROP
iptables -A INPUT -s 211.147.1.216 	-j DROP
iptables -A INPUT -s 218.148.242.187 	-j DROP
iptables -A INPUT -s 78.129.227.129 	-j DROP
iptables -A INPUT -s 212.99.230.70 	-j DROP
iptables -A INPUT -s 208.66.56.37 	-j DROP
iptables -A INPUT -s 72.249.77.26 	-j DROP
iptables -A INPUT -s 221.142.84.227 	-j DROP
iptables -A INPUT -s 70.86.175.4 	-j DROP
iptables -A INPUT -s 87.106.217.65 	-j DROP
iptables -A INPUT -s 194.187.130.33 	-j DROP
iptables -A INPUT -s 82.18.121.25 	-j DROP
iptables -A INPUT -s 200.26.153.204 	-j DROP
iptables -A INPUT -s 218.208.56.4 	-j DROP
iptables -A INPUT -s 210.124.36.46 	-j DROP
iptables -A INPUT -s 124.164.234.7 	-j DROP
iptables -A INPUT -s 218.67.79.60 	-j DROP
iptables -A INPUT -s 71.166.159.177 	-j DROP
iptables -A INPUT -s 218.28.143.246 	-j DROP
iptables -A INPUT -s 58.211.139.57 	-j DROP
iptables -A INPUT -s 218.67.79.60 	-j DROP
iptables -A INPUT -s 211.235.32.124 	-j DROP
iptables -A INPUT -s 77.221.130.4 	-j DROP
iptables -A INPUT -s 118.129.100.136 	-j DROP
iptables -A INPUT -s 195.94.90.25 	-j DROP
iptables -A INPUT -s 219.144.46.146	-j DROP
iptables -A INPUT -s 59.120.88.243	-j DROP
iptables -A INPUT -s 110.172.24.28	-j DROP
iptables -A INPUT -s 203.177.51.186	-j DROP
iptables -A INPUT -s 218.15.163.222	-j DROP
iptables -A INPUT -s 194.54.88.38	-j DROP
iptables -A INPUT -s 78.137.9.153	-j DROP
iptables -A INPUT -s 60.49.0.221	-j DROP
iptables -A INPUT -s 78.137.9.153	-j DROP
iptables -A INPUT -s 69.59.21.119	-j DROP
iptables -A INPUT -s 72.55.156.70	-j DROP
iptables -A INPUT -s 211.169.249.251	-j DROP
iptables -A INPUT -s 91.182.229.181	-j DROP
iptables -A INPUT -s 78.137.19.98	-j DROP
iptables -A INPUT -s 78.137.0.0		-j DROP
iptables -A INPUT -s 89.189.179.214	-j DROP

exit 0
```

## Redirigir tráfico

Ejemplo, tenemos una aplicación propietaria con cierta IP harcodeada y que no podemos modifica, si ese servidor cambio de IP y no tenemos forma de modificar la configuración de la aplicación, podremos redirigir el tráfico y será transparente para la aplicación.

La IP original era 192.168.1.199 y la nueva IP es 192.168.2.199

```
iptables -t nat -D OUTPUT -d 192.168.1.199 -j DNAT --to 192.168.2.199
```
