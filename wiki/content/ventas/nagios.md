---
title: "Monitoria y análisis de Red con Nagios"
date: 2015-03-30T17:19:12-03:00
---

## Necesidades

### Que se busca

- Detección de caídas del sistemas
  - Disponibilidad del sistema
- Evaluación de cumplimiento de SLA

<!-- -->

- Capacity planing
- Analisis de performance
  - Detección de errores de diseño y de programación

Acciones indirectas del monitoreo =\> Mejora o Confeccion de una CMDB

#### Planificación y revisión

- Sofware
  - De base
    - Aplicaciones
      - Funcionales

#### Comunicaciones

- Subsistemas interconectados
- Enlaces y servicios de conectividad, vpn, gsm etc

<!-- -->

- Capacidad para absorver crecimiento
- Capacidad para absorver picos de stress de sistemas

<!-- -->

- Ubicaciones geográficas y estado general de las instalaciones
- Costos de servicios contratados y tiempo de respuesta ante incidentes

#### Coste de caida de servicio

- Corte inactividad
- Caida productividad
- Incumplimiento de SLA en provision de servicios

## Descripción Nagios

- Monitorización de servicios de red (SMTP, POP3, HTTP, NTTP, ICMP, SNMP).
- Monitorización de los recursos de un host (carga del procesador, uso de los discos, logs del sistema) en varios sistemas operativos, incluso Microsoft Windows con el plugin NRPE_NT.
- Monitorización remoto, a través de túneles SSL cifrados o SSH.
- Diseño simple de plugins, que permiten a los usuarios desarrollar sus propios chequeos de servicios dependiendo de sus necesidades, usando sus herramientas preferidas (Bash, C++, Perl, Ruby, Python, PHP, C#, etc.).
- Chequeo de servicios paralizados.
- Posibilidad de definir la jerarquía de la red, permitiendo distinguir entre host caídos y host inaccesibles.
- Notificaciones a los contactos cuando ocurren problemas en servicios o hosts, así como cuando son resueltos ( Vía email, pager ( Busca), Jabber, SMS, o cualquier método definido por el usuario junto con su correspondiente complemento).
- Posibilidad de definir manejadores de eventos que ejecuten al ocurrir un evento de un servicio o host para resoluciones de problemas proactivas.
- Rotación automática del archivo de registro.
- Soporte para implementar hosts de monitores redundantes.
- Interfaz web opcional, para observar el estado de la red actual, notificaciones, historial de problemas, archivos de registros, etc.

## Implementación

### Instalación en servidor

*Solamente en los casos que sea necesario* —generalmente cuando el sistema se instala sobre un sistema operativo en producción y poco actualizado— se realizará una compilación de todo el sistema desde sus archivos fuente.

Una vez compilado (o instalado de paquete, según la forma de instalación) se procederá a configurarlo.

#### Configuración

Las tareas de la configuración son las siguientes:

- Definición de usuario para la ejecución de los *daemons*
- Configuración de apache para la visualización de la consola. Generalmente se accede a la consola de Nagios utilizando vínculos cifrados, por lo que es necesario crear los certificados correspondientes.
- Si el cliente posee un directorio donde centraliza todos sus usuarios, se podrá utilizar un usuario especialmente creado para consultar los perfiles de los usuarios que accederán a la consola de administración. Entre los directorios soportados se encuentra Microsoft Active Directory.
- Configuración de servidor de correo (MTA) para el envío de todas las alertas que genera el sistema.
- Configuración de plugines básicos y comunes, entre ellos (plugines estándar de Nagios, todos los plugines estables que permiten consultas SNMP, plugin para monitoría a través de blackberry, etc.)
- Definición de Services (los services son elementos para monitorear en los diferentes sistemas operativos, por ejemplo: espacio en el disco F:, actividad de un procesador CPU, etc.)
- Definición de dependencias de servicios (en aquellos servicios que dependan de la funcionalidad de otro servicio para poder ser monitoreados se deben configurar las dependencias, esto le indica a Nagios que no tiene que declarar fuera de línea un servicio si del que depende está caído).
- Definición de Commands (los comandos son la forma en que se verifican el estado de los servicios declarados)
- Definición de HostGroups (los hostgroups son grupos de servidores que se monitorean, junto con el cliente de realiza la diagramación de estos grupos)
- Definición de Contacts (los contacts son personas o más bien direcciones de email que integrarán los ContactGroups)
- Definición de ContactGroups (los contactgroups son grupos que se utilizarán para enviar las alertas que se generen en la monitoría)
- Creación de directivas (especificación de dónde se encuentran varias definiciones de Nagios como Hostgroups, ContactGroups, Contacts, etc.)
- Configuración de sistema de *parsing* de logs para producir las gráficas (históricas) de rendimiento de los servicios
- Adaptación de notificaciones (las notificaciones, el texto del correo electrónico, puede ser adaptado para una mejor identificación de problemas a los administradores)

### Instalación en clientes

Dentro del proyecto se realizará la instalación de —por lo menos— dos tipos de clientes (generalmente Linux y Microsoft Windows).

Para la instalación de Linux solo se requerirá la implementación del sistema snmpd, en el caso de Microsoft Windows (versiones modernas) se activa el servicio interno de Windows de SNMP.

### Instalación y configuración de PNP

PNP es un agregado a nagios que analiza datos de rendimiento provista por plugines de Nagios y los graba automáticamente en bases de datos RRD (Round Robin Databases)

PNP fue programado utilizando la guía de desarrollo de plugines definida por el grupo de desarrolladores de plugins de Nagios, esta limitante está impuesta a propósito para forzar a los desarrolladores de plugines respetar las guías previamente delineadas.

Durante la configuración de este agregado a Nagios se realizan las siguientes tareas:

- Definición del modo en que se recolectarán los datos de nagios (default, bulk o bulk con NPCD)
- Configuración de interfaz web para que se puedan ver los gráficos directamente desde nagios
- Definición de look & feel de los gráficos y qué datos tendrá cada uno
