---
title: "Desarrollo de plugins de Nagios"
date: 2016-07-27T12:12:42-03:00
---

### Programando plugins

Desarrollar plugins de chequeos para Nagios es extremadamente flexible, ya que no dependemos del lenguaje de programación debido a que Nagios toma la salida resultante de su ejecución.

Deberemos conocer bien lo que queremos chequear y conocer los indicadores que nos mostraran si deberemos expresarlos como un OK, un WARNING o un CRITICAL.

Luego deberemos reflejar esos estados en su código de retorno o Exit status, dependiendo del código del mismo Nagios entenderá que debe mostrar.

| Exit status | Estado de Servicio | Estado de Host | Descripcion |
|----|----|----|----|
| 0 | OK | UP | El plugin es capaz de verificar el servicio y que parece estar funcionando correctamente |
| 1 | WARNING | UP/DOWN/UNREACHABLE | El plugin es capaz de verificar el servicio, pero que parecía estar por encima de un umbral de “advertencia” o parece no estar funcionando correctamente |
| 2 | CRITICAL | DOWN/UNREACHABLE | El plugin detecta que o bien el servicio no funciona o que está por encima de un umbral “crítico” |
| 3 | UNKNOWN | DOWN/UNREACHABLE | Argumentos de línea de comandos no válida o fallas internas del plugin (por ejemplo error en un socket o dns) que le impiden realizar las operaciones especificadas |

**Añadir nuestro plugin**

Para añadir nuestro plugin a Nagios

- Deberemos copiar nuestro ejecutable (ya sea script o binario) al directorio */usr/local/nagios/libexec* con permisos de ejecución.
- Crearle una entrada dentro del archivo de configuración de Comandos.
- Crear un Servicio y asignarle ese comando para que se encargue de dicho chequeo.

**Notas sobre el desarrollo de plugins**

- No ejecutar comandos del sistema, sin especificar su ruta de acceso completa.
  - Utilice spopen () si deben ser ejecutados comando externos en su plugin en C.
  - Ver los principales ejemplos de plugin para ver cómo se hace esto.
- No haga archivos temporales a menos que sea absolutamente necesario.
  - Si se necesitan archivos temporales, asegúrese de que el plugin y elimine el archivo temporal, cuando finalice.
- No se deje engañar por enlaces simbólicos
  - Si el plug-in abre todos los archivos, tomar medidas para asegurarse de que no están siguiendo un enlace simbólico a otro lugar en el sistema.
- Validar todas las entradas
- Usar rutinas en utils.c utils.pm o escribir más según sea necesario

**Referencias sobre el desarrollo de plugins**

<http://nagios.sourceforge.net/docs/3_0/pluginapi.html>

<http://nagiosplug.sourceforge.net/developer-guidelines.html>

#### Ejemplo de consulta de un puerto TCP con Perl por medio de IO::Socket::INET

**check_port.pl \$HOSTADDRESS\$ \$ARG1\$**

```perl>
new(
         PeerAddr => $IP,
         PeerPort => $PORT,
         Proto => 'tcp',
         );
         $sock or $STATUS = "2";

if ( $STATUS eq 0 ) {
   print "El puerto $ARGV[1] esta OK";
   exit 0;
} else {
   print "No puedo abrir el puerto $ARGV[1] WARNING";
   exit 2;
   die;
} 
```

### Ejemplo de plugins SNMP

##### Plugin consultando SNMP

Dado el caso que queramos obtener información interna de determinado Host y necesitemos que este disponible para consultarla por SNMP, podemos incluir dicha informacion. Para ellos deberemos incluir diferentes directivas de configuracion en el archivo *snmpd.conf*, y haremos uso de la tabla externa del objeto UCD. La *Tabla externa UCD* es una tabla extensible de comandos de la cual obtendremos su código de resultado de ejecución y su salida.

**/etc/snmp/snmpd.conf**

```
syslocation Unknown (configure /etc/snmp/snmpd.local.conf)
syscontact Root <root@localhost> (configure /etc/snmp/snmpd.local.conf)

exec comando1 /bin/comando1
exec comando2 /bin/comando2
exec comando3 /bin/comando3
exec comando3 /bin/comando4

rouser consultorsnmp auth
#createUser consultorsnmp MD5 consultorsnmp123
```

