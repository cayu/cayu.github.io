---
title: "Correlación de eventos"
date: 2017-07-21T13:06:48-03:00
---

Si bien Nagios provee de monitoreo en vivo para saber el estado de nuestros servicios, a veces necesitamos un proceso para analizar los datos de eventos e identificar patrones, que nos ayudaran a entender causas comunes y causas iniciales, por medios de reglas para estados predefinidos.

Para poder determinar estos casos deberemos implementar una Bitácora centralizada de eventos del sistema (SYSLOG).

### Sistema de loggueo centralizado

Se tiene la necesidad de un sistema que reciba y almacenes archivos de registros determinados de n\* servidores. Que se disponga de una interfaz de acceso a esos datos, para la organizacion y procesamiento de los mismos, con lo cual se podran detectar eventos, resolver situaciones y emitir distintos tipos de alarmas.

**Syslog** es un estándar de facto para el envío de mensajes de registro en una red informática IP. Por syslog se conoce tanto al protocolo de red como a la aplicación o biblioteca que envía los mensajes de registro.

Un mensaje de registro suele tener información sobre la seguridad del sistema, aunque puede contener cualquier información. Junto con cada mensaje se incluye la fecha y hora del envío.

Es útil registrar, por ejemplo:

* Un intento de acceso con contraseña equivocada
* Un acceso correcto al sistema
* Anomalías: variaciones en el funcionamiento normal del sistema
* Alertas cuando ocurre alguna condición especial
* Información sobre las actividades del sistema operativo
* Errores del hardware o el software

También es posible registrar el funcionamiento normal de los programas; por ejemplo, guardar cada acceso que se hace a un servidor web, aunque esto suele estar separado del resto de alertas.

El protocolo syslog es muy sencillo: existe un ordenador servidor ejecutando el servidor de syslog, conocido como syslogd (demonio de syslog). El cliente envía un pequeño mensaje de texto (de menos de 1024 bytes).

Los mensajes de syslog se suelen enviar vía UDP, por el puerto 514, en formato de texto plano. Algunas implementaciones del servidor, como syslog-ng, permiten usar TCP en vez de UDP, y también ofrecen Stunnel para que los datos viajen cifrados mediante SSL/TLS.

#### Generalidades de un sistema de log.

*Log es el registro de acciones y eventos que tienen lugar en un sistema.*

*Los logs son el primer registro de la actividad en los sistemas y redes.*

*Los logs de un sistema son una parte primaria de la seguridad y pueden ser usados en la detección de ataques e intrusiones, así como en el análisis de fallas de hardware y software.*

El programa syslog, es una interface que provee un framework standard para que tanto programas como el mismo sistema operativo puedan generar mensajes que peuden ser almacenados localmente o ser enviados a un host remoto. Originalmente escrito para Unix, se convirtió en un standard que se usa en muchos sistemas operativos y dispositivos de red.

**¿ Cual es la utilidad de un sistema de syslog centralizado ?**

En un sistema de syslog centralizado, un server común recibe todos los mensajes de syslog de todos los sistemas de la red. Esto incluye logs de los servers Unix/linux/Windows etc, firewalls, y dispositivos de red (routers, switches, etc)

Hay varias ventajas de un sistema de syslog centralizado

- El syslog puede ser conectado en un segmento de red diferente protegido por un firewall

para mantener más segura la información

- Teniendo los mensajes de todos los equipos, puede hacerse una correlación de ataques o

fallas en distintos puntos de una manera mucho más sencilla. Por ejemplo si en el syslog aparece un mensaje de desconexión de la interface de red de varios servers en el mismo momento, es lógico suponer una falla en el switch donde estos servers estan conectados.

- Un usuario no deseado que haya ingresado en un server, no podrá alterar los mensajes que

se hayan almacenado en el server central.

- Se pueden generar alertas usando sistemas de monitoreo de logs.

##### Sistema de monitoreo de logs

El análisis de logs es una herramienta muy importante para mantener el control de servers y dispositivos de red.

Sin embargo esta es una de las tareas que más tiempo consume y por consiguiente que menos se hace.

