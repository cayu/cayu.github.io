---
title: "Instalar Placa TDM400P"
date: 2009-07-04T01:37:43-03:00
---

![tdm400p.jpg](/notas/tdm400p.jpg)

Documentacion sobre como realizar la instalacion de la placa analogica TDM400P con 4 puertos FX0

## Tareas

Instalar la placa PCI Instalar paquetes necesarios para compilacion

```
apt-get install linux-headers-2.6.18-6-686 build-essential
```

Descargar y compilar los modulos del kernel de <http://downloads.digium.com/pub/zaptel/>

```
# tar zxvf zaptel-1.4-current.tar.gz
# make
# make install
```

Con esto tenemos instalados diferentes modulos de kernel

##### /etc/modules

```
zaptel
wcfxo
wctdm
```

Y diferentes utilidades

```
ztcfg ztscan ztmonitor ztspeed zttest
```

Dentro del archivo

##### /etc/zaptel.conf

Debemos configurar la siguiente entrada extra

```
fxsks=1-4
```

Lo siguiente es ejecutar ztcfg para confirmar que nuestra configuración esta correcta. Si nos hemos equivocado con la señalización de cada puerto tendremos un mensaje de error que nos lo indicará para poder corregirlo.

```
ztcfg -vvvv

Zaptel Version: 1.4.11
Echo Canceller: MG2
Configuration
======================

Channel map:

Channel 01: FXS Kewlstart (Default) (Slaves: 01)
Channel 02: FXS Kewlstart (Default) (Slaves: 02)
Channel 03: FXS Kewlstart (Default) (Slaves: 03)
Channel 04: FXS Kewlstart (Default) (Slaves: 04)

4 channels to configure.
```

En el caso de que nos diga 4 channels to configure deberemos correr la utilidad *genzaptelconf –s –d -v* para que nos genere un archivo /etc/zaptel.conf y un archivo /etc/asterisk/zapata-channels.conf a incluirse en /etc/asterisk/zapata.conf.

## Menu

A la entrada en la PBX, nos da un tono de respuesta para poder introducir el interno SIP al cual queremos llamar.

```
[from-pstn]
exten => s,1,Answer
exten => s,2,Read(INTERNO,agent-user)
exten => s,3,Playback(auth-thankyou)
exten => s,4,Dial(SIP/${INTERNO})
exten => s,5,Hangup
```

# Esto que hace ?

- \[from-pstn\]
  -  Nombre del Contexto
- exten =\> s,1,Answer
  -  Contesta la llamada
- exten =\> s,2,Read(INTERNO,agent-user)
  -  Lee la variable capturada marcando el telefono y nos lo indica por medio de una voz
- exten =\> s,3,Playback(auth-thankyou)
  -  Reproduce el sonido “Gracias”
- exten =\> s,4,Dial(SIP/\${INTERNO})
  -  Marca el Interno con los datos obtenidos en la variable
- exten =\> s,5,Hangup
  -  Corta la llamada

En **/var/lib/asterisk/sounds** encontramos los sonidos que podemos reproducir y los idiomas disponibles
