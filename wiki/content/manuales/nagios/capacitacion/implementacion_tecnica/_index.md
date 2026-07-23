---
title: "Implementación Técnica de Nagios"
date: 2017-07-21T12:54:09-03:00
---

## Instalación de Nagios

### Dependencias para compilación y puesta en marcha

<table>
<thead>
<tr>
<th>Sistema Operativo</th>
<th>Debian GNU/Linux</th>
</tr>
</thead>
<tbody>
<tr>
<td>Compilador</td>
<td>GNU GCC</td>
</tr>
<tr>
<td>Utilidades de comunicación<br />
Notificaciones</td>
<td>MTA<br />
SMS</td>
</tr>
<tr>
<td>Interfaz de usuario<br />
(Opcional)</td>
<td>Servidor Web</td>
</tr>
</tbody>
</table>

### Dependencias de Software

Para una correcta instalación de Nagios, con todas sus características es necesario tener instalados ciertos paquetes de software en el sistema, la instalación puede variar según la distribución de Linux que elijamos, si los tenemos empaquetados, o si los tenemos que compilar en instalar manualmente.

<table>
<thead>
<tr>
<th>Paquete</th>
<th>Descripción</th>
<th>Sitio web</th>
</tr>
</thead>
<tbody>
<tr>
<td>Perl</td>
<td>Interprete para el lenguaje de script Perl</td>
<td><a href="http://www.perl.org">http://www.perl.org</a></td>
</tr>
<tr>
<td>Net::SNMP</td>
<td>Modulo de Perl para consultas SNMP</td>
<td><a href="http://search.cpan.org/dist/Net-SNMP">http://search.cpan.org/dist/Net-SNMP</a></td>
</tr>
<tr>
<td>Crypt::DES</td>
<td>Modulo de Perl para encripción DES, necesario para consultas SNMPv3</td>
<td><a href="http://search.cpan.org/~dparis/Crypt-DES/">http://search.cpan.org/~dparis/Crypt-DES/</a></td>
</tr>
<tr>
<td>Digest::HMAC</td>
<td>Keyed-Hashing for Message Authentication</td>
<td><a href="http://search.cpan.org/dist/Digest-HMAC/">http://search.cpan.org/dist/Digest-HMAC/</a></td>
</tr>
<tr>
<td>Digest::SHA1</td>
<td>Perl interface to the SHA-1 algorithm</td>
<td><a href="http://search.cpan.org/dist/Digest-SHA1/">http://search.cpan.org/dist/Digest-SHA1/</a></td>
</tr>
<tr>
<td>RRDTool</td>
<td>Utilitario para generación de gráficas de red y además<br />
su módulo de integración con el lenguaje Perl</td>
<td><a href="http://oss.oetiker.ch/rrdtool">http://oss.oetiker.ch/rrdtool</a></td>
</tr>
<tr>
<td>Zlib</td>
<td>Librería de compresión utilizada por las utilidades graficas</td>
<td><a href="http://www.gzip.org/zlib/">http://www.gzip.org/zlib/</a></td>
</tr>
<tr>
<td>LibJPEG</td>
<td>Librería para exportación jpg</td>
<td><a href="http://www.ijg.org/">http://www.ijg.org/</a></td>
</tr>
<tr>
<td>LibPNG</td>
<td>Librería para exportación png</td>
<td><a href="http://www.libpng.org/pub/png/">http://www.libpng.org/pub/png/</a></td>
</tr>
<tr>
<td>Freetype2</td>
<td>Librería para procesamiento de fuentes</td>
<td><a href="http://www.freetype.org/">http://www.freetype.org/</a></td>
</tr>
<tr>
<td>Graphviz</td>
<td>Utilitario para generación de graficas</td>
<td><a href="http://www.graphviz.org/">http://www.graphviz.org/</a></td>
</tr>
<tr>
<td>XFree86-libs</td>
<td>Librerías gráficas generales</td>
<td><a href="http://koala.ilog.fr/lehors/xpm.html">http://koala.ilog.fr/lehors/xpm.html</a></td>
</tr>
<tr>
<td>Apache 2</td>
<td>Servidor Web</td>
<td><a href="http://httpd.apache.org/">http://httpd.apache.org/</a></td>
</tr>
<tr>
<td>PHP</td>
<td>Interprete de lenguaje de script</td>
<td><a href="http://www.php.net">http://www.php.net</a></td>
</tr>
<tr>
<td>MySQL</td>
<td>Sistema de base de datos</td>
<td><a href="http://www.mysql.com">http://www.mysql.com</a></td>
</tr>
<tr>
<td>Postfix</td>
<td>SMTP para enviar mail</td>
<td><a href="http://www.postfix.org/">http://www.postfix.org/</a></td>
</tr>
<tr>
<td>GD</td>
<td>Librería para generación de formatos graficos</td>
<td><a href="http://www.libgd.org/">http://www.libgd.org/</a></td>
</tr>
<tr>
<td>Nagvis</td>
<td>Aditivo para la generación de diagramas dinamicos</td>
<td><a href="http://www.nagvis.org/">http://www.nagvis.org/</a></td>
</tr>
<tr>
<td>PNP4Nagios</td>
<td>Aditivo para la generación de gráficos estadísticos y reportes visuales</td>
<td><a href="http://www.pnp4nagios.org/">http://www.pnp4nagios.org/</a></td>
</tr>
<tr>
<td>NDO</td>
<td>Agregado para articular Nagios con MySQL</td>
<td><a href="http://www.nagios.org">http://www.nagios.org</a></td>
</tr>
<tr>
<td>Plugins</td>
<td>Plugins de chequeo standard de Nagios</td>
<td><a href="http://www.nagios.org">http://www.nagios.org</a></td>
</tr>
<tr>
<td>SNMP Plugins</td>
<td>Plugins para la integración de chequeos SNMP de Nagios</td>
<td><a href="http://nagios.manubulon.com/">http://nagios.manubulon.com/</a></td>
</tr>
<tr>
<td>Nagios</td>
<td>Sitio de descarga oficial</td>
<td><a href="http://www.nagios.org">http://www.nagios.org</a></td>
</tr>
<tr>
<td>NagiosQL</td>
<td>Herramienta visual de configuración de Nagios via Web</td>
<td><a href="http://www.nagiosql.org/">http://www.nagiosql.org/</a></td>
</tr>
<tr>
<td>Dokuwiki</td>
<td>Herramienta de documentación colaborativa</td>
<td><a href="http://www.dokuwiki.org/">http://www.dokuwiki.org/</a></td>
</tr>
<tr>
<td>Syslog-Ng</td>
<td>Logueo de eventos del sistema</td>
<td><a href="http://www.balabit.com/network-security/syslog-ng/">http://www.balabit.com/network-security/syslog-ng/</a></td>
</tr>
<tr>
<td>SNARE</td>
<td>Agente Syslog para clientes Windows</td>
<td><a href="http://www.intersectalliance.com/projects/index.html">http://www.intersectalliance.com/projects/index.html</a></td>
</tr>
<tr>
<td>MK Livestatus</td>
<td>Aditivo para obtener los datos de Nagios<br />
en Vivo via Socket (muy útil para abandonar NDO)</td>
<td><a href="http://mathias-kettner.de/checkmk_livestatus.html">http://mathias-kettner.de/checkmk_livestatus.html</a></td>
</tr>
<tr>
<td>Gnokii</td>
<td>Aplicación de interfaz para celulares y modems 3G,<br />
para la realización de llamadas y alertas SMS</td>
<td><a href="http://gnokii.org">http://gnokii.org</a></td>
</tr>
<tr>
<td>Thruk</td>
<td>Interfaz alternativa para Nagios, con muchas funciones extras,<br />
basado en MK Livestatus</td>
<td>www.thruk.org</td>
</tr>
<tr>
<td>Interfacetable_v3t</td>
<td>Plugin para detectar y chequear las interfaces de un Router</td>
<td><a href="http://www.tontonitch.com/tiki/tiki-index.php?page=Nagios%20plugins%20-%20interfacetable_v3t">http://www.tontonitch.com/</a></td>
</tr>
<tr>
<td>Check TSM</td>
<td>Plugin para hacer chequeos generales en Tivoli TSM</td>
<td><a href="https://github.com/osklil/nagios-misc">https://github.com/osklil/nagios-misc</a></td>
</tr>
<tr>
<td>Check Iostat</td>
<td>Estadisticas y alertas en base a los datos de iostat</td>
<td><a href="http://sysengineers.wordpress.com/2010/05/27/check_iostat-pl-version-0-9-7/">http://sysengineers.wordpress.com/2010/05/27/check_iostat-pl-version-0-9-7/</a></td>
</tr>
<tr>
<td>Cliente Oracle<br />
Basic<br />
SQL*Plus</td>
<td>Cliente de Oracle para realizar los chequeos</td>
<td><a href="http://www.oracle.com/technetwork/database/features/instant-client/">http://www.oracle.com/technetwork/database/features/instant-client/</a></td>
</tr>
<tr>
<td>Eventdb</td>
<td>Integración de chequeos de Syslog</td>
<td><a href="https://www.netways.org/projects/eventdb">https://www.netways.org/projects/eventdb</a></td>
</tr>
<tr>
<td>Highchart for Nagios</td>
<td>Gráficos de PNP4Nagios en AJAX</td>
<td><a href="http://sourceforge.net/projects/highchartfornag/">http://sourceforge.net/projects/highchartfornag/</a></td>
</tr>
</tbody>
</table>

