---
title: "Plugins usuales de Nagios"
date: 2015-10-16T11:42:44-03:00
---

## Comandos default en Nagios

El sistema Nagios incorpora un gran número de comandos por default que se pueden utilizar. Algunos de ellos hacen uso de librerías y paquetes que deben estar instalados en el sistema. En las siguientes líneas se comentan cuales son los comandos que vienen por default y que hacen.

##### check_by_ssh

Este comando es muy interesante interesante. Permite ejecutar comandos en ordenadores remotos vía SSH (de forma segura, por tanto). El resultado de ese comando será tomado por Nagios.

Uso : check_by_ssh -H \<host\> -C \<command\> \[-fqv\] \[-1\|-2\] \[-4\|-6\]

         [-S [lines]] [-E [lines]] [-t timeout] [-i identity]
         [-l user] [-n name] [-s servicelist] [-O outputfile]
         [-p port] [-o ssh-option]

Opciones: -h, –help

      Imprimir ayuda detallada

-V, –version

      Imprimir informacion de version

-H, –hostname=ADDRESS

      Hostname, direccion IP, or socket unix (path completo en este caso)

-p, –port=INTEGER

      Puerto a chequear

-4, –use-ipv4

      Usar IPv4

-6, –use-ipv6

      Usar IPv6

-1, –proto1

      Usar protocolo 1 de SSH [optional]

-2, –proto2

      Usar protocolo 2 de SSH [optional]

-S, –skip-stdout\[=n\]

      Ignora todas o las primeras n lineas de STDOUT [optional]

-E, –skip-stderr\[=n\]

      Ignora todas o las primeras n lineas de STDERR [optional]

-f

      Decirle al SSH que realize un fork en vez de una tty [optional]. Siempre devolver OK si ssh es ejecutado

-C, –command=‘COMMAND STRING’

      Comando a ejecutar en la maquina remota

-l, –logname=USERNAME

      Nombre de usuario SSH [optional]

-i, –identity=KEYFILE

      Llave SSH autorizada [optional]

-O, –output=FILE

      archivo de comando externo de Nagios [optional]

-s, –services=LIST

      lista de nombres de servicios Nagios separados por ':' [optional]

-n, –name=NAME

      nombre corto del host en la configuracion de Nagios [optional]

-o, –ssh-option=OPTION

      llamar ssh con la opcion '-o' (puede ser usada multiples veces) [optional]

-q, –quiet

      Tell ssh to suppress warning and diagnostic messages [optional]

-w, –warning=DOUBLE

      Response time to result in warning status (seconds)

-c, –critical=DOUBLE

      Response time to result in critical status (seconds)

-t, –timeout=INTEGER

      Seconds before connection times out (default: 10)

-v, –verbose

      Mostrar detalles de la linea de comandos para debug (Nagios truncara la salida)

La forma mas comun de uso, es por medio de una llave ssh con el argumento ‘-i’. De este modo el par de llaves deben tener una contraseña nula y la llave publica debe estar listada en el archivo authorized_keys del host remoto. Usualmente la llave debe estar restringida para correr solo un comando en el servidor remoto. Si el script remoto permite la adicion de argumentos, puede actuar como una agente al estilo proxy para ejecutar otros comandos remotos.

Ejemplo de como configurarlo como **comando**

```
define command {
 command_name    check_ssh_load
 command_line    $USER1$/check_by_ssh -H $HOSTADDRESS$ -C "/user/bin/check_load -w $ARG1$ -c $ARG2$"
}
```

Ejemplo de como configurarlo en un **servicio**

```
define service {
 use                 local-service
 hostgroup_name      ssh-nagios-services
 service_description Current Load
 check_command       check_ssh_load!5.0,4.0,3.0!10.0,6.0,4.0
}
```

Ejemplo de como dar de alta el **hostgroup** de ejecucion

