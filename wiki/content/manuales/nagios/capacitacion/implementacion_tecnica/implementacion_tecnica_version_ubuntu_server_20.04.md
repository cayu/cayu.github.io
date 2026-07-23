---
title: "Implementación Técnica de Nagios"
date: 2020-08-26T12:42:43-03:00
---

## Instalación de Nagios en Ubuntu Server 20.04

### Dependencias para compilación y puesta en marcha

<table>
<thead>
<tr>
<th>Sistema Operativo</th>
<th>Ubuntu 20.04</th>
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

## Instalación de Nagios orientada a la distribución

Es útil pensar en utilizar varios paquetes de plugins disponibles en la distribución para poder facilitar el proceso de actualizaciones, igualmente es recomendable los componentes base de Nagios compilarlos y dejar separados los plugins personalizados para poder dejarlos independientes de las actualizaciones que apliquemos a los paquetes de la distribución.

### Caso concreto

| Sistema Operativo | Ubuntu 20.04 |
|-------------------|--------------|
| Version de Nagios | 4.4.6        |

./configure

```
./configure  --prefix=/usr/local/nagios --enable-nerd --enable-corewindow --enable-event-broker --with-nagios-user=nagios  --with-nagios-group=nagios   --with-command-user=nagios   --with-command-group=nagios

...

*** Configuration summary for nagios 4.4.6 2020-04-28 ***:

 General Options:
 -------------------------
        Nagios executable:  nagios
        Nagios user/group:  nagios,nagios
       Command user/group:  nagios,nagios
             Event Broker:  yes
        Install ${prefix}:  /usr/local/nagios
    Install ${includedir}:  /usr/local/nagios/include/nagios
                Lock file:  /run/nagios.lock
   Check result directory:  /usr/local/nagios/var/spool/checkresults
           Init directory:  /lib/systemd/system
  Apache conf.d directory:  /etc/httpd/conf.d
             Mail program:  /usr/sbin/sendmail
                  Host OS:  linux-gnu
          IOBroker Method:  epoll

 Web Interface Options:
 ------------------------
                 HTML URL:  http://localhost/nagios/
                  CGI URL:  http://localhost/nagios/cgi-bin/
 Traceroute (used by WAP):  


Review the options above for accuracy.  If they look okay,
type 'make all' to compile the main program and CGIs.
```

make install-base

```
make[1]: Entering directory '/root/nagios-4.4.6/base'
/usr/bin/install -c -m 775 -o nagios -g nagios -d /usr/local/nagios/bin
/usr/bin/install -c -s -m 774 -o nagios -g nagios nagios /usr/local/nagios/bin
/usr/bin/install -c -s -m 774 -o nagios -g nagios nagiostats /usr/local/nagios/bin
make[1]: Leaving directory '/root/nagios-4.4.6/base'
```

make install-init

```
/usr/bin/install -c -m 755 -d -o root -g root /lib/systemd/system
/usr/bin/install -c -m 755 -o root -g root startup/default-service /lib/systemd/system/nagios.service
```

make install-commandmode

```
/usr/bin/install -c -m 775 -o nagios -g nagios -d /usr/local/nagios/var/rw
chmod g+s /usr/local/nagios/var/rw

*** External command directory configured ***
```

Paquetes de plugins a instalar junto con sus dependencias :

- **monitoring-plugins** - Plugins for nagios compatible monitoring systems (metapackage)
- **monitoring-plugins-basic** - Plugins for nagios compatible monitoring systems (basic)
- **monitoring-plugins-btrfs** - btrfs plugin for nagios compatible monitoring systems
- **monitoring-plugins-common** - Common files for plugins for nagios compatible monitoring
- **monitoring-plugins-standard** - Plugins for nagios compatible monitoring systems (standard)
- **logaricheck** - Nagios plugin to check disk, memory and swap usage
- **nagios-check-xmppng** - monitoring plugin to check XMPP servers
- **nagios-nrpe-plugin** - Nagios Remote Plugin Executor Plugin
- **nagios-plugin-check-multi** - run nagios checks as a group
- **nagios-plugins-contrib** - Plugins for nagios compatible monitoring systems
- **nagios-plugins-rabbitmq** - Set of Nagios checks useful for monitoring a RabbitMQ server
- **python3-nagiosplugin** - Python class library for writing Nagios (Icinga) plugins (Python 3)
- **python3-pynag** - Python 3 Modules for Nagios plugins and configuration
- **libmonitoring-availability-perl** - Calculate Availability Data from Nagios and Icinga Logfiles
- **libmonitoring-livestatus-perl** - Perl API for check_mk livestatus to access runtime
- **libmonitoring-plugin-perl** - family of perl modules to streamline writing Monitoring plugins
- **nagios-snmp-plugins**

```
apt-get install monitoring-plugins monitoring-plugins-basic monitoring-plugins-btrfs monitoring-plugins-common monitoring-plugins-standard logaricheck nagios-check-xmppng nagios-nrpe-plugin nagios-plugin-check-multi nagios-plugins-contrib nagios-plugins-rabbitmq python3-nagiosplugin python3-pynag libmonitoring-availability-perl libmonitoring-livestatus-perl libmonitoring-plugin-perl nagios-snmp-plugins
```

Paquetes extra para agregados de Nagios :

- rrdtool,librrds-perl
- Cliente oracle
  - oracle-instantclient-basic_10.2.0.5-2_amd64.deb
  - oracle-instantclient-sqlplus_10.2.0.5-2_amd64.deb
- php7.4-cli
- php7.4-curl
- php7.4-gd
- ntpstat
- ntpdate
- nmap
- xmlstarlet
- libmail-sendmail-perl
- php7.4-snmp

```
apt-get install rrdtool librrds-perl php7.4-cli php7.4-curl php7.4-gd ntpstat ntpdate nmap xmlstarlet libmail-sendmail-perl php7.4-snmp
```

## Configuración

Ahora nos referiremos a la configuración de los elementos instalados para su posterior *articulación* y funcionamiento en *conjunto*.

### Notas extras

Para el cliente de Oracle debemos agregar a nuestro LD_LIBRARY_PATH , la siguiente ruta :

```
/usr/lib/oracle/10.2.0.5/client64/lib
```