### Tareas

#### Descarga y compilación

En este apartado nos concentraremos en la descarga y compilación de los diferentes paquetes bajados en formato de *código fuente*.

Para empezar deberemos descargar el *código fuente* del software Nagios desde su sitio web, en formato tar.gz

Para ello descomprimiremos el paquete descargado y luego procederemos a compilarlo.

Luego ejecutaremos el script **configure**, lo que hacer dicho script, es generar el ambiente propicio para poder ejecutar el proceso de compilación, ayudados por medio de la utilidad **make**.

Ejemplo de salida de ejecución del script configure :

```
./configure --enable-corewindow --enable-event-broker
...
Creating sample config files in sample-config/ ...


*** Configuration summary for nagios 4.3.1 02-23-2017 ***:

 General Options:
 -------------------------
        Nagios executable:  nagios
        Nagios user/group:  nagios,nagios
       Command user/group:  nagios,nagios
             Event Broker:  yes
        Install ${prefix}:  /usr/local/nagios
    Install ${includedir}:  /usr/local/nagios/include/nagios
                Lock file:  ${prefix}/var/nagios.lock
   Check result directory:  ${prefix}/var/spool/checkresults
           Init directory:  /etc/init.d
  Apache conf.d directory:  /etc/httpd/conf.d
             Mail program:  /bin/mail
                  Host OS:  linux-gnu
          IOBroker Method:  epoll

 Web Interface Options:
 ------------------------
                 HTML URL:  http://localhost/nagios/
                  CGI URL:  http://localhost/nagios/cgi-bin/
 Traceroute (used by WAP):  /usr/sbin/traceroute


Review the options above for accuracy.  If they look okay,
type 'make all' to compile the main program and CGIs.
```

## Instalación de Nagios orientada a la distribución

Es útil pensar en utilizar varios paquetes de plugins disponibles en la distribución para poder facilitar el proceso de actualizaciones, igualmente es recomendable los componentes base de Nagios compilarlos y dejar separados los plugins personalizados para poder dejarlos independientes de las actualizaciones que apliquemos a los paquetes de la distribución.

### Caso concreto

| Sistema Operativo | Debian 9 |
|-------------------|----------|
| Version de Nagios | 4.3.1    |

Desinstalar paquetes o cosas innecesarias del paquete base de debian netinst e instalar el compilador GCC, realizar pruebas con el FLAG O3 al momento de la compilación.

make install-base

```
cd ./base && make install
make[1]: Entering directory '/root/nagios-4.3.1/base'
make install-basic
make[2]: Entering directory '/root/nagios-4.3.1/base'
/usr/bin/install -c -m 775 -o nagios -g nagios -d /usr/local/nagios/bin
/usr/bin/install -c -m 774 -o nagios -g nagios nagios /usr/local/nagios/bin
/usr/bin/install -c -m 774 -o nagios -g nagios nagiostats /usr/local/nagios/bin
make[2]: Leaving directory '/root/nagios-4.3.1/base'
make strip-post-install
make[2]: Entering directory '/root/nagios-4.3.1/base'
/usr/bin/strip /usr/local/nagios/bin/nagios
/usr/bin/strip /usr/local/nagios/bin/nagiostats
make[2]: Leaving directory '/root/nagios-4.3.1/base'
make[1]: Leaving directory '/root/nagios-4.3.1/base'
```