Luego podemos ver los resultados obtenidos realizando una consulta SNMP a *UCD-SNMP-MIB::extTable* o *.1.3.6.1.4.1.2021.8*, obteniendo resultados similares a :

**snmpwalk -v 3 -l authNoPriv -a MD5 -u consultorsnmp -A consultorsnmp123 127.0.0.1 UCD-SNMP-MIB::extTable**\*

```
UCD-SNMP-MIB::extIndex.1 = INTEGER: 1
UCD-SNMP-MIB::extIndex.2 = INTEGER: 2
UCD-SNMP-MIB::extIndex.3 = INTEGER: 3
UCD-SNMP-MIB::extIndex.4 = INTEGER: 4
UCD-SNMP-MIB::extNames.1 = STRING: comando1
UCD-SNMP-MIB::extNames.2 = STRING: comando2
UCD-SNMP-MIB::extNames.3 = STRING: comando3
UCD-SNMP-MIB::extNames.4 = STRING: comando4
UCD-SNMP-MIB::extCommand.1 = STRING: /bin/comando1
UCD-SNMP-MIB::extCommand.2 = STRING: /bin/comando2
UCD-SNMP-MIB::extCommand.3 = STRING: /bin/comando3
UCD-SNMP-MIB::extCommand.4 = STRING: /bin/comando4
UCD-SNMP-MIB::extResult.1 = INTEGER: 0
UCD-SNMP-MIB::extResult.2 = INTEGER: 0
UCD-SNMP-MIB::extResult.3 = INTEGER: 0
UCD-SNMP-MIB::extResult.4 = INTEGER: 0
UCD-SNMP-MIB::extOutput.1 = STRING: salida-comando
UCD-SNMP-MIB::extOutput.2 = STRING: salida-comando
UCD-SNMP-MIB::extOutput.3 = STRING: salida-comando
UCD-SNMP-MIB::extOutput.4 = STRING: salida-comando
UCD-SNMP-MIB::extErrFix.1 = INTEGER: 0
UCD-SNMP-MIB::extErrFix.2 = INTEGER: 0
UCD-SNMP-MIB::extErrFix.3 = INTEGER: 0
UCD-SNMP-MIB::extErrFix.4 = INTEGER: 0
UCD-SNMP-MIB::extErrFixCmd.1 = STRING:
UCD-SNMP-MIB::extErrFixCmd.2 = STRING:
UCD-SNMP-MIB::extErrFixCmd.3 = STRING:
UCD-SNMP-MIB::extErrFixCmd.4 = STRING:
```

Ramas SNMP :

- UCD-SNMP-MIB::extNames
  - En esta rama obtendremos el nombre que le hemos asignado a dicho comando
- UCD-SNMP-MIB::extCommand
  - Esta rama nos devolvera la ruta completa al ejecutable del comando
- UCD-SNMP-MIB::extResult
  - Nos devolvera el resultado de la ejecucion del comando fue exitosa o no, devolviendonos su *exit status*
- UCD-SNMP-MIB::extOutput
  - Aqui obtendremos la salida del comando con el string o expresion que necesitemos conocer

**Referencias sobre consultas SNMP a la tabla UCD**

<http://net-snmp.sourceforge.net/docs/mibs/ucdavis.html>

#### Ejemplo de consulta SNMP con Perl por medio de Net::SNMP

Ejemplo simple de como consultar una variable SNMPv1.

```perl>
session(
    -hostname  => shift || '127.0.0.1',
    -community => shift || 'public',
);

if (!defined $session) {
    printf "ERROR: %s.\n", $error;
    exit 1;
}

my $result = $session->get_request(-varbindlist => [ $OID_sysUpTime ],);

if (!defined $result) {
    printf "ERROR: %s.\n", $session->error();
    $session->close();
    exit 1;
}

printf "The sysUpTime for host '%s' is %s.\n",
$session->hostname(), $result->{$OID_sysUpTime};

$session->close();

exit 0;
```