Con la cantidad de mensajes informativos que se generan en un sistema de log, detectar en forma manual los mensajes de problemas es muy dificultoso y con mucha probabilidad de error. Esto se vé aumentado cuando se usa un sistema de syslog centralizado, donde la información proviene de varias fuentes distintas. Muchas soluciones de monitoreo se basan en sumarizar la información de archivos de log de dias previos.

Esto es muy útil para la generación de estadísticas y análisis posterior a una falla o intrusión, pero no tanto para la resolución de problemas.

Un administrador no puede actuar en forma proactiva, previamente a que el error ocurra.

Muchas fallas o accesos no autorizados se ven precedidos por mensajes que de haber sido detectados, podrían haber permitido tomar acciones preventivas.

Por ejemplo, un acceso no autorizado via ssh, puede haber estado precedido por una gran cantidad de intentos fallidos de acceso.

Disponer una solución online de monitoreo, permite disponer de herramientas que pueden ayudar a prevenir problemas graves antes que ocurran.

Detectar eventos en el momento que ocurren permite generar acciones en ese mismo instante y no luego de las consecuencias.

Siguiendo con el ejemplo del acceso ssh, podría bloquearse el acceso ssh desde determinada dirección IP despues de un número de intentos fallidos de acceso.

Un concepto que aparece aquí es el de correlación de eventos.

Un sistema automatizado de análisis de logs que pueda hacer una correlación de varios eventos simplifica y acelera el monitoreo de eventos consolidando alertas y mensajes de error en un mensaje más corto y fácil de entender.

Una serie de operaciones están relacionadas con la correlación de eventos.

Compresión toma varias apariciones del mismo evento y se examina la duplicación de información, se remueve las redundacias y se reporta como un único evento. De esta manera 1000 mensajes “route failed” se convierte en un único alerta que dice “route failed 1000 times” Recuento reporta un número específico de eventos similares como uno solo. Esto se diferencia de la compresión en que no solo cuenta en que sea el mismo evento, sino que se supere un determinado umbral para generar un reporte.

Supresión asocia prioridades con alarmas y permite que el sistema suprima un alarma de prioridad más baja si ha ocurrido un evento de prioridad mayor.

Generalización asocia alarmas con eventos de más alto nivel que son los que son reportados.

Esto puede ser útil para por ejemplo para correlacionar eventos de múltiples discos de un array.

No se necesita ver cada mensaje específico si se puede determinar que el array completo tiene problemas.

Correlación basada en tiempo puede ser útil estableciendo causalidad. A menudo una información puede ser obtenida relacionando eventos que tienen una relación temporal específica.

**Ejemplos genéricos:**

- El Evento A está seguido del Evento B.

<!-- -->

- Este es el primer Evento A desde el Evento B reciente.

<!-- -->

- El Evento A sigue al Evento B dentro de los dos minutos.

<!-- -->

- El Evento A no fue observado dentro del Intervalo I.

### Objetivos

Simplificar y optimizar la administración de diferentes servicios para conocer su estado minuto a minuto, y elaborar planes de acción. A su vez el sistema debe ser simple de utilizar por el administrador, y ser posible de ver via web los registros del sistema, realizar busquedas etc.

Es útil registrar, por ejemplo:

* Un intento de acceso con contraseña equivocada
* Un acceso correcto al sistema
* Anomalías: variaciones en el funcionamiento normal del sistema
* Alertas cuando ocurre alguna condición especial
* Información sobre las actividades del sistema operativo
* Errores del hardware o el software

También es posible registrar el funcionamiento normal de los programas; por ejemplo, guardar cada acceso que se hace a un servidor web, aunque esto suele estar separado del resto de alertas.

Puede logguear tanto por UDP como por TCP, teniendo compatibilidad con el viejo syslog, soportando a su vez muchas mas opciones y tareas que el syslog comun.

### Sistemas de Syslog Centralizado

- [Syslog-NG](/manuales/nagios/capacitacion/correlacion_de_eventos/syslog_ng/)
- [EventDB](/manuales/nagios/capacitacion/correlacion_de_eventos/eventdb/)
- [Rsyslog - Reliable system and kernel logging daemon](/manuales/nagios/capacitacion/correlacion_de_eventos/rsyslog/)