make install-init

```
/usr/bin/install -c -m 755 -d -o root -g root /etc/init.d
/usr/bin/install -c -m 755 -o root -g root daemon-init /etc/init.d/nagios

*** Init script installed ***
```

make install-commandmode

```
/usr/bin/install -c -m 775 -o nagios -g nagios -d /usr/local/nagios/var/rw
chmod g+s /usr/local/nagios/var/rw

*** External command directory configured ***
```

Paquetes debian de plugins a instalar junto con sus dependencias :

- monitoring-plugins-basic
- monitoring-plugins-common
- monitoring-plugins-standard
- nagios-plugins-contrib
- nagios-snmp-plugins
- nagios-nrpe-plugin
- nagios-plugin-check-multi
- python-nagiosplugin
- python3-nagiosplugin
- libnagios-plugin-perl (si Debian \< 9)

Paquetes extra para la administración del sistema :

- htop
- bmon
- tcpdump
- strace
- tcptrack
- ltrace

Paquetes extra para agregados de Nagios :

- rrdtool,librrds-perl
- Cliente oracle
  - oracle-instantclient-basic_10.2.0.5-2_amd64.deb
  - oracle-instantclient-sqlplus_10.2.0.5-2_amd64.deb
- php7.0-cli
- php7.0-curl
- php7.0-gd
- ntpstat
- ntpdate
- nmap
- xmlstarlet
- libmail-sendmail-perl
- php7.0-snmp

Nótese que no se realizó la instalación del servidor Web, lo máximo a dejar en el mismo servidor de Nagios es un **MTA** para notificaciones, por ejemplo postfix. Ya que el uso de un servidor Web dentro de la misma instalación de Nagios, dependiendo de su tamaño, puede afectar en seria forma a la performance del servidor.

## Configuración

Ahora nos referiremos a la configuración de los elementos instalados para su posterior *articulación* y funcionamiento en *conjunto*.

### Implementación

Para el correcto funcionamiento de Nagios, y asegurar escalabilidad con orden, se debe seguir una estructura de configuración y tener previamente planteados temas como:

-  Definición una estructura de archivos y directorios acorde a la situación, haciéndolo a su vez mas entendible para su posterior administración
-  Configurar Apache para su permitir su acceso via web por HTTP o HTTPS
-  En la mayoría de los equipos a monitorear mientras fuera posible instalar y dejar corriendo los servicios de SNMP
-  Configurar servicio de envío de emails
-  Definir grupos de contactos a los cuales se les enviarían los avisos de notificaciones, dependiendo de que hosts o servicio se trate.
-  Definir grupos de hosts y servicios, al tenerlos agrupados y verlos mas facilmente

*A continuación se detalla como llevar a cabo dichos pasos necesarios para su implementación.*

### En el servidor

Configuraciones necesarias en el servidor de monitoreo.

### Nagios

Algunos puntos basicos previos a la instalacion :

- **PATH** Esta es la ruta de instalación. Por defecto es /usr/local/nagios
- **Usuario** Usuario que va a usar nagios par ejecutarse. Debe crearse con adduser especificarle el PATH de Nagios como *su directorio home de inicio*, usualmente deberemos llamarlo nagios y debe estar dentro del grupo nagios
- **Grupo** Grupo de usuario que va a usar Nagios. Este grupo tendrá permisos sobre todos los ficheros y directorios de Nagios. Por defecto es nagios. Puede crearse con groupadd.
- **URL** Nagios utiliza una interfaz web para ejecutarse. Esta URL determina cual va a ser el directorio virtual que debe usar para instalarse. Por defecto /nagios, es decir, las peticiones irán dirigidas a <http://host/nagios>

##### Estructura de archivos

Una vez que compilamos e instalamos el paquete Nagios nos termina quedando una nomenclatura de directorios como la siguiente :

- **bin**
  - *Aqui se almacenan los binarios ejecutables*

<!-- -->

- **etc**
  - *Guarda la configuracion de Nagios*

<!-- -->

- **libexec**
  - *Se almacenan los plugins que efectuaran los chequeos a monitorear*

<!-- -->

- **sbin**
  - *Dentro de este directorio se mantienen los ejecutables CGI de la interfaz web*

<!-- -->

- **share**
  - *Organiza el contenido web a mostrar, iconos, html, php etc*

<!-- -->

- **var**
  - *Guarda los datos de ejecucion del monitoreo, estado de servicios, hosts, y logs*

**bin**

Dentro de este directorio encontramos los ejecutable principales, como el binario ***nagios*** que es el que se ejecuta como proceso en segundo plano, el objeto ***ndomod.o*** que es el modulo que se encarga de traducir las estadisticas de nagios en formato de consultas *MySQL*, y ***ndo2db*** que el proceso en segundo plano que se encarga conectarse con la base de datos para posteriormente ejecutar esas consultas.

**etc**

Este directorio guarda la configuración de Nagios, sus componentes, hosts/servicios a chequear, comandos de ejecucion, contactos de notificación, intervalos de chequeos. Dentro de el hay diferentes subdirectorios y archivos.

**libexec**

Alli se contienen lo ejecutables de los plugins que efectuan los chequeos, SNMP, SAP, Oracle, SSH, que pueden ser binarios, scripts en Perl, PHP, Shell, Java, etc.

**sbin**

Aqui se almacenan los ejecutables cgi que se ejecutaran para la visualizacion por web de la consola Nagios.

**share**

Aqui encontramos el contenido web, imagenes, logos, los aditivos como PNP, Nagvis y los datos que necesitan para funcionar estos.

**var**

Aqui se guardan los datos internos de Nagios, estadisticas de los chequeos, informacion de ejecucion actual, archivos de sockets, registros de logs, colas de ejecución de chequeos.

#### Archivos de configuracion nagios/etc

**cgi.cfg**

- *Definir archivo de configuracion principal de Nagios*
  - main_config_file=/usr/local/nagios/etc/nagios.cfg
- *Ruta donde se ubican los archivos a mostrar via web*
  - physical_html_path=/usr/local/nagios/share
- *Ruta del url a donde ubicar Nagios desde el navegador*
  - url_html_path=/nagios
