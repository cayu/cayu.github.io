---
title: "ILS (Internet Locator Server)"
date: 2010-03-08T15:33:05-03:00
---

Un servidor ILS (Internet Locator Server), también conocido como directorio, permite a NetMeeting obtener la dirección IP de una persona que ha iniciado sesión en ese directorio para establecer una llamada. No es más que una lista de personas que están utilizando NetMeeting y desean hacer públicos sus datos para que alguien les pueda localizar y llamar. Para que los datos aparezcan en un directorio se deben registrar en él, desde NetMeeting se puede configurar en el menú Herramientas\|Opciones, pestaña general, donde dice ‘Configuración de directorio’.

Al registrarse en un directorio es posible localizarse fácilmente, cuando se elige conversar con alguien que está listado en el directorio, NetMeeting se comunica con el directorio, obtiene su dirección IP y establece la llamada directamente, ahí acaba la misión del servidor ILS.

# Implementación

Se tomó una maquina virtual con un SUSE Linux Enterprise Server 10 SP1, en el cual se instaló el sistema base, y se compiló lo necesario para su funcionamiento.

Primero se definieron diferentes interfaces de red, una interfaz para la red general que utiliza el servicio ILS y otra interfaz para su administracion por SSH para el segmento de la red de tecnologia.

Paquetes necesarios para emprender la compilacion

- GCC

<!-- -->

- gdbm-dev

# Como funciona

El ILS es un servidor LDAP con valores retocados, para poder hacerlo funcionar bajo GNU/Linux deberemos bajar la version 2.0.7 de OpenLDAP \[0\], y el NDK \[1\] (NetMeeting Directory Kit).

Con lo cual obtendremos una version funcional de OpenLDAP con el schema preparado para NetMeeting.

![ils.png](/manuales/ils.png)

El OpenLDAP, deberemos compilarlo con las siguientes opciones, y de la siguiente forma

##### compilacion

```
./configure --enable-shell --enable-monitor --enable-debug
make depend
make
make install
```

##### perl -MCPAN -e shell

```
cpan shell -- CPAN exploration and modules installation (v1.58)
ReadLine support enabled

cpan> install Net::LDAP

... much output omitted ...

  /usr/bin/make install -- OK

cpan>
```

##### Estructura de archivos

- **netmeeting.perl** *Script en Perl para interfacear el LDAP de NetMeeting*
- **netmeeting.schema** *Schema para la estructura LDAP*
- **slapd.conf** *Configuracion para el servidor LDAP primario*
- **slapd2.conf** *Configuracion para el servidor LDAP secundario*
- **initialize** *Script para iniciar la base LDAP del ILS*
- **slapd.rc** *Script de inicio de /etc/init.d*
- **nmaddentry** *Perl script to add entries to the NetMeeting directory*
- **nmdirectory** *Perl/Tk script to query the NetMeeting directory*

# Levantando los servicios

Para arrancar el LDAP primario solo basta con ejecutar la siguiente linea de comando especificando el archivo de configuracion a usar

```
/usr/local/libexec/slapd -f /usr/local/etc/openldap/slapd.conf
```

Para ejecutar el servidor LDAP secundario tenemos que especificar el puerto en el cual escuchar ya que el debe diferir del servidor LDAP primario

```
/usr/local/libexec/slapd -h ldap://localhost:2345/ -f /usr/local/etc/openldap/slapd2.conf
```

Copy netmeeting.perl to the /usr/local/libexec directory, netmeeting.schema to the /usr/local/etc/openldap/schema directory, and copy both slapd.conf and slapd2.conf to the /usr/local/etc/openldap directory. Create the directory /usr/local/var/openldap-netmeeting to store the LDAP database, and make it world writable.

Ademas de esto hay que inicializar la base LDAP con algunos parametros dentro de ella :

```
ldapadd -H ldap://localhost:2345/ -x -D "cn=root,objectclass=rtperson" -w secret <<EOF
dn: objectclass=rtperson
objectclass: top
EOF
```

# Nota de ILS en Windows 2000

Si el ILS corre sobre un Windows 2000, por default este en vez de escuchar el servicio en el puerto 389 de LDAP, lo hace sobre el puerto 1002, entonces el NetMeeting intenta primero en el puerto 1002, pero si la conexion es rechazada entonces reintenta conectar al puerto 389 por default de LDAP.

Nota: Si surgiera la necesidad de instalar el servidor ILS dentro de un servidor que previamente esta corriendo LDAP, entonces probar configurar para que el LDAP correspondiente al ILS corra en el puerto 1002

\[0\] <ftp://ftp.openldap.org/pub/OpenLDAP/openldap-release/>

\[1\] <http://www.freesoft.org/software/NetMeeting/download>

# Archivos

*Archivos, scripts y demas necesarios para el correcto funcionamiento de ILS.*

## Configuracion

##### /usr/local/etc/openldap/slapd.conf