```
define hostgroup {
 hostgroup_name  ssh-nagios-services
 alias           Nagios over SSH
 members         remote1,remote2
}
```

##### check_dig

Este comando sirve para comprobar el funcionamiento del servicio de DNS en un equipo remoto. Utiliza dig para esto.

Usage:check_dig -l \<query_address\> \[-H \<host\>\] \[-p \<server port\>\] \[-T \<query type\>\] \[-w \<warning interval\>\] \[-c \<critical interval\>\] \[-t \<timeout\>\] \[-a \<expected answer address\>\] \[-v\]

Ejemplos: Esto enviara una consulta TCP al servidor DNS consultando por el nombre www.example.com

```
 check_dig -H DNSSERVER -l www.example.com -A "+tcp"
```

Definir como comando:

```
define command{
    command_name    check_dig
    command_line    $USER1$/check_dig -H '$HOSTADDRESS$' -l '$ARG1$'
}
```

Definir como servicio:

```
define service{
    use                     generic-service
    host_name               nombre_host
    service_description     dig
    check_command           check_dig!www.google.com
}
```

##### check_disk

Este comando sirve para comprobar el espacio libre de un volumen montado en el sistema de ficheros donde se esté ejecutando Nagios. Permite especificar dos umbrales y generar disparadores advertencias cuando se supera el menor, y errores críticos cuando se supera el segundo.

Usage: check_disk -w limit -c limit \[-W limit\] \[-K limit\] {-p path \| -x device} \[-C\] \[-E\] \[-e\] \[-g group \] \[-k\] \[-l\] \[-M\] \[-m\] \[-R path \] \[-r path \] \[-t timeout\] \[-u unit\] \[-v\] \[-X type\]

Ejemplos: check_disk -w 10% -c 5% -p /tmp -p /var -C -w 100000 -c 50000 -p /

      Checks /tmp and /var at 10% and 5%, and / at 100MB and 50MB

check_disk -w 100M -c 50M -C -w 1000M -c 500M -g sidDATA -r ‘^/oracle/SID/data.\*\$’

      Checks all filesystems not matching -r at 100M and 50M. The fs matching the -r regex
      are grouped which means the freespace thresholds are applied to all disks together

check_disk -w 100M -c 50M -C -w 1000M -c 500M -p /foo -C -w 5% -c 3% -p /bar

      Checks /foo for 1000M/500M and /bar for 5/3%. All remaining volumes use 100M/50M

Definir como comando:

```
define command{
    command_name    check_all_disks
    command_line    $USER1$/check_disk -w '$ARG1$' -c '$ARG2$'
}
```

Definir como servicio

```
define service{
    use                     generic-service
    host_name               nombre_host
    service_description     Disk Space
    check_command           check_all_disks!20%!10%
}
```

##### check_disk_smb

Planificación, especificación, diseño y evaluación de redes Este comando funciona exactamente igual que check_disk pero realiza la comprobación utilizando samba para realizar la comprobación de volúmenes compartidos en quipos remotos, en redes Windows.

Perl Check SMB Disk plugin for Nagios

Usage: check_disk_smb -H \<host\> -s \<share\> -u \<user\> -p \<password\>

        -w <warn> -c <crit> [-W <workgroup>] [-P <port>]

-H, –hostname=HOST

     Nombre NetBIOS del servidor

-s, –share=STRING

     Recurso compartido a testear

-W, –workgroup=STRING

     Grupo de trabajo o dominio usado (Default "WORKGROUP")

-u, –user=STRING

     Usuario a loguearse en el servidor. (Defaults "guest")

-p, –password=STRING

     Contraseña para loguearse en el servidor. (Defaults NULL)

-w, –warning=INTEGER or INTEGER\[kMG\]

     Percent of used space at which a warning will be generated (Default: 85%)

-c, –critical=INTEGER or INTEGER\[kMG\]

     Percent of used space at which a critical will be generated (Defaults: 95%)

