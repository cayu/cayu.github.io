---
title: "Conectividad con Asterisk"
date: 2016-11-16T12:10:55-03:00
---

## Objetivos

Tener la comunicacion repartida en diferentes servidores Asterisk por cada planta, tomando como internos los equipos convencionales de telefonia y como adicional utilizar los telefonos moviles de cada empleado para asi poder localizarlo este donde este por medio de su numero de interno.

## Tareas

Implementacion de un servidor de pruebas para el Asterisk, se instalo y configuro un Debian GNU/Linux Etch dentro de un equipo Pentium III 450 Mhz con 128 MB de memoria RAM. Se instalo el paquete basico de Asterisk.

```
apt-get install asterisk
```

En los clientes windows/linux se instalo un cliente SIP, el Ekiga, disponible para ambos Sistemas Operativos. Con lo cual se probo la comunicacion por medio de SoftPhones.

Luego por medio de un dispositivo ATA de comunicacion para VoIP, un LinkSys PAP2T-NA, se realizo la configuracion para su integracion con el servidor Asterisk, se probo por medio de un telefono analogico comun realizar llamadas a una computadora conectada al Asterisk.

# Configuracion

Para iniciar o detener el servicio asterisk, ejecutar el siguiente comando con los parametros start o stop

```
asterisk:/# /etc/init.d/asterisk
```

Dentro del archivo sip.conf se encuentra la configuracion de usuarios SIP con sus contraseñas y contexto al que corresponden

```
asterisk:/etc/asterisk# cat sip.conf
[general]
context=default
srvlookup=yes
;videosupport=yes

disallow=all   ; First disallow all codecs
allow=ulaw
allow=alaw     ; Allow codecs in order of
allow=ilbc     ; preference
allow=gsm
allow=h261
allow=g723
allow=g726
allow=g729


[sergio]
type=friend
secret=cayucontraseña
qualify=yes
nat=no
host=dynamic
canreinvite=no
context=home
mailbox=cayu@cayu.com.ar

asterisk:/etc/asterisk#
```

Dentro de extensions.conf, se encuentra la configuracion de “internos”, o sea si marco 107 me conecto al usuario sergio.

```
asterisk:/etc/asterisk# cat extensions.conf

[home]
exten => 101,1,Dial(SIP/101)
exten => 107,1,Dial(SIP/sergio)
```