```java>
== /usr/local/etc/openldap/slapd2.conf ==

<code java>
include         /usr/local/etc/openldap/schema/netmeeting.schema
schemacheck     off

pidfile         /var/run/slapd2.pid

database        ldbm
suffix          "OBJECTCLASS=RTPERSON"
directory       /usr/local/var/openldap-ldbm
rootdn          "cn=root,objectclass=rtperson"
rootpw          secret
lastmod         on 
access to * by * write
```

## Inicializar la Base de Datos ILS

Carga los datos iniciales de la Base LDAP para el correcto registro del cliente NetMeeting

##### initialize

```bash>
===== Script de inicio para el servidor ILS =====

Para poder iniciar y detener el servicio ILS, se armo un script que detenia y comenzaba los servicios de red LDAP, con las convenciones y cosas del SuSE

== /etc/init.d/ils ==


<code bash>
#!/bin/sh
# description: Correr procesos ILS
# Sergio Cayuqueo <cayu [at] cayu [dt] com [dt] ar>
#

### BEGIN INIT INFO
# Provides:       ils
# Required-Start: 
# Required-Stop:
# Default-Start:  3 5
# Default-Stop:   0 1 6
# Description:    Internet Locator Server (ILS)
### END INIT INFO

 
# Source function library.
. /lib/lsb/init-functions
 
SLAPD_BIN=/usr/local/libexec/slapd
test -x $SLAPD_BIN || exit 5
 
SLAP1_CONF=/usr/local/etc/openldap/slapd.conf
SLAP2_CONF=/usr/local/etc/openldap/slapd2.conf

PIDFILE1=/var/run/slapd.pid
PIDFILE2=/var/run/slapd2.pid

test -x $SLAPD_BIN || { echo "$SLAPD_BIN no instalado";
        if [ "$1" = "stop" ]; then exit 0;
        else exit 5; fi; }

test -r $SLAP1_CONF || { echo "$SLAP1_CONF no existe";
        if [ "$1" = "stop" ]; then exit 0;
        else exit 6; fi; }

test -r $SLAP2_CONF || { echo "$SLAP2_CONF no existe";
        if [ "$1" = "stop" ]; then exit 0;
        else exit 6; fi; }


case "$1" in
  start)
        echo -n "Starting slapd1: "
        startproc -p $PIDFILE1 -f $SLAPD_BIN -f $SLAP1_CONF ; rc_status -v
        echo
        echo -n "Starting slapd2: "
        startproc -p $PIDFILE2 -f $SLAPD_BIN -h ldap://localhost:2345/ -f $SLAP2_CONF ; rc_status -v
        echo 
        ;;
  stop)
        echo -n "Shutting down ILS: "
        killproc -p $PIDFILE1 $SLAPD_BIN ; rc_status -v
        killproc -p $PIDFILE2 $SLAPD_BIN ; rc_status -v
        ;;
  status)
        echo -n "Checking for service ILS "
        ## Check status with checkproc(8), if process is running
        ## checkproc will return with exit status 0.
 
        # Estado de salida del comando:
        # 0 - servicio corriendo
        # 1 - servicio muerto pero en /var/run/ existe el archivo PID
        # 2 - servicio muerto pero en /var/lock/ existe el archivo lock
        # 3 - servicio sin correr
        #checkproc $SLAPD_BIN ; rc_status -v
        pidofproc -p $PIDFILE1 $SLAPD_BIN ; rc_status -v
        pidofproc -p $PIDFILE2 $SLAPD_BIN ; rc_status -v
        ;;
  restart)
        $0 stop
        $0 start
        ;;
  *)
        echo $"Usage: $0 {start|stop|status|restart}"
        exit 1
esac
 
exit 0
```

Notese que esta \$“Usage, los locales del sistema se encargan de traducirlo a Uso y el \$0 es la variable que contiene el nombre del script en cuestion.

Una vez que copiamos nuestro script a /etc/init.d ejecutamos el siguiente comando para configurarlo como servicio al inicio ***innserv /etc/init.d/ils*** y para borrarlo ***innserv -r /etc/init.d/ils***

**Sobre la ejecucion del script**

|             | Opciones                             | Descripcion |     |
|-------------|--------------------------------------|-------------|-----|
| **start**   | *Incia el servicio*                  |             |     |
| **stop**    | *Detiene el servicio*                |             |     |
| **status**  | *Nos muestra el estado de ejecucion* |             |     |
| **restart** | *Reinicia el servicio*               |             |     |

Niveles de ejecucion por defecto de inicio del script

|       |                                                       |
|-------|-------------------------------------------------------|
| **3** | *Modo multiusuario con red*                           |
| **5** | *Modo multiusuario completo con red y gestor grafico* |

#### TODO

FIXME TODO

- Agregar datos de emergecia en caso de caida

<!-- -->

- Para un futuro estaria bueno armar un RPM o un DEB

## Archivos

Scripts y configuracion

[ils0.1.tgz](/manuales/ils0.1.tgz)