-P, –port=INTEGER

     Port to be used to connect to. Some Windows boxes use 139, others 445 (Defaults to smbclient default)

     If thresholds are followed by either a k, M, or G then check to see if that
     much disk space is available (kilobytes, Megabytes, Gigabytes)

     Porcentaje WARNING debe ser menor al porcentaje CRITICAL
     Warning (remaining) disk space should be greater than critical.

##### check_dns

Este comando permite hacer una consulta DNS para averiguar la dirección IP de un equipo dado el nombre o viceversa. Utiliza nslookup para ello; permite especificar el servidor DNS a usar o si no usa el o los especificados en /etc/resolv.conf.

Usage:check_dns -H host \[-s server\] \[-a expected-address\] \[-A\] \[-t timeout\] \[-w warn\] \[-c crit\]

##### check_dummy

Este comando permite realizar una consulta a un dispositivo ficticio (devuelve el mismo parámetro que se le pasa). Puede ser utilizado para comprobaciones y depuraciones.

Usage: check_dummy \<estado numerico\> \[optional text\]

##### check_flexlm

Este comando comprueba el funcionamiento de un sistema FlexLM. Este sistema es un servidor de licencias en red usado para obtener permisos de uso de software en red. Devuelve distintos errores dependiendo del estado de estos servidores de licencias.

##### check_ftp

Este comando realiza comprobaciones de conexión a un servidor FTP remoto. Permite conocer el estado de este servicio.

This plugin tests FTP connections with the specified host (or unix socket).

Usage:check_ftp -H host -p port \[-w \<warning time\>\] \[-c \<critical time\>\] \[-s \<send string\>\] \[-e \<expect string\>\] \[-q \<quit string\>\]\[-m \<maximum bytes\>\] \[-d \<delay\>\] \[-t \<timeout seconds\>\] \[-r \<refuse state\>\] \[-M \<mismatch state\>\] \[-v\] \[-4\|-6\] \[-j\] \[-D \<days to cert expiry\>\] \[-S \<use SSL\>\] \[-E\]

##### check_http

Este comando comprueba servicios HTTP y HTTPS en equipos remotos. Permite además realizar el seguimiento de redirecciones, tiempos de conexión, la expiración de los certificados para SSL, etcétera. Es especialmente útil para servidores web que sirvan de base para aplicaciones de comercio electrónico.

Usage: check_http -H \<vhost\> \| -I \<IP-address\> \[-u \<uri\>\] \[-p \<port\>\]

         [-w <warn time>] [-c <critical time>] [-t <timeout>] [-L]
         [-a auth] [-f <ok | warn | critcal | follow>] [-e <expect>]
         [-s string] [-l] [-r <regex> | -R <case-insensitive regex>] [-P string]
         [-m <min_pg_size>:<max_pg_size>] [-4|-6] [-N] [-M <age>] [-A string]
         [-k string] [-S] [-C <age>] [-T <content-type>]

Ejemplos: CHECK CONTENT: check_http -w 5 -c 10 –ssl -H www.verisign.com

When the ‘www.verisign.com’ server returns its content within 5 seconds, a STATE_OK will be returned. When the server returns its content but exceeds the 5-second threshold, a STATE_WARNING will be returned. When an error occurs, a STATE_CRITICAL will be returned.

CHECK CERTIFICATE: check_http -H www.verisign.com -C 14

When the certificate of ‘www.verisign.com’ is valid for more than 14 days, a STATE_OK is returned. When the certificate is still valid, but for less than 14 days, a STATE_WARNING is returned. A STATE_CRITICAL will be returned when the certificate is expired.

Ejemplo desde un script

```bash>
== check_ifoperstatus ==

Este comando comprueba el estado de operación de interfaces de red remotas por medio de SNMP v1 o SNMP v3.

== check_ifstatus ==

Este comando comprueba el estado general de interfaces de red remotas por medio de SNMP v1 o SNMP v3.


== check_imap ==

Este comando realiza conexiones contra un servidor IMAP para comprobar su estado de
funcionamiento. Permite generar advertencias y errores críticos.

Definir como comando :

<code>
define command{
    command_name    check_imap
    command_line    $USER1$/check_imap -H '$HOSTADDRESS$'
}
```

