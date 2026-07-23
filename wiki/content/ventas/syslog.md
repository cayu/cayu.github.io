---
title: "Centralización de logs con syslog-ng"
date: 2015-01-07T20:48:55-03:00
---

## Syslog-ng

Syslog-NG es un sistema para el envío de mensajes de registro en una red. Este sistema es generalmente usado para detectar:

- Intentos de acceso con contraseña equivocada
- Un acceso correcto al sistema
- Anomalías: variaciones en el funcionamiento normal del sistema
- Alertas cuando ocurre alguna condición especial
- Información sobre las actividades del sistema operativo
- Errores del hardware o el software

También es posible registrar el funcionamiento normal de los programas; por ejemplo, guardar cada acceso que se hace a un servidor web, aunque esto suele estar separado del resto de alertas.

Puede logguear tanto por UDP como por TCP, teniendo compatibilidad con el viejo syslog, soportando a su vez muchas mas opciones y tareas que el syslog comun.

### Configuración

La configuración de este sistema requiere de la definición de:

- Fuentes de datos (de dónde se obtendrán)
- Destino de los datos (dado que es un sistema centralizador de logs, en los syslog-ng cliente se define a qué servidor/es se enviará, en el caso del syslog-ng servidor se define en qué archivos locales se guardarán los datos recibidos y propios)
- Filtros para separar la información (cuando un sistema centralizador de logs se encuentra en funcionamiento se tiene que configurar detalladamente como se organizará los datos recibidos, de esta forma se podrán ver estos registros incluso en forma manual si es necesario)
- Finalmente se combinan las tres configuraciones (fuentes de los datos, destino y filtros) para generar las reglas que le informarán al daemon qué tiene que hacer (qué archivar y qué no)

## Rotación de archivos de log

Una vez realizada la configuración del sistema syslog-ng también se adaptará la forma en que se rotan los archivos para evitar acumulaciones innecesarias de datos.

La rotación de logs será administrada por el producto (común en la mayoría de las distribuciones Linux) logrotate.

## Kiwi Syslog Daemon - Snare

Este producto no es un producto libre sino gratis (freeware), por lo que se podrá trabajar con él adaptando las necesidades de la compañía a las limitantes y/o condiciones del software (y no viceversa).

Dentro de este proyecto se lo instala para poder enviar registros de log desde sistemas operativos Windows de aplicaciones o dispositivos que soporte el protocolo. Es importante tener en cuenta que este protocolo no es implementado por Windows, por lo que mucha de la información del sistema operativo no es logueada.