- *Mostrar o no el icono de ayuda en la interfaz web*
  - show_context_help=0
- *Mostrar objetos pendientes de chequeo*
  - use_pending_states=1
- *Usar autenticacion para acceder a Nagios*
  - use_authentication=1
- *Tener usuario logueado por default (no recomendado, dejar comentado)*
  - \#default_user_name=guest
- *Usuarios con acceso permitido para ver la informacion de objetos (separados por comas)*
  - authorized_for_system_information=nagiosadmin
- *Usuarios con acceso permitido para ver la informacion de configuracion (separados por comas)*
  - authorized_for_configuration_information=nagiosadmin
- *Usuarios con acceso permitido ejecucion de comandos nagios (separados por comas)*
  - authorized_for_system_commands=nagiosadmin
- *Usuarios permitidos a ver informacion de hosts y servicios (separados por comas)*
  - authorized_for_all_services=nagiosadmin
  - authorized_for_all_hosts=nagiosadmin
- *Usuarios permitidos para ejecutar comandos sobre hosts y servicios (separados por comas)*
  - authorized_for_all_service_commands=nagiosadmin
  - authorized_for_all_host_commands=nagiosadmin
- *Tasa de refresco para la interfaz web en segundos*
  - refresh_rate=90

**htpasswd.users**

- Archivo con passwords encriptadas de los usuarios que se autentificaran por HTTP

**nagios.cfg**

- Archivo de configuracion principal de Nagios, aqui se especifican los directorios de trabajo y se incluyen los archivos de configuracion extra a utilizar por Nagios
- Con diversos parametros :
  - **log_file** se especifica el archivo de log a utilizar por Nagios
  - **cfg_file** se especifica un archivo de configuracion extra a incluir en la ejecucion de Nagios
  - **cfg_dir** se especifica un directorio con archivos de configuracion extra a incluir recursivamente en la ejecucion de Nagios
  - **log_archive_path** path donde se alojaran los archivos de log
  - **use_syslog** integracion con syslog

**ndo2db.cfg**

- Archivo de configuracion del daemon que se encarga de introducir las consultar generadas por el modulo ndomod

**ndomod.cfg**

- Modulo de Nagios que se encarga de traducir la informacion de ejecucion de Nagios en consultas MySQL, disponiendolas por medio de un socket

**resource.cfg**

- Archivo de configuracion donde se definen macros de ejecucion

Ejemplo de configuración teniendo en cuenta el ambiente mixto con plugins de paquetes Debian y compilados manualmente :

```bash>
**objects/**
  * Directorio de archivos generales de configuracion

**objects/commands.cfg**
  * Definicion de comandos de ejecucion por default, con los alias que queremos usar

**objects/contacts.cfg**
  * Definicion de contactos de notificacion

**objects/localhost.cfg**
  * Plantilla inicial para el chequeo del host local

**objects/printer.cfg**
  * Plantilla de ejemplo de chequeo de impresoras por SNMP

**objects/switch.cfg**
  * Plantilla de ejemplo de chequeo de switch por SNMP

**objects/templates.cfg**
  * Plantillas generales de host, contactos, y servicios

**objects/timeperiods.cfg**
  * Plantilla inicial para definir periodos de chequeos, aquí se definen los rangos de tiempo donde son válidos el envío de alertas y las verificaciones de los servicios que están funcionando

**objects/windows.cfg**
  * Plantilla de ejemplo de chequeo de equipos Windows

**services/**
  * Aqui vamos a definir los servicios que usaremos en los chequeos. Se define la métrica o el servicio a monitorizar y el host/grupo de hosts sobre el que se ejecuta

**var/rw/**
  * Alli se encuentra un archivo special de socket que realiza la comunicacion de los comando y ordenes de la interfaz web hacia nagios, como cambiar horarios de chequeo, deshabilitar notificaciones etc.
    * El archivo que alli se encuentra //nagios.cmd// debe tener permisos de escritura y lectura por el propietario y el grupo de pertenencia //nagios:nagcmd (660)//, //nagcmd// es un grupo especial en el cual vamos a incluir al usuario que ejecuta el servidor web (//ej. en apache sobre Debian www-data//), y asi poder enviar ordenes desde la interfaz web CGI. Esta es una característica avanzada de Nagios es que permite vía web la ejecución de ciertas tareas más allá del propio conjunto de CGI’s que vienen de serie, como por ejemplo la
caída o el reinicio del propio Nagios, etcétera. Para poder ejecutar este tipo de comandos es
necesario también configurar el sistema de una forma un tanto especial. No hay que olvidar que
al configurar Nagios de este modo se está permitiendo desde la web activar o desactivar opciones que en principio sólo estaban disponibles desde la consola del sistema. Para configurar Nagios de esta forma, hay que
editar el fichero principal //nagios.cfg// y añadir (o modificar si ya existen) las siguientes líneas:
<code>
check_external_commands=1
command_check_interval=-1
command_file=/usr/local/nagios/var/rw/nagios.cmd
```

Lo que hará que Nagios active el chequeo para buscar comandos externos, con tanta frecuencia como sea posible por el sistema y buscará los comandos en el archivo *nagios.cmd*.

En el siguiente gráfico detalla la organización recomendada de la configuración de Nagios.

<img src="/manuales/nagios/funcionamiento_nagios2.png" class="align-center" alt="funcionamiento_nagios2.png" />

<img src="/manuales/nagios/diagrama_nagios.dia.gz" class="align-center" alt="diagrama_nagios.dia.gz" />

Si por ejemplo tenemos estos dos casos :

**Nagios un país multiples provincias o localidades**

- **Buenos Aires**
  - Lanús
  - Florencio Varela
  - CABA
  - Olavarria
  - Bahia Blanca
- **Santa Fe**
  - Rosario
- **Neuquén**
  - Zapala
  - San Martin

En este caso, simplemente deberemos tener templates para hosts y servicios, que se dividan por tipo. Si es un servidor, equipo de red, scada etc.

**Nagios múltiples países y multiples provincias o localidades**

- **Argentina**
  - Buenos Aires
  - Neuquen
  - Tucuman
- **Brasil**
  - Rio
  - Cajati
- **Paraguay**
  - Ciudad del este
  - Villa hayes
- **Portugal**
  - Lisboa