Chequear IMAP con SSL

```
define command {
    command_name    check_simap
    command_line    $USER!$/plugins/check_imap -p 993 -H '$HOSTADDRESS$' -S
}
```

Definir como servicio:

```
define service{
    use                     generic-service
    host_name               nombre_host
    service_description     imaps
    check_command           check_simap
}
```

##### check_ircd

Este comando comprueba el funcionamiento de un servidor de IRC remoto. Realiza conexiones para ello, esta escrito en Perl.

##### check_ldap

Este comando realiza conexiones y búsquedas LDAP contra un servidor remoto y comprueba así su estado de funcionamiento y si responde dentro del tiempo esperado o no.

Usage: check_ldap -H \<host\> -b \<base_dn\> \[-p \<port\>\] \[-a \<attr\>\] \[-D \<binddn\>\]

         [-P <password>] [-w <warn_time>] [-c <crit_time>] [-t timeout]
         [-2|-3] [-4|-6]

Notes: If this plugin is called via ‘check_ldaps’, method ‘STARTTLS’ will be implied (using default port 389) unless –port=636 is specified. In that case ‘SSL on connect’ will be used no matter how the plugin was called. This detection is deprecated, please use ‘check_ldap’ with the ‘–starttls’ or ‘–ssl’ flags to define the behaviour explicitly instead.

##### check_load

Este comando trabaja en local en la máquina que está ejecutando el sistema Nagios. Comprueba la carga del sistema en función de unos umbrales que tiene preestablecidos y permite generar advertencias o errores severos según sea esta carga.

Uso:check_load \[-r\] -w WLOAD1,WLOAD5,WLOAD15 -c CLOAD1,CLOAD5,CLOAD15

-r, –percpu

      Divide la carga por el numero de CPU's siempre que fuera posible

       Cambia a WARNING si el promedio de carga excede “WLOADn”

       Cambia a CRITICAL si el promedio de carga excede “CLOADn” 

Definir como comando :

```
define command{
    command_name    check_load
    command_line    $USER1$/check_load --warning=$ARG1$,$ARG2$,$ARG3$ --critical=$ARG4$,$ARG5$,$ARG6$
}
```

```
define service{
    use                       generic-service
    host_name                 nombre_host
    service_description       Current Load
    check_command             check_load!5.0!4.0!3.0!10.0!6.0!4.0
}
```

##### check_log

Este comando es muy interesante para administradores del sistema. Funciona en local y permite buscar coincidencia de patrones en ficheros de suceso. Cuando el patrón que se busca es encontrado, Nagios recoge esta incidencia.

Usage: check_log -F logfile -O oldlog -q query Usage: check_log –help Usage: check_log –version

##### check_mailq

Este comando funciona en local en la máquina que corre Nagios. Permite comprobar el número de mensajes que hay en espera en las colas de Sendmail. Se puede establecer un límite para que se genere una notificación en ese caso.

```
Usage: check_mailq -w <warn> -c <crit> [-W <warn>] [-C <crit>] [-M <MTA>] [-t <timeout>] [-v verbose]

   Checks the number of messages in the mail queue (supports multiple sendmail queues, qmail)
   Feedback/patches to support non-sendmail mailqueue welcome

-w (--warning)   = Min. number of messages in queue to generate warning
-c (--critical)  = Min. number of messages in queu to generate critical alert ( w < c )
-W (--Warning)   = Min. number of messages for same domain in queue to generate warning
-C (--Critical)  = Min. number of messages for same domain in queue to generate critical alert ( W < C )
-t (--timeout)   = Plugin timeout in seconds (default = 15)
-M (--mailserver) = [ sendmail | qmail | postfix | exim ] (default = sendmail)
-h (--help)
-V (--version)
-v (--verbose)   = debugging output


Note: -w and -c are required arguments.  -W and -C are optional.
 -W and -C are applied to domains listed on the queues - both FROM and TO. (sendmail)
 -W and -C are applied message not yet preproccessed. (qmail)
 This plugin uses the system mailq command (sendmail) or qmail-stat (qmail)
 to look at the queues. Mailq can usually only be accessed by root or
 a TrustedUser. You will have to set appropriate permissions for the plugin to work.
```

