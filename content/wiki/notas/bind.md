---
title: "Configuración de Servidor DNS Bind"
date: 2018-11-22T15:33:59-03:00
---

BIND (Berkeley Internet Name Domain, anteriormente : Berkeley Internet Name Daemon) es el servidor de DNS más comúnmente usado en Internet. Es patrocinado por la Internet Systems Consortium. Una nueva versión de BIND (BIND 9) fue escrita desde cero en parte para superar las dificultades arquitectónicas presentes anteriormente para auditar el código en las primeras versiones de BIND, y también para incorporar DNSSEC (DNS Security Extensions). BIND 9 incluye entre otras características importantes: TSIG, notificación DNS, nsupdate, IPv6, rndc flush, vistas, procesamiento en paralelo, y una arquitectura mejorada en cuanto a portabilidad. Es comúnmente usado en sistemas GNU/Linux.

## Instalación

aptitude install bind9 dnsutils

## Probarlo

1\. Editar siguiente archivo:

    sudo cp /etc/resolv.conf /etc/resolv.conf.bak

    sudo vim /etc/resolv.conf

Con el siguiente contenido:

    search example.com
    nameserver 192.168.1.16

La ip 192.168.1.16 es la de la maquina donde esta corriendo bind

3\. (Opcional) Instalar paquetes:

    sudo aptitude update; sudo aptitude install dnsutils

4\. Probar:

    nslookup www.example.com

Deberia devolver algo parecido a:

```
nslookup www.example.com
Server:		192.168.1.16
Address:	192.168.1.16#53

Name:	www.example.com
Address: 192.168.1.14
```

Gracias al forward, por supuesto, tambien resuelve direcciones publicas:

    nslookup www.gmail.com

Y deberia devolver:

```
Server:		192.168.1.16
Address:	192.168.1.16#53

Non-authoritative answer:
www.gmail.com	canonical name = mail.google.com.
mail.google.com	canonical name = googlemail.l.google.com.
Name:	googlemail.l.google.com
```

## Recargar configuración sin reiniciar

Tras editar los archivos de configuración:

    sudo rndc reload

## Bind TIP’s

Tips útiles sobre BIND

### Exportar configuración de dominio en otro DNS

```
dig @xxx.zoneedit.com your.domain.com axfr >your.domain.com.txt
```

### Include de hosts

Include de archivo con información de hosts dentro de un archivo de zona

Ejemplo si queremos sobreescribir un nombre que tenemos en la configuración y no podemos cambiar la configuración general

Dentro de nuestro archivo de zona

**cayu.com.ar.dns**

```
$INCLUDE /etc/named-proxyconf.conf
wpad.cayu.com.ar.            IN      A      10.70.2.1
proxyconf.cayu.com.ar.       IN      A      10.70.2.1
```

**named-proxyconf.conf**

```
wpad.cayu.com.ar.            IN      A      10.1.2.1
proxyconf.cayu.com.ar.       IN      A      10.1.2.1
```

En db.local.zone podemos tener

```
;
; BIND data file for local loopback interface
;
$TTL	604800
@	IN	SOA	localhost. root.localhost. (
			      1		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			    600 )	; Negative Cache TTL
;
@	IN	NS	localhost.
@	IN	A	127.0.0.1
wpad.cayu.com.ar.            IN      A      10.1.2.1
proxyconf.cayu.com.ar.       IN      A      10.1.2.1
```

y al final terminara respondiendo a las ip 10.1.2.1

### Actualizar named.ca

```
dig @a.root-servers.net . ns > /var/named/named.ca
```

### Fordwar para una zona específica

```
zone "foo.example" {
    type master;
    file "db.foo.example";
    forwarders {};
};
```

### Banner de versión

Modificar headers de respuesta, útiles a la hora de un **nmap -sV**

```
options {
	version none;
	hostname none;
	server-id none;
};
```

### Ver cache actual

```
# rndc dumpdb
# less /var/cache/bind/named_dump.db
# grep 'yahoo.com' /var/cache/named/named_dump.db
```