En este caso algo mas complejo, deberemos tener templates para hosts divididos para cada país, templates para servicios divididos para cada país, templates para contactos divididos para cada país.

Siempre debemos utilizar templates para todo, porque cuando debemos hacer un cambio en masa es mucho mas simple y con menos posibilidad a errores.

### En el Monitoreo

## Creando directivas

Debemos crear algunas entradas de configuración para especificar donde encontramos los servicios, grupos, contactos etc, las mismas debemos incluirlas en nuestro archivo de configuración nagios.cfg

```
# Directorio con la configuración de grupos de Hosts de los Servidores
cfg_dir=/usr/local/nagios/etc/hostgroups
# Directorio con la configuración de grupos de servicios de los Servidores
cfg_dir=/usr/local/nagios/etc/servicegroup
# Directorio con la configuración de contactos
cfg_dir=/usr/local/nagios/etc/contacts
# Directorio con la configuración de grupos de contacto
cfg_dir=/usr/local/nagios/etc/contactgroups
# Directorio con la configuración de servicios
cfg_dir=/usr/local/nagios/etc/services
# Directorio con la configuración de los comandos
cfg_dir=/usr/local/nagios/etc/commands
# Directorio con la configuración de los equipos a monitorear
cfg_dir=/usr/local/nagios/etc/servers
```

Con la directiva cfg_dir el indicamos Nagios que tome como configuración los archivos con extencion “cfg” encontrados en tal directorio.

### Agregando Grupos de Servicios

Los grupos de servicio se utilizan para denotar un variedad de servicios sobre otros, debemos tener asignado aunque sea un servicio a ese grupo por que si no de lo contrario el Nagios mostrara un error al arranque, para eso lo asignamos en la propiedad *servicegroups* de un servicio en particular. Ejemplo de una entrada de grupo de servicios:

```
define servicegroup{
        servicegroup_name       lotus_response
        alias                   Lotus Reponse Services
        }
```

```
define servicegroup{
        servicegroup_name       {nombre corto del grupo de servicio}
        alias                   {alias descriptivo completo del grupo}
        }
```

Se deberá crear el archivo {nagios-dir}/etc/servicegroup/{nombregrupodeservicios.cfg} con las entradas correspondientes anteriormente explicadas.

### Agregando Servicios

A continuacion se muestra una tipica entrada de configuración de un servicio

```
define service {
        use                             windows
        host_name                       srv1,srv2
        hostgroup_name                  servidores-windows
        service_description             Verification disco F:
        servicegroups                   storage
        is_volatile                     0
        check_period                    24x7
        max_check_attempts              3
        normal_check_interval           5
        retry_check_interval            1
        contact_groups                  windows
        notification_interval           240
        notification_period             24x7
        notification_options            c,r
        check_command                   check_snmp_storage!^F!60!90!-C public!-r
}
```

```
define service {
        use                             {template de servicio a utilizar}
        host_name                       {hosts que ejecutan dicho servicio}
        hostgroup_name                  {grupos de host que ejecutan ese servicio}
        service_description             {descripcion del servicio}
        servicegroups                   {grupo al cual pertenece}
        is_volatile                     {si el servicio es volatil}
        check_period                    {periodo de tiempo para el chequeo}
        max_check_attempts              {maximo de intentos de chequeo}
        normal_check_interval           {intervalo de tiempo a programar los chequeos}
        retry_check_interval            {intervalo de tiempo para un re-chequeo}
        contact_groups                  {grupo de contacto};
        max_check_attempts              {maxima cantidad de chequeos}
        notification_interval           {intervalo de tiempo entre notificaciones}
        notification_period             {priodo de tiempo de notificaciones}
        notification_options            {cuando enviar notificaciones}
        check_command                   {comando de chequeo con sus argumentos}
}
```

**use**

Template de servicio a utilizar

**host_name**

Nombre del o los host a los cuales esta asignado dicho servicio

**hostgroup_name**

Nombre del grupo de host en los cuales esta asignado dicho servicio, es útil para cuando se vuelve tedioso poner uno por uno los nombres de los hosts a los cuales se asigna el servicio

**service_description**

Alias descriptivo del nombre del servicio

**contact_groups**

Grupos de contacto a los que enviar las notificaciones

**max_check_attempts**

Maxima cantidad de chequeos a efectuar por Nagios, antes de enviar un OK como resultado

**normal_check_interval**

Intervalo de tiempo antes de programar un nuevo chequeo del servicio

**retry_check_interval**

Intervalo de tiempo antes de realizar un re-chequeo del servicio

**notification_interval**

Esta directiva se utiliza para definir el número de las “unidades del tiempo” para esperar antes de re-notificar a un contacto que este servidor todavía está abajo o inalcanzable. A menos que se haya cambiado la directiva interval_length del valor prefijado de 60, este número significará minutos. Si este valor se establece a 0, Nagios re-no notificará contactos sobre los problemas para este host - solamente una notificación del problema será enviada.

**notification_period**

Periodo de tiempo en el cualse envia notificacion y notificacion

**notification_options**

Esta directiva indica a Nagios en que momentos debe enviar notificaciones de estado

- d = DOWN cuando el host esta caido

<!-- -->

- u = UNREACHABLE cuando el host no es visible o es inalcanzable

<!-- -->

- r = RECOVERY (OK) cuando el host se recupero

<!-- -->

- f = FLAPPING cuando es de estado cambiante

<!-- -->

- n = NONE no enviar notificaciones

**check_command**

Comando para efectuar el chequeo de dicho servicio

*Se deberá crear el archivo {nagios-dir}/etc/services/{nombreservicio.cfg} con las entradas correspondientes anteriormente explicadas.*

#### Dependencia del estado del servicio por el estado de otro servicio

En caso en los cuales el estado de un servicio dependa de la disponibilidad o el estado de otro, se pueden definir dependencias. Una entrada a modo ejemplo puede ser:

```
define servicedependency{
	host_name			Host A
	service_description		Service A
	dependent_host_name		Host B
	dependent_service_description	Service D
	execution_failure_criteria	u
	notification_failure_criteria	n
	}
```

```
define servicedependency{
	host_name			{host donde se ejecuta el servicio dependiente}
	service_description		{servicio dependiente}
	dependent_host_name		{host donde se ejecuta el servicio del cual se depende}
	dependent_service_description	{servicio del cual se depende}
	execution_failure_criteria	{criterio para establecer el estado}
	notification_failure_criteria	{notificar segun x estado}
	}
```