##### check_mrtg

Este comando también trabaja en local en la máquina que está ejecutando Nagios y permite monitorizar los ficheros de sucesos de MRTG; en concreto permite monitorizar cualquiera de los parámetros que se vuelcan sobre dichos ficheros como por ejemplo conexiones, carga del procesador, entrada, salida, etcétera. Permite establecer umbrales que si se superan generan notificaciones.

This plugin will check either the average or maximum value of one of the two variables recorded in an MRTG log file.

Usage:check_mrtg -F log_file -a \<AVG \| MAX\> -v variable -w warning -c critical \[-l label\] \[-u units\] \[-e expire_minutes\] \[-t timeout\] \[-v\]

##### check_mrtgtraf

Este comando permite comprobar el servicio UPS en un equipo remoto y establecer umbrales para, según el valor devuelto, disparar una advertencia, un error severo o nada.

This plugin will check the incoming/outgoing transfer rates of a router, switch, etc recorded in an MRTG log. If the newest log entry is older than \<expire_minutes\>, a WARNING status is returned. If either the incoming or outgoing rates exceed the \<icl\> or \<ocl\> thresholds (in Bytes/sec), a CRITICAL status results. If either of the rates exceed the \<iwl\> or \<owl\> thresholds (in Bytes/sec), a WARNING status results.

Usage check_mrtgtraf -F \<log_file\> -a \<AVG \| MAX\> -v \<variable\> -w \<warning_pair\>-c \<critical_pair\> \[-e expire_minutes\] \[-t timeout\] \[-v\]

Options: -h, –help

      Print detailed help screen

-V, –version

      Print version information

-F, –filename=STRING

      File to read log from

-e, –expires=INTEGER

      Minutes after which log expires

-a, –aggregation=(AVG\|MAX)

      Test average or maximum

-w, –warning

      Warning threshold pair <incoming>,<outgoing>

-c, –critical

      Critical threshold pair <incoming>,<outgoing>

Notes: - MRTG stands for Multi Router Traffic Grapher. It can be downloaded from

     http://ee-staff.ethz.ch/~oetiker/webtools/mrtg/mrtg.html

\- While MRTG can monitor things other than traffic rates, this

     plugin probably won't work with much else without modification.

\- The calculated i/o rates are a little off from what MRTG actually

     reports.  I'm not sure why this is right now, but will look into it
     for future enhancements of this plugin.

##### check_nagios

Este comando se ejecuta en la máquina que está ejecutando Nagios y permite comprobar que el archivo de sucesos del sistema de monitorización no sea más antiguo de lo que se especifique.

Usage:check_nagios -F \<status log file\> -e \<expire_minutes\> -C \<process_string\>

Options: -h, –help

      Print detailed help screen

-V, –version

      Print version information

-F, –filename=FILE

      Name of the log file to check

-e, –expires=INTEGER

      Minutes aging after which logfile is considered stale

-C, –command=STRING

      Substring to search for in process arguments

-v, –verbose

      Show details for command-line debugging (Nagios may truncate output)

Examples: check_nagios -e 5 -F /usr/local/nagios/var/status.log -C /usr/local/nagios/bin/nagios

##### negate