Ejemplo simple de como consultar una variable SNMPv3, igualmente se pueden consultar mas de una variable por sesion. En este caso estamos consultado la variable extOutput.1 para ver la salida del primer comando.

```perl>
session(
                -username       =>  $snmpv3_username,
                -authpassword   =>  $snmpv3_password,
                -authprotocol   =>  $snmpv3_authprotocol,
                -hostname       =>  $hostname,
                -version        =>  $version,
                -timeout        =>  $timeout,
        );
        if ($s){
        } else {
            print "CRITICAL - El agente no responde, SNMP v3 ($e)\n";
            exit(2);
        }
    $s->get_request($oid);
        foreach ($s->var_bind_names()) {
            $oid_consulta   = $s->var_bind_list()->{$_};
        }
$s->close();
print $oid_consulta;
```

##### Plugin ejecutando comando por SNMP

snmpd.conf

```
exec .1.3.6.1.4.1.2021.555.1 cat /bin/cat /backup/export_full/export_full.exit_status
exec .1.3.6.1.4.1.2021.555.2 cat /bin/cat /backup/export_dos/export_dos.exit_status
exec .1.3.6.1.4.1.2021.555.3 cat /bin/cat /backup/export_full_DB/export_full_DB.exit_status
exec .1.3.6.1.4.1.2021.555.4 cat /bin/cat /backup/export_tres/exportTRES.exit_status
```

```perl
#!/usr/bin/perl
use Net::SNMP;
$oid = $ARGV[1];

$snmpv3_username = "nagios";    # SNMPv3 username
$snmpv3_password = "claveNagios"; # SNMPv3 password
$snmpv3_authprotocol = "md5";          # SNMPv3 hash algorithm (md5 / sha)
$snmpv3_privpassword = "";             # SNMPv3 hash algorithm (md5 / sha)
$snmpv3_privprotocol = "des";          # SNMPv3 encryption protocol (des / aes / aes128)
$version = "3";
$timeout = "2";
$hostname = $ARGV[0];
# Crear la sesion SNMP
        ($s, $e) = Net::SNMP->session(
                -username       =>  $snmpv3_username,
                -authpassword   =>  $snmpv3_password,
                -authprotocol   =>  $snmpv3_authprotocol,
                -hostname       =>  $hostname,
                -version        =>  $version,
                -timeout        =>  $timeout,
        );
        if ($s){
        } else {
            print "CRITICAL - El agente no responde, SNMP v3 ($e)\n";
            exit(2);
        }
    $s->get_request($oid);
        foreach ($s->var_bind_names()) {
            $oid_consulta   = $s->var_bind_list()->{$_};
        }
$s->close();

if($oid_consulta == 0) {
    print "OK - El exit status es ".$oid_consulta."\n";
    exit 0;
} else {
    print "CRITICAL - El exit status es ".$oid_consulta."\n";
    exit 2;
}
```

Configuración de comando en Nagios

```
define service {
	host_name                      	srvblabla
	service_description            	Control de exit status de backup
	use                            	linux-service
	check_command                  	check_snmp_cat_file_exit_status!.1.3.6.1.4.1.2021.555.1.101.1
	check_interval                 	240
	notification_interval          	300
}
```

### Ejemplos de scripts generales

##### check_heartbeat

Script simple para chequear el estado de HeartBeat y sus nodos, muy útil por cierto

\<file bash check_heartbeat\> \#!/bin/bash \# Author: Emmanuel Bretelle \# Date: 12/03/2010 \# Description: Retrieve Linux HA cluster status using cl_status \# Based on <http://www.randombugs.com/linux/howto-monitor-linux-heartbeat-snmp.html> \# \# Autor: Stanila Constantin Adrian \# Date: 20/03/2009 \# Description: Check the number of active heartbeats \# <http://www.randombugs.com>

\# Get program path REVISION=1.3 PROGNAME=\`/bin/basename \$0\` PROGPATH=\`echo \$0 \| /bin/sed -e ‘s,\[\\/\]\[^\\/\]\[^\\/\]\*\$,,’\`