**host_name**

Nombre del o los host dentro de los cuales se ejecuta el servicio dependiente

**service_description**

Descripcion del servicio dependiente, debe ser igual a la entrada que aparece en la configuracion del servicio.

**dependent_host_name**

Host donde se esta ejecutando el servicio del cual se depende

**dependent_service_description**

Nombre descriptivo que corresponde al servicio del cual se depende, debe ser igual al de su configuracion de servicio

**execution_failure_criteria**

Criterios para definir el estado del servicio

**notification_failure_criteria**

En base a que estado realizar las notificaciones, si esta caido, si esta ok o no realizar notificaciones

#### Informacion extendida de servicio

En algunos casos podemos agregar un link informativo u externo haciendo referencia al servicio en ejecution

```
define serviceextinfo{
	host_name		linux2
	service_description	Carga del sistema Linux
	notes			Carga sistema
	notes_url		http://localhost/cargalinux.pl?host=linux2&service=Carga+Sistema
	icon_image		carga.png 
	icon_image_alt		Alertas de carga
	}
```

```
define serviceextinfo{
	host_name		{nombre del host}
	service_description	{nombre descriptivo del servicio}
	notes			{nota descriptiva sobre la informacion extra}
	notes_url		{url donde se encuentra la informacion extra}
	icon_image		{imagen de icono}
	icon_image_alt		{texto alternativo de la imagen}
	}
```

**host_name**

Host donde se ejecuta dicho servicio

**service_description**

Nombre descriptivo del servicio al cual corresponde la informacion extra

**notes**

Descripcion sobre “que es” o “a que corresponde” la información extra a mostrar

**notes_url**

Url donde encontrar la informacion extra

**icon_image**

Icono a referenciar el link de la información extra

**icon_image_alt**

Texto alternativo del icono

### Agregando Grupos de Hosts

Los host en nagios de puede agrupar mediante grupos y asi tener un listado aparte que los diferencia de los demas. por ejemplo tener por un lado los servidores SAP con Oracle y por otro los servidores Lotus, o Linux y Windows por separado etc.

Un archivo tipo de grupos de host se presenta a continuacion

```
define hostgroup {
    hostgroup_name                      florencio_varela
    alias                               Equipos de Florencio Varela
    members                             varela01,router-flv-1,srvfvl001
}
```

```
define hostgroup {
    hostgroup_name                      {nombre del grupo}
    alias                               {alias descriptivo}
    members                             {host miembros}
}
```

**hostgroup_name**

Nombre del grupo de hosts

**alias**

Alias descriptivo del grupo

**members**

Host que son miembros del grupo, debemos ingresar el host_name de cada uno separado por comas “,”

*Se deberá crear el archivo {nagios-dir}/etc/hostgroups/{nombregrupodehosts.cfg} con las entradas correspondientes anteriormente explicadas.*

### Agregando Hosts

Para configurar un host con o sin SNMP previamente instalado y configurado como lo indicado anteriormente, para su posterior monitoreo. Se debe crear una entrada en la configuracion de Nagios.

Un tipico archivos hosts.cfg

```
  define host{
        use                             servidores
        host_name                       servidorsap2
        hostgroup_name                  servidores-linux
        alias                           SAP SERVER
        address                         192.168.10.84
        parents                         buenos_aires
        contact_groups                  linux;
        max_check_attempts              10
        notification_interval           120
        notification_period             24x7
        notification_options            d,u,r
    }
```

```
  define host{
        use                             {template-host}
        host_name                       {nombre-host}
        hostgroup_name                  {grupos al que pertenece este host}
        alias                           {alias-descriptivo}
        address                         {ip}
        parents                         {host del que depende}   
        contact_groups                  {grupo de contacto};
        max_check_attempts              {maxima cantidad de chequeos}
        notification_interval           {intervalo de tiempo entre notificaciones}
        notification_period             {priodo de tiempo de notificaciones}
        notification_options            {cuando enviar notificaciones}
    }
```

**use**

Template de host a utilizar

**host_name**

Nombre del host

**hostgroup_name**

Grupos a los que pertenece este host

**address**

Direccion IP del host

**parents**

Host del que depende y que esta delante suyo, por ejemplo puede ser un router o un equipo que le brinde la conectividad etc, y en el mapa se dibujara como dependiente de ese nodo

**contact_groups**

Grupos de contacto a los que enviar las notificaciones

**max_check_attempts**

Maxima cantidad de chequeos a efectuar por Nagios, antes de enviar un OK como resultado

**notification_interval**

Esta directiva se utiliza para definir el número de las “unidades del tiempo” para esperar antes de re-notificar a un contacto que este servidor todavía está abajo o inalcanzable. A menos que se haya cambiado la directiva interval_length del valor prefijado de 60, este número significará minutos. Si este valor se establece a 0, Nagios re-no notificará contactos sobre los problemas para este host - solamente una notificación del problema será enviada.

**notification_period**

Periodo de tiempo en el cualse envia notificacion y notificacion

**notification_options**

Esta directiva indica a Nagios en que momentos debe enviar notificaciones de estado

- d = DOWN cuando el host esta caido

<!-- -->

- u = UNREACHABLE cuando el host no es visible o es inalcanzable

<!-- -->

- r = RECOVERY (OK) cuando el host se recupero

<!-- -->

- f = FLAPPING cuando es de estado cambiante

<!-- -->

- n = NONE no enviar notificaciones

//Se deberá crear el un subdirectorio correspondiente al nombre del host y segun corresponda ubicarlo en el directorio servers/{linux-windows-lotus} o routers y deentro crear un archivo hosts.cfg con la configuración anteriormente explicada, //

#### Informacion extendida de host

La informacion extendida de host se utiliza para el look and feel de los host dentro de los mapas de estado, ya sea tanto el 2D como el 3D

```
  define hostextinfo{
        host_name       linuxoracle
        notes           Servidor Oracle de uniface
        icon_image      oracle.png
        icon_image_alt  Oracle
        vrml_image      oracle.png
        statusmap_image oracle.gd2
    }
```

```
  define hostextinfo{
        host_name       {nombre de host}
        notes           {descripcion para el host}
        icon_image      {logo para ver en la interfaz web}
        icon_image_alt  {texto para el logo}
        vrml_image      {logo para ver en el mapa 3D}
        statusmap_image {logo para ver en el mapa 2D}
    }
```