Este comando sirve para, en combinación con cualquiera de los otros plugins, negar su valor. Por ejemplo, el uso normal del comando check_ftp es que devuelve OK cuando el servicio esté funcionando y CRITICAL cuando no. Con este comando se invierten los valores. Es útil para cuando se desea tener notificación explícita de que algo está funcionando bien en lugar de cuando falla.

Usage:negate \[-t timeout\] \[-owcu STATE\] \[-s\] \<definition of wrapped plugin\>

##### check_nntp

Este comando establece conexiones NNTP contra un servidor remoto especificado para comprobar que el servicio de NEWS esté activo.

This plugin tests NNTP connections with the specified host (or unix socket).

Usage:check_nntp -H host -p port \[-w \<warning time\>\] \[-c \<critical time\>\] \[-s \<send string\>\] \[-e \<expect string\>\] \[-q \<quit string\>\]\[-m \<maximum bytes\>\] \[-d \<delay\>\] \[-t \<timeout seconds\>\] \[-r \<refuse state\>\] \[-M \<mismatch state\>\] \[-v\] \[-4\|-6\] \[-j\] \[-D \<days to cert expiry\>\] \[-S \<use SSL\>\] \[-E\]

##### check_nt

Este comando realiza peticiones a un equipo Windows NT/2000/XP remoto que esté ejecutando el servicio NSClient para comprobar parámetros locales a dicho equipo como por ejemplo uso de la CPU, de la memoria, del disco, etcétera.

##### check_ntp

Este comando ejecuta ntpdate para comprobar que el timestamp de la máquina local que ejecuta Nagios no difiere en más de lo especificado del timestamp de una máquina remota dado.

##### check_nwstat

Planificación, especificación, diseño y evaluación de redes Este comando realiza peticiones a un equipo Novell remoto que esté ejecutando el servicio MRTGEXT NLM para comprobar paráme tros locales a dicho equipo como por ejemplo uso de la CPU, de la memoria, del disco, etcétera.

##### check_oracle

Este comando permite comprobar el estado de un SGBD Oracle en un ordenador remoto así como el estado de los tablespaces, de bases de datos, de las caché, etcétera, de dicho servidor.

##### check_overcr

Este comando permite comprobar el estado de un servicio Over-CR ejecutándose en un sistema UNIX remoto. Realiza peticiones a este servicio para comprobar su estado.

##### check_pop

Este comando comprueba si el servicio POP de un equipo remoto está funcionando correctamente. Realiza peticiones para ello.

##### check_procs

Este comando funciona en la máquina donde se está ejecutando Nagios. Comprueba el número de procesos que se están ejecutando en la máquina y genera advertencias cuando este número sobrepasa el umbral especificado.

##### check_real

Este comando comprueba si el servicio REAL de un equipo remoto está funcionando correctamente. Realiza peticiones para ello.

##### check_rpc

Este comando comprueba si un servicio RPC remoto está registrado y funcionando correctamente. Utiliza para ello llamadas a rpcinfo.

##### check_sensors

Este comando funciona en la máquina local donde se ejecuta Nagios; necesita de paquetes adicionales instalados en el sistema de monitorización y su función es comprobar el estado del hardware de la máquina.

##### check_smtp

Este comando permite conocer el estado de un servicio SNMP de una máquina remota. Realiza conexiones a este servicio para averiguar la información necesaria.

##### check_snmp

Este comando permite conocer el estado de una máquina remota mediante la consulta a su agente SNMP. Utiliza para ello el protocolo SNMP en cualquiera de sus versiones 1, 2 ó 3.

##### check_ssh

Este comando permite controlar si el servicio SSH de una máquina remota está activo o no. Realiza peticiones a este servicio para obtener la información necesaria.

##### check_swap

Este comando funciona en local, en la máquina donde está instalado Nagios. Permite monitorizar el tamaño de la memoria de intercambio utilizada y generar advertencias o errores cuando este valor sobrepaso los umbrales establecidos.

##### check_tcp