NODE_NAME=\`uname -n\` CL_ST=‘/usr/bin/cl_status’

\#nagios error codes \#. \$PROGPATH/utils.sh OK=0 WARNING=1 CRITICAL=2 UNKNOWN=3

usage () {

      echo "\

Nagios plugin to heartbeat.

Usage:

    $PROGNAME 
    $PROGNAME [--help | -h]
    $PROGNAME [--version | -v]

Options:

    --help -l Print this help information
    --version -v  Print version of plugin

” }

help () {

      print_revision $PROGNAME $REVISION
      echo; usage; echo; support

}

while test -n “\$1” do

    case "$1" in
      --help | -h)
        help
        exit $STATE_OK;;
      --version | -v)
        print_revision $PROGNAME $REVISION
        exit $STATE_OK;;

\# -H) \# shift \# HOST=\$1;; \# -C) \# shift \# COMMUNITY=\$1;;

      *)
        echo "Heartbeat UNKNOWN: Wrong command usage"; exit $UNKNOWN;;
    esac
    shift

done

\$CL_ST hbstatus \> /dev/null res=\$? if \[ \$res -ne 0 \] then

    echo "Heartbeat CRITICAL: Heartbeat is not running on this node"
    exit $CRITICAL

fi

declare -i I=0 declare -i A=0 NODES=\`\$CL_ST listnodes\`

for node in \$NODES do

    status=`$CL_ST nodestatus $node`
    let I=$I+1
    if [ $status == "active" ]
    then
      let A=$A+1
    fi

done

if \[ \$A -eq 0 \] then

    echo "Heartbeat CRITICAL: $A/$I"
    exit $CRITICAL

elif \[ \$A -ne \$I \] then

    echo "Heartbeat WARNING: $A/$I"
    exit $WARNING

else

    echo "Heartbeat OK: $A/$I"
    exit $OK

fi \</code\>

```
define command {
 command_name    check_ha_by_ssh
 command_line    $USER1$/check_by_ssh -l root -H $HOSTADDRESS$ -C "/usr/local/sbin/check_heartbeat.sh"
}
</file>

== check_systemimager ==

Script para chequear que nuestras imágenes estén actualizadas a la fecha

<file php check_systemimager>
#!/usr/bin/php -q
# Sergio Cayuqueo <cayu@cayu.com.ar>
# http://cayu.com.ar
<?php
$lista_imagenes = shell_exec("si_lsimage --verbose|grep Image");
$lista_imagenes = preg_split("/[\n]+/",$lista_imagenes);
$fecha_actual = date('Y.m.d');
foreach($lista_imagenes as $imagen) {
    if(strlen($imagen)>0) {
	if(@!$i) {
	    $i=1;
	}
	$imagen = preg_split("/[\s]+/",$imagen);
	$imagenes[$i]['nombre'] = $imagen[2];
	$imagenes[$i]['actualizada'] = $imagen[4];
	$imagenes[$i]['ip'] = $imagen[8];
	if($imagen[4] == $fecha_actual) {
	    $imagenes[$i]['estado'] = "ok" ;
	} else {
	    $imagenes[$i]['estado'] = "critical" ;
            $critical=1;
	}
	$i++;
    }
}

if(@$critical) {
    $head = "CRITICAL - Hubo un desfasaje en una o mas imagenes\n";
    $exit = 2;
} else {
    $head = "OK - Todas las imagenes actualizadas a la fecha\n";
    $exit = 0;
}
print $head;
foreach($imagenes as $imagen) {
    if(strlen($imagen['nombre'])<9) {
	$tab = "\t\t";
    } else {
	$tab = "\t";
    }
    if($imagen['estado'] == "ok") {
	print "OK - ".$imagen['nombre']." ".$tab.$imagen['ip']."\n";
    } else {
        print "CRITICAL - ".$imagen['nombre']."   ".$tab.$imagen['ip']." \t actualizado a : ".$imagen['actualizada']."\n";
    }
}
exit($exit);
?>
</file>


<code>
define command {
 command_name    check_si_by_ssh
 command_line    $USER1$/check_by_ssh -l root -H $HOSTADDRESS$ -C "/usr/local/sbin/check_systemimager.php"
}
```