**host_name**

Nombre del host al cual corresponde la información dada

**notes**

Pequeña nota descriptiva de la informacion del host a presentar en los mapas de estado

**icon_image**

Icono a visualizar en el entorno html de la consola Nagios

**icon_image_alt**

Texto alternativo para mostrar al logo

**vrml_image**

Icono a visualizar en el mapa 3D

**statusmap_image**

Icono a visualizar en el mapa 2D

*Se deberá crear en el archivo hostextinfo.cfg dentro subdirectorio correspondiente al host con las entradas de configuración anteriormente explicadas. Lo iconos se encuentran dentro del directorio {nagiosdir}share/images/logos/ tanto en su version png como gd*

Para convertir una imagen png comun a un icono gd2 (necesario para la generacion del grafico de statusmap 2D) debemos ejecutar el siguiente comando:

```
pngtogd2 mi-icono.png mi-icono.gd2  1  1
```

El primer parametro es mi ya existente imagen png, el segundo parametro es el nombre de archivo de salida en formato gd2, el parametro 1 se refiere a que la cree en formato raw (crudo), y el segudo parametro es para que lo cree sin compresion, todo esto se realiza dentro del directorio logos anteriormente explciado.

#### Dependencia del estado de host por el estado de otro host

En casos donde hay host donde su estado depende del estado de otro host, es posible especificar dependencias. Aqui vemos una entrada tipica

```
 define hostdependency{
        host_name                               linuxsap2
        dependent_host_name                     linuxoracle
        notification_failure_criteria           d
    }
```

```
 define hostdependency{
        host_name                               {nombre del host a referise}
        dependent_host_name                     {nombre del host del cual depende}
        notification_failure_criteria           {opciones de notificacion}
    }
```

**host_name**

Nombre del host al cual corresponde la información dada

**dependent_host_name**

Nombre del host del cual depende

**notification_failure_criteria**

Opciones de notificacion en caso de cada estado

*Se deberá crear en el archivo hostdependency.cfg dentro subdirectorio correspondiente al host con las entradas de configuración anteriormente explicadas.*

### Definiendo tiempos de chequeo

Dentro de Nagios se pueden establecer definiciones para controlar cuando diversos aspectos de la lógica de vigilancia y alerta sobre elementos monitoreador por ejemplo que tal servicio o host se chequee en ciertos intervalos de tiempo, por ejemplo si un host no debe ser chequeado los domingos o un servicio debe ser chequeado solo de lunes a viernes de 08:00 HS a 18:00 HS etc

A su ves las definiciones Timeperod pueden contener varios tipos de directivas, entre los días de semana, días del mes, y las fechas. El orden de precedencia de los distintos tipos de directivas (en orden descendente) es el siguiente:

* Fecha del calendario (2008-01-01)
* Mes específico fecha (1 de enero)
* Mes día genéricos (Día 15)
* Compensar los días de la semana de mes específico (2 º martes de diciembre)
* Compensar los días de la semana (3 º lunes)
* Normal del día de la semana (martes) 

Ejemplos de las diferentes directivas timeperiod que podemos implementar :

*Chequeos las 24 horas los 7 dias de la semana*

```
define timeperiod{
        timeperiod_name 24x7
        alias           24 Hours A Day, 7 Days A Week
        sunday          00:00-24:00
        monday          00:00-24:00
        tuesday         00:00-24:00
        wednesday       00:00-24:00
        thursday        00:00-24:00
        friday          00:00-24:00
        saturday        00:00-24:00
        }
```

*Chequeos en las horas laborales lunes a viernes de 9 a 17*

```
define timeperiod{
        timeperiod_name workhours
        alias           Normal Work Hours
        monday          09:00-17:00
        tuesday         09:00-17:00
        wednesday       09:00-17:00
        thursday        09:00-17:00
        friday          09:00-17:00
        }
```

*Chequeos sin tiempos, o sea no chequea en ningún momento*

```
# 'none' timeperiod definition
define timeperiod{
        timeperiod_name none
        alias           No Time Is A Good Time
        }
```

*En ciertos dias feriados excluir el chequeo servicios, ej Navidad, Año nuevo etc*

```
define timeperiod{
        name                    us-holidays
        timeperiod_name         us-holidays
        alias                   U.S. Holidays
        january 1               00:00-00:00     ; New Years
        monday -1 may           00:00-00:00     ; Memorial Day (last Monday in May)
        july 4                  00:00-00:00     ; Independence Day
        monday 1 september      00:00-00:00     ; Labor Day (first Monday in September)
        thursday -1 november    00:00-00:00     ; Thanksgiving (last Thursday in November)
        december 25             00:00-00:00     ; Christmas
        }
```

*Definimos un periodo de tiempo que chequee las 24 horas del dia los 7 dias de la semana, pero que incluya las excepciones anteriormente mostradas*

```
define timeperiod{
        timeperiod_name 24x7_sans_holidays
        alias           24x7 Sans Holidays
        use             us-holidays             ; Agregar excepciones
        sunday          00:00-24:00
        monday          00:00-24:00
        tuesday         00:00-24:00
        wednesday       00:00-24:00
        thursday        00:00-24:00
        friday          00:00-24:00
        saturday        00:00-24:00
        }
```

*Otra manera de evitar tiempos de chequeo es con la directiva exclude*

// Como ejemplo podemos definir 3 timeperiods//

```
define timeperiod{
        name                    weekdays
        timeperiod_name         weekdays
        monday                  00:00-24:00
        tuesday                 00:00-24:00
        wednesday               00:00-24:00
        thursday                00:00-24:00
        friday                  00:00-24:00
        }
define timeperiod{
        name                    weekends
        timeperiod_name         weekends
        saturday                00:00-24:00
        sunday                  00:00-24:00
	}
define timeperiod{
        name                    holidays
        timeperiod_name         holidays
        january 1               00:00-24:00	; New Year's Day
        2008-03-23              00:00-24:00	; Easter (2008)
        2009-04-12              00:00-24:00	; Easter (2009)
        monday -1 may           00:00-24:00	; Memorial Day (Last Monday in May)
        july 4                  00:00-24:00	; Independence Day
        monday 1 september      00:00-24:00	; Labor Day (1st Monday in September)
        thursday 4 november     00:00-24:00	; Thanksgiving (4th Thursday in November)
        december 25             00:00-24:00	; Christmas
        december 31             17:00-24:00	; New Year's Eve (5pm onwards)
	}
```