Este comando permite realizar peticiones arbitrarias a conexiones (sockets) TCP contra sistemas remotos. Por tanto permite monitorizar cualquier servicio que utilice sockets TCP para recibir peticiones.

Definir como comando:

```
define command{
    command_name    check_tcp
    command_line    $USER1$/check_tcp -H '$HOSTADDRESS$' -p $ARG1$
}
```

```
define service{
    use                     generic-service
    host_name               nombre_host
    service_description     telnet
    check_command           check_tcp!23
}
```

##### check_time

Este comando permite comprobar si el servicio de hora (TIME) está funcionando en una máquina remota. Realiza conexiones a este servicio para obtener la información.

##### check_udp

Este comando permite realizar peticiones arbitrarias a conexiones (sockets) UDP contra sistemas remotos. Por tanto permite monitorizar cualquier servicio que utilice sockets UDP para recibir peticiones.

##### check_ups

Este comando permite monitorizar el estado del servicio UPS en máquinas remotas; para ello hace peticiones a este servicio. Necesita paquetes adicionales instalados en el sistema de monitorización.

##### check_users

Este comando permite conocer el número de usuarios conectados actualmente en el sistema local, en el que se está ejecutando Nagios. Genera advertencias y errores cuando el número supera el umbral fijado.

##### check_vsz

Este comando permite comprobar que el tamaño en memoria de un programa determinado no sea mayor de un límite fijado. Cuando se produzca el caso contrario se generarán advertencias y/o errores.

##### urlize

Este comando permite, usando con otro comando, que la salida de este último se pueda mostrar en la pantalla de un navegador en formato HTML como un enlace hipertexto navegable.

Usage:urlize \<url\> \<plugin\> \<arg1\> … \<argN\>

Options: -h, –help

      Print detailed help screen

-V, –version

      Print version information

Examples: Pay close attention to quoting to ensure that the shell passes the expected data to the plugin. For example, in:

urlize <http://example.com/> check_http -H example.com -r ‘two words’

      the shell will remove the single quotes and urlize will see:

urlize <http://example.com/> check_http -H example.com -r two words

      You probably want:

urlize <http://example.com/> “check_http -H example.com -r ‘two words’”

**Importante** Para que este comando funcione bien, deberemos tener configurado en el archivo *cgi.cfg* la siguiente directiva :

```
escape_html_tags=0
```

## Plugins SNMP

Caracteristicas generales

| Script detail page | Description | performance | Supported platforms |
|----|----|----|----|
| check_snmp_storage | checks storages (disks,swap, memory, etc…) | Yes | All MIB2 compliant |
| check_snmp_int | checks interface states, usage on hosts, switch, routers, etc | Yes | All MIB2 compliant |
| check_snmp_process | checks if process are running, the number that are running, memory and cpu used. | No | All MIB2 compliant |
| check_snmp_load | checks the load or the cpu of a machine | Yes | Linux, Windows, Cisco, AS400, HP Procurve, LinkProof, Blucoat, Nokia,Fortinet, Netscreen,HPUX. |
| check_snmp_vrrp | checks the interface state of vrrp cluster | No | Nokia IP (VRRP & Clustering), LinkProof, Alteon |
| check_snmp_cpfw | checks Checkpoint Firewall1 status | Yes | Checkpoint Firewall |
| check_snmp_mem | Checks memory and swap usage | Yes | Linux/Netsnmp, Cisco, HP Switch |
| check_snmp_win | Checks windows services | No | Windows |
| check_snmp_css | Checks css services state | No | CSS |
| check_snmp_env | Checks environemental status (fan, temp, power supply). | No | Cisco, Nokia, Bluecoat, IronPort, Foundry |
| check_snmp_nsbox | Checks nsbox vhost & diode status. | No | NetSecureOne Netbox |
| check_snmp_boostedge | Checks Boostedge services | No | Boostedge |
| check_snmp_linkproof_nhr | Checks linkproof NHR | No | Radware Linkproof |
