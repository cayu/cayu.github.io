---
title: "EventDB"
date: 2017-07-21T13:05:46-03:00
---

### EventDB

#### ¿Que es Eventdb?

EventDB es una herramienta para facilitar el tratamiento de los datos basados ​​en eventos de Syslog para herramientas de monitoreo como Nagios o Icinga y su integración con los mismos se logra a través de un plug-in de chequeo. Cuenta con varios plugins para diferentes fuentes de datos. La interfaz web permite a los usuarios buscar, filtrar y reconocer todos los eventos.

#### ¿Cómo funciona?

El EventDB toma los eventos recibidos y los almacena en una base de datos MySQL. Se tomando los datos de syslog-ng desde un pequeño demonio perl (syslog-ng2mysql.pl). syslog-ng2mysql.pl abre una unix-pipe por un lado desde donde recibe los mensajes de syslog-ng, y utiliza DBI en el otro para escribir datos en MySQL.

![nagios_eventdb.jpg](/manuales/nagios/nagios_eventdb.jpg)