*Ahora definimos un timeperiod llamadas por ejemplo, incluya los dias de la semana, pero excluya los dias festivos*

```
define timeperiod{
	timeperiod_name		llamadas
	use			weekdays	; Include weekdays
	exclude			holidays	; Exclude holiday dates/times defined elsewhere
	}
```

*Alternando dias, o sea desde el primero de agosto de 2007 cada dos dias notificar, si en vez de / 2 ponemos / 14 lo realizara cada 14 dias*

```
define timeperiod{
	timeperiod_name		john-oncall
	2007-08-01 / 2		00:00-24:00	; Every two days, starting August 1st, 2007
	}
```

*En la entrada del contacto deberemos especificarle los timeperiods para hosts y servicios*

```
define contact{
	contact_name			john
	...
	host_notification_period	john-oncall
	service_notification_period	john-oncall
	}
```

### Agregando Comandos

En Nagios los encargados de recabar los datos del monitoreo, de mostrar alertas, de todas las tareas, son los comandos.

Los mismos se dividen en comandos de performance y en comandos de chequeo, los primeros son utilizados para algunos casos en particular.

Los comandos de chequeo no traen datos de los equipos a monitorear, como consumo de CPU, Memoria, Disco, procesos corriendo, puertos abiertos etc, es decir todos los datos necesarios de la monitoria.

Los comandos de performance se utilizan cuando hay que guardar ciertos datos o enviarlos a algún host externo etc, con información de algún servicio.

Una entrada en un archivo de configuración de comandos puede ser como la siguiente

```
define command{
        command_name check_snmp_mem
        command_line $USER1$/check_snmp_mem.pl -H $HOSTADDRESS$ $ARG1$ -w $ARG2$ -c $ARG3$ $ARG4$
        }
```

```
define command{
        command_name {nombre del comando}
        command_line {datos de  ejecucion}
        }
```

**command_name**

El nombre que el comando tendra para nuestra configuración personal de Nagios

**command_line**

Modo del cual Nagios ejecutara el comando en cuestión, con su ruta física y argumentos Lo que vemos en entre signos \$ son variables internas de nagios, llamadas macros, las mas comunes son:

**\$USER1\$** : Contiene datos del path de ejecución de los plugins de Nagios

**\$HOSTADDRESS\$** : Tiene la IP de hosts desde el cual se esta corriendo el servicio

**\$ARG1\$ \$ARG2\$ \$ARG3\$ \$ARG4\$** : Son los números en orden de argumentos que recibe el comando a ejecutar

Podemos definir nuestros propios macros seteando variables en el archivo resource.cfg

*Se deberán agregar al archivo {nagios-dir}/etc/commands.cfg las entradas correspondientes anteriormente explicadas.*

### Manejadores de Eventos - Event Handler

Cuando hablamos de **event handler** o **manejador de eventos**, nos referimos a funciones que responden a eventos que se producen, como pueden ser un cambio de estado.

### Configuración de alertas

#### Agregando Grupos de contacto

Para que el Nagios envíe notificaciones sobre el estado de los servicios es necesario definir grupos a los cuales enviárselas, y dentro de ellos estarán los miembros a cuales enviarlos

A continuación podemos ver una configuración típica de un grupo de contactos

```
   define contactgroup{
        contactgroup_name               admin
        alias                           Administrators
        members                         admin-sap,admin-windows
    }
```

```
   define contactgroup{
        contactgroup_name               {nombre del grupo contacto}
        alias                           {descripcion}
        members                         {miembros del grupo}
    }
```

**contactgroup_name**

Nombre que se le asignara al grupo de contacto

**alias**

Descripción corta para identificar al grupo

**members**

Miembros del grupo separados por comas

*Se deberá crear el archivo {nagios-dir}/etc/contactgroups/{nombregrupodecontacto.cfg} con las entradas correspondientes anteriormente explicadas.*

#### Agregando Contactos

Para recibir las notificaciones de Nagios es necesario generar contactos que estén incluidos en diferentes grupos de contactos, una configuración simple para un contacto se ve como la siguiente entrada

```
   define contact{
        contact_name                   admin
        alias                          Administrador Nagios
        contactgroups                  admin
        service_notification_period    24x7
        host_notification_period       24x7
        service_notification_options   w,u,c,r
        host_notification_options      d,u,r
        service_notification_commands  notify-by-email
        host_notification_commands     host-notify-by-email
        email                          root@localhost
    }
```

```
   define contact{
        contact_name                   {nombre del contacto}
        alias                          {descripcion del contacto}
        contactgroups                  {grupo de contactos al cual pertenece}
        service_notification_period    {priodo de tiempo de notificaciones de servicios}
        host_notification_period       {priodo de tiempo de notificaciones de hosts}
        service_notification_options   {opciones de notificacion por servicio}
        host_notification_options      {opciones de notificacion por host}
        service_notification_commands  {comando de notificacion a utilizar por servicio}
        host_notification_commands     {comando de notificacion a utilizar por host}
        email                          {direccion de email del contacto}
    }
```

**contact_name**

Nombre literal a asignarle al contacto

**alias**

Alias descriptivo del contacto, ej Administrador de Routers

**contactgroups**

Grupos de contactos a los cuales pertenece

**service_notification_period**

Periodo de tiempo en el cual recibir notificaciones sobre el estado de los servicios

**host_notification_period**

Periodo de tiempo en el cual recibir notificaciones sobre el estado de los hosts

**service_notification_options**

Opciones de cuando realizar una notificación sobre el estado de un servicio

**host_notification_options**

Opciones de cuando realizar una notificación sobre el estado de un host

**service_notification_commands**

Comando para realizar la notificación del estado del servicio, podemos definir múltiples comandos

**host_notification_commands**

Comando para realizar la notificacion del estado del host, podemos definir múltiples comandos

**email**

Email perteneciente al contacto en el cual recibira las notificaciones por email. Para que esto funcione se debe tener correctamente configurado el mail delivery local.

*Se deberá crear el archivo {nagios-dir}/etc/contacts/{nombredecontacto.cfg} con las entradas correspondientes anteriormente explicadas.*
