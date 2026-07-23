---
title: "TIPS y recomendaciones"
date: 2015-10-22T11:49:42-03:00
---

**Sobre performance**

* Compilar Nagios y NDO con optimizaciones para nuestro procesador
http://en.gentoo-wiki.com/wiki/Safe_Cflags
* Ver los tips de la documentacion oficial
http://nagios.sourceforge.net/docs/3_0/tuning.html
* Modificar opciones de buffer y cache de MySQL
* Establecer los valores que necesitemos de timeouts de Nagios
* Ubicar el archivo de estado de Nagios, sus archivos temporales en el sistema de archivos de memoria compartida
* nagios.cfg : status_file=/dev/shm/status.dat
* nagios.cfg : temp_file=/dev/shm/nagios.tmp
* nagios.cfg : temp_path=/dev/shm

*Nagios utiliza cada vez que necesita ejecutar un check dos forks en vez de uno, esto le trae entre otros beneficios el aislamiento del proceso hijo. Sin embargo la utilización de CPU se incrementa necesariamente. Deshabilitando esta opción puede incrementarce la performance en instalaciones grandes.*

Configuración por defecto:

      child_processes_fork_twice=1

Configuración para generar menos forks:

      child_processes_fork_twice=0

**Midiendo la performance de Nagios**

La utilidad **nagiostats** permite obtener información variada sobre la ejecución de Nagios que puede ser muy útil para medir la performance de monitoreo. Puede obtener información, ya sea en formato legible en variables compatibles con RRD+PNP / MRTG. Esto nos sirve para informarnos efectivamente de las frecuencias de monitoreo y saber si estas son correctas y si la información obtenida es actual o tiene algún desfasaje.

<img src="/manuales/nagios/nagiostats.png" class="align-right" />

<img src="/manuales/nagios/nagiostats2.jpg" class="align-right" />

```
Program Running Time:                   0d 5h 20m 39s
Nagios PID:                             10119
Used/High/Total Command Buffers:        0 / 0 / 64
Used/High/Total Check Result Buffers:   0 / 7 / 512

Total Services:                         95
Services Checked:                       94
Services Scheduled:                     91
Services Actively Checked:              94
Services Passively Checked:             1
Total Service State Change:             0.000 / 78.950 / 1.026 %
Active Service Latency:                 0.000 / 4.272 / 0.561 sec
Active Service Execution Time:          0.000 / 60.007 / 2.066 sec
Active Service State Change:            0.000 / 78.950 / 1.037 %
Active Services Last 1/5/15/60 min:     4 / 68 / 91 / 91
Passive Service State Change:           0.000 / 0.000 / 0.000 %
Passive Services Last 1/5/15/60 min:    0 / 0 / 0 / 0
Services Ok/Warn/Unk/Crit:              58 / 16 / 0 / 21
Services Flapping:                      1
Services In Downtime:                   0

Total Hosts:                            24
Hosts Checked:                          24
Hosts Scheduled:                        24
Hosts Actively Checked:                 24
Host Passively Checked:                 0

Total Host State Change:                0.000 / 9.210 / 0.384 %
Active Host Latency:                    0.000 / 0.446 / 0.219 sec
Active Host Execution Time:             1.019 / 10.034 / 2.764 sec
Active Host State Change:               0.000 / 9.210 / 0.384 %
Active Hosts Last 1/5/15/60 min:        5 / 22 / 24 / 24
Passive Host State Change:              0.000 / 0.000 / 0.000 %
Passive Hosts Last 1/5/15/60 min:       0 / 0 / 0 / 0
Hosts Up/Down/Unreach:                  18 / 4 / 2
Hosts Flapping:                         0
Hosts In Downtime:                      0

Active Host Checks Last 1/5/15 min:     9 / 52 / 164
   Scheduled:                           4 / 23 / 75
   On-demand:                           3 / 23 / 69
   Cached:                              2 / 6 / 20
Passive Host Checks Last 1/5/15 min:    0 / 0 / 0
Active Service Checks Last 1/5/15 min:  9 / 80 / 244
   Scheduled:                           9 / 80 / 244
   On-demand:                           0 / 0 / 0
   Cached:                              0 / 0 / 0
Passive Service Checks Last 1/5/15 min: 0 / 0 / 0
External Commands Last 1/5/15 min:      0 / 0 / 0
```

**Flap Detection**

Flap detection es un mecanismo mediante el cual Nagios induce que un servicio esta cambiando de estado continuamente sin entrar en un régimen permanente. El procedimiento del que se vale flap detection para detectar que un servicio o host esta en ese estado (“flapping”), es tomar los últimos 21 resultados de los checks y verificar cuantos cambios de estado hay en los mismos. Analizando más en detalle, dentro de 21 resultados hay 20 posibles cambios de estado. Suponiendo que dentro de estos 20 cambios de estado posibles, hubo 10 cambios de estado reales, la relación daría como resultado 10/20=0,50, es decir, 50%. Mediante dos parámetros se configuran los umbrales para que el mecanismo considere que el servicio o host se encuentra en ese estado.

Es interesante entender que el concepto de “flapping” se puede asignar por servicio ya sea en el template utilizado o en la definición de un servicio en particular. Igualmente es conveniente dejar la configuración general en su estado original y solo alterar la configuración particular del servicio que presenta problemas de esta índole.

Otra consideración importante es que se pueden quitar estados a interpretarse como cambio de estado, por ejemplo el estado “UNKNOWN” podría considerarse como que no hubo cambio respecto del estado anterior. Para ello hay que utilizar la directiva flap_detection_options dentro de la configuración del servicio o host.

**Sobre los flags de compilacion**

Ejemplo para un Pentium Dual E2160 o un Intel Quad Core

```bash>
Los incluimos de la siguiente manera en el proceso de compilacion de Nagios

<code>
CHOST="i686-pc-linux-gnu" CFLAGS="-march=prescott -O2 -pipe -fomit-frame-pointer" CXXFLAGS="${CFLAGS}" ./configure
```

La misma configuracion es valida para compilar MySQL (ej 5.1.37)

```
CHOST="i686-pc-linux-gnu" CFLAGS="-march=prescott -O2 -pipe -fomit-frame-pointer" CXXFLAGS="${CFLAGS}" ./configure --with-big-tables --with-plugins=partition,myisam,innobase
```

**NDO**

Cuando la base de datos esta cargada con una gran cantidad de registros, el NDO comienza a tener un comportamiento erratico, y los datos reflejados no son los correctos, por lo tanto cada cierto tiempo hay que purgar algunas tablas, ese tiempo sera dependiendo de la cantidad de objetos a monitorear.

Podemos crear un script que realize dicha tarea

```bash>
Para luego incluirlo en el **crontab**, por ejemplo si tenemos alrededor de mas de 600 hosts y alrededor de 2000 servicios o mas, chequeando ambos en intervalos de ente 1 y 5 minutos, podemos establecer su ejecucion en 15 minutos.

<code>
0,15,30,45 * * * root   /usr/local/nagios/sbin/truncar_db.sh
```

Igualmente en las opciones de configuracion del daemon ndo2db tenemos opciones referidas a esto, aunque igualmente dependeremos del script a realizar ya que el NDO en si mismo puede fallar. La configuracion se refiere a valores en minutos.

```
# Keep timed events for 24 hours
#max_timedevents_age=1440
max_timedevents_age=120
# Keep system commands for 1 week
#max_systemcommands_age=10080
max_systemcommands_age=1
# Keep service checks for 1 week
#max_servicechecks_age=10080
max_servicechecks_age=240
# Keep host checks for 1 week
#max_hostchecks_age=10080
max_hostchecks_age=240
# Keep event handlers for 31 days
#max_eventhandlers_age=44640
max_eventhandlers_age=60
```

Reparar tabla de NDO : En caso de que una tabla se corrompa, deberemos bajar el servicio NDO para que nagios no siga escribiendo registros y ejecutar la siguiente orden desde el shell.

```
mysqlcheck –-auto-repair nagios nagios_servicechecks;
```

**Retoques al codigo fuente**

Para que cuando elegimos exportar los informes de disponibiliad en formato CSV, no nos aparesca en la ventana del navegador y en cambio nos abra un cuadro de dialogo para elegir guardar o abrir con un programa externo, deberemos modificar los encabezados HTTP que imprime Nagios en dicho momento.

**cgi/avail.c**

```c>
Lo cambiaremos como :

<code c>
        if(output_format==HTML_OUTPUT)
                printf("Content-type: text/html\r\n\r\n");
        else{
                printf("Content-Disposition: attachment;filename=informe_mensual-nagios.csv
                        Content-type:application/csv\r\n\r\n");
                return;
                }
```

#### Scripts útiles

A continuacion se detallan scripts que pueden ser de utilidad para el dia a dia con Nagios

**Volcado de estado actual** : *Haciendo uso de la clase StatusLog disponible en CPAN podemo parsear el contenido del archivo status.dat de Nagios, para luego exportar su contenido*

```perl>
new(
Filename => "/dev/shm/status.dat",
Version => 3.0
);
print("Content-type: text/xml\n\n");
print("<?xml version='1.0'?>");
print("<status>\n");
foreach my $host ($log->list_hosts()) {
        print("<host>\n<name>$host</name>\n<services>");
        foreach my $serv ($log->list_services_on_host($host)) {
                print ("<service>\n");
                print (ref $serv);
                my $st  = $log->service($host, $serv);
                foreach $tag ($st->list_tags()) {
                        print("<$tag>$$st{$tag}</$tag>\n");
                }
                print ("</service>\n");
        }
        print("</services>\n</host>\n")
}
print("</status>");
```

Recordemos que para instalar modulo de CPAN deberemos ejecutar

```
# perl -MCPAN -e shell
cpan> install MODULO::NOMBRE
Ejemplo para este caso
cpan> install Nagios::StatusLog
```

Ejemplo de uso de Nagios::Report

```perl>
new(q<local_cgi nagios_web_server nagios_user>, [ '24x7' ], 'thismonth')
    or die "Can't construct Nagios::Report object." ;
my @these_fields = qw(
        HOST_NAME
        PERCENT_TOTAL_TIME_UP
  ) ;
$x->mkreport(
\@these_fields,
                            # All records
                            #   sub { 1 },
                            # All records whose HOST_NAME starts with 'Alb'
                            #   sub { my %F = @_; my $h = $F{HOST_NAME}; $h =~ /^Alb/ },
                            # Regrettably, this is _NOT_ the same since
                            # @_ can't be used as a hash.
                            #   sub { $_{HOST_NAME} =~ /^Alb/ }
                            # All records with an up time percent < 98%
sub {
    my %F = @_;
    my $u = $F{PERCENT_TOTAL_TIME_UP};
    $u =~ s/%//;
    $u > 0
},
# Sort order
&comp( alpha => 0, ascend => 0, fields => [ qw(TOTAL_TIME_DOWN TOTAL_TIME_UNREACHABLE) ]),
# Sorts descending by max of TOTAL_TIME_DOWN and TOTAL_TIME_UNREACHABLE
                            # DIY sorters remember that $a and $b _must_ be in Nagios::Report package.
                            # eg by TOTAL_DOWN_TIME descending.
                            #   sub { my %f = @_ ;
                            #         package Nagios::Report;
                            #         $b->[$f{TOTAL_TIME_DOWN}] <=> $a->[$f{TOTAL_TIME_DOWN}]
                            #        },
                            # Same as
                            #  &comp(alpha => 0, ascend => 0, fields => ['TOTAL_TIME_DOWN'])
                            # Same but harder,
                            #  sub { package Nagios::Report; $b->[16] <=> $a->[16] },
                            # Optional callback to add or mangle fields.
                            # Add 2 fields for downtime vals in hours minutes and secs.
sub {
    $F = shift @_;
    $F->{PERCENT_TOTAL_TIME_UP} =~ 	s/%//;
    $F->{TIME_UP_HHMMSS}    	=    	t2hms( $F->{TOTAL_TIME_UP} ),
    $F->{TIME_DOWN_HHMMSS}    	=    	t2hms( $F->{TOTAL_TIME_DOWN} ),
    $F->{TIME_UNREACH_HHMMSS}	=    	t2hms( $F->{TOTAL_TIME_UNREACHABLE} ) ;
    qw(TIME_UP_HHMMSS TIME_DOWN_HHMMSS TIME_UNREACH_HHMMSS)
}
) ;
$x->csv_dump ;
```

Volcado de variables de entorno de Nagios (esto es útil cuando programamos plugins que requiren variables de entorno y no sabemos como las tenemos que ver o capturar) :

```perl>
";
print Dumper(%ENV);
print "</pre>";
```

Ejemplo de mk_livestatus

```python>
Llamando al socket directamente desde PHP.

<code php>
$data_address="unix:///usr/local/nagios/var/rw/live";
$query="GET services\nFilter: host_groups >= ServiciosGenerales\nColumns: host_name host_address display_name service_state plugin_output notes_url\nOutputFormat: json\n\n";
$fp = fsockopen($data_address,0);
if (!$fp) {
    echo "$errstr ($errno)<br />\n";
} else {
    $returnval = "";
    fwrite($fp,$query);
    while (!feof($fp)) {
	$returnval = $returnval.fgets($fp, 128);
#	print $returnval;
    }
fclose($fp);
if ($returnval == "[] ")
    $returnval = "";
}
print_r(json_decode($returnval,true));
```

Llamando al socket indirectamente desde PHP utilizando *unixcat*.

Ejemplo de como obtener la lista de equipos y su estado desde php y exportarlo a formato CSV

```php>
Convertir el timestamp de nagios.log a human readable

<code>
tail -f nagios.log | perl -pe 's/(\d+)/localtime($1)/e' 
```

**LQL - The Livestatus Query Language**

El lenguaje de consultas de Livestatus, basicamente consiste en consultas del tipo GET (case sensitive) con estos parámetros a los cuales consideraremos como equivalente a tablas SQL :

* hosts - your Nagios hosts
* services - your Nagios services, joined with all data from hosts
* hostgroups - you Nagios hostgroups
* servicegroups - you Nagios servicegroups
* contactgroups - you Nagios contact groups
* servicesbygroup - new in 1.1.3 - all services grouped by service groups
* servicesbyhostgroup - new in 1.1.3 - all services grouped by host groups
* hostsbygroup - new in 1.1.3 - all hosts group by host groups
* contacts - your Nagios contacts
* commands - your defined Nagios commands
* timeperiods - time period definitions (currently only name and alias)
* downtimes - all scheduled host and service downtimes, joined with data from hosts and services.
* comments - all host and service comments
* log - a transparent access to the nagios logfiles (include archived ones)ones
* status - general performance and status information. This table contains exactly one dataset.
* columns - a complete list of all tables and columns available via Livestatus, including descriptions!

**Ver como Human Readable el timestamp del archivo nagios.log**

```
# cat /usr/local/nagios/var/nagios.log | perl -pe 's/(\d+)/localtime($1)/e'
```

#### NetMySLA

netMySLA es una variedad de procedimientos almacenados de MySQL. Se creó un procedimiento para calcular un único valor de disponibilidad de servicio. El otro itera a través de todos los hosts y servicios para crear valores de disponibilidad de servicio y lo divide en plazos. Por lo tanto, usted puede calcular los valores una vez al día y consulta la tabla de resultados para obtener sus datos.

<https://www.nagiosforge.org/gf/project/netmysla/>

Ejemplo de consulta con netMySLA

**Lista de argumentos**

1.  Hostname varchar (objects)
2.  Servicename varchar (objects) can be null
3.  Consider Downtime boolean
4.  Consider Acknowledge boolean
5.  Consider Unknown boolean
6.  Consider Warning boolean
7.  The initial state boolean (true = up, false = down)
8.  Startdate datetime
9.  Stopdate datetime
10. Debugflag boolean (Display some workflow output)
11. quiet (No resultset, only inserts the values into a table -\> np_sla)

```sql>
^sla_id^host_object_id^host_name^service_object_id^service_name^outage_percent^availability_percent^
|1|122|srv-web1|1761|HTTP|0.0227476|99.9772|

Ejemplo de como llamar al procedimiento desde PHP5, para por ejemplo conocer el SLA de **x** servicio en **n** cantidad de hosts.

<code php>
$consulta ="SELECT nagios_hosts.display_name
    FROM
    nagios_hostgroups,nagios_instances,
    nagios_hosts,nagios_hostgroup_members,
    nagios_objects
    WHERE
    nagios_hostgroups.hostgroup_id=nagios_hostgroup_members.hostgroup_id
    AND
    nagios_hostgroup_members.host_object_id=nagios_hosts.host_object_id
    AND
    nagios_hostgroups.hostgroup_object_id=nagios_objects.object_id
    AND
    nagios_objects.name1 = '{hostgroup_a_consultar}'
    ORDER BY nagios_hosts.display_name ASC";



$pdo = new PDO("mysql:dbname=nagios;host=127.0.0.1", "nagios","nagios");
$lista_hosts = $pdo->prepare($consulta);
$lista_hosts->execute();
$hosts = $lista_hosts->fetchall();

$service = "Servicio a consultar en los hosts";

foreach($hosts as $host) {
	$query = $pdo->prepare("call np_checkAvailability('".$host['display_name']."', '".$service."', false, false, false, false, true, '".$start_time."', '".$end_time."', false, false)");
	$query->execute();
	print_r($query->fetch());
}
```

Devolviendonos algo como esto por cada hosts:

```
Array  
(
    [sla_id] => 1
    [host_object_id] => 463
    [host_name] => 001
    [service_object_id] => 
    [service_name] => Servicio
    [outage_percent] => 0
    [availability_percent] => 100
)
```

Tambien podemos utilizar el script *np_aggregate.sh* para ejecutarlo por medio de cron y tener autogenerados los informes, por cada hosts y cada servicios, organizados *diaria, semanal, mensual y anualmente*.

#### Documentacion al vuelo

Para tener una documentacion actualizada de los equipos y sus servicios, lo ideal es implementar un wiki.

DokuWiki es un software para gestión de webs colaborativas de tipo wiki, escrito en lenguaje PHP y distribuido en códigon abierto bajo la licencia GPL.

Está enfocado para ser usado por grupos de desarrolladores, grupos de trabajo en general y pequeñas compañías.

Su sintaxis es similar a la de MediaWiki, aunque a diferencia de este software, la información se almacena en archivos de texto planos, por lo que no requiere el uso de una base de datos.

**Características:**

- Gestión de espacios de contenidos que permite un almacenamiento ordenado de los documentos.
- Soporte para imágenes y otros contenidos multimedia.
- Índices automatizados de contenidos.
- Control de versiones.
- Corrector ortográfico opcional.
- Interfaz traducido a múltiples idiomas, incluyendo el castellano.
- Posibilidad de utilizar plantillas de diseño.
- Disponibilidad de complementos (plugins) para extender la funcionalidad.
- Control de bloqueos para solucionar problemas de concurrencia.
- Gestión de usuarios.
- Búsqueda de texto completo.

Para integrarlo con Nagios podemos implementar un script en PHP como este :

```php>
 */
 
$host=str_replace(' ', '_',strtolower($_GET['host']));
 
$srv=str_replace(' ', '_',strtolower($_GET['srv']));
 
 
 
if ($host!="" && $srv!="") {
 
        header("Location: doku.php?id=nagios:".$host.":".$srv);
 
        exit;
 
} elseif($host!="") {
 
        header("Location: doku.php?id=nagios:".$host.":host");
 
        exit;
 
} else {
 
        header("Location: doku.php?id=nagios:index");
 
        exit;
 
}
?>
```

Con lo cual deberemos agregar algunas directivas a la configuración de Nagios.

Ejemplo de un serviceextinfo, aunque tambien lo podemos aplicar dentro de la plantilla inicial del servicio.

```
define serviceextinfo {
  host_name router
  service_description ping
  notes_url /wiki/nagios.php?host=$HOSTNAME$&srv=$SERVICEDESC$
  icon_image help.png 
}
```

Desde Dokuwiki haciendo uso de la extension PHP, podemos embeber los datos actuales de los objetos de Nagios, gracias a MK Live Status

```php>
$lista_servidores = shell_exec("/bin/echo -e \"GET hosts\nColumns: name address alias state\n\" | /usr/local/bin/unixcat /usr/local/nagios/var/rw/live");
$lista_hosts = str_getcsv($lista_servidores, "\n");
echo "<div class='level2'> 
<div class='table sectionedit3'>
<table class='inline'> 
<th>Host</th><th>IP</th><th>Descripcion</th><th>Estado Actual</th><th>Metricas / Informes</th>
";

foreach ($lista_hosts as $host) {
    $host = explode(";", $host);
    print "<tr><td>";
    print "<a href='doku.php?id=nagios:servidores:".$host['0']."'>".$host['0']."</a>";
    print "</td><td>";
    print $host['1'];
    print "</td><td>";
    print $host['2'];
    print "</td><td>";
    if($host['3']==0) print "<a href='/nagios/cgi-bin/extinfo.cgi?type=1&host=".$host['0']."'><font color='#008000'>ARRIBA</font></a>";
    if($host['3']==1) print "<a href='/nagios/cgi-bin/extinfo.cgi?type=1&host=".$host['0']."'><font color='#FF0000'>CA&Iacute;DO</font><img src='/nagios/wiki/lib/images/smileys/icon_exclaim.gif' class='middle' /></a>";
    if($host['3']==2) print "<a href='/nagios/cgi-bin/extinfo.cgi?type=1&host=".$host['0']."'><font color='#FF0000'>UNREACHEABLE</font><img src='/nagios/wiki/lib/images/smileys/icon_exclaim.gif' class='middle' /></a>";
    print "</td>";
    print "<td><a href='/pnp4nagios/index.php/graph?host=".$host['0']."'>Performance</a> / <a href='/nagios/cgi-bin/avail.cgi?get_date_parts=&host=".$host['0']."'>Disponibilidad</a></td></tr>";
}

echo "</table></div></div>";
</php>
```

Tambien desde dokuwiki por ejemplo si necesitamos podemos consultar comandos por SSH para mostrar su salida en la documentación de nuestro equipo, como un plus de un dato en tiempo real.

```php>
if (!function_exists("ssh2_connect")) die("function ssh2_connect doesn't exist");
if(!($con = ssh2_connect("oracle", 22))){
    echo "No puedo establecer una conexion SSH\n";
} else {
if (ssh2_auth_pubkey_file($con, 'root',
                          '/home/usuario/.ssh/id_rsa.pub',
                          '/home/usuario/.ssh/id_rsa', 'secret')) {
//  echo "Public Key Authentication Successful\n";
} else {
  die('Public Key Authentication Failed');
}
        if (!($stream = ssh2_exec($con, "/home/usuario/dokuwiki_archlogs.pl" ))) {
            echo "fail: unable to execute command\n";
        } else {
            // collect returning data from command
            stream_set_blocking($stream, true);
            $data = "";
            while ($buf = fread($stream,4096)) {
                $data .= $buf;
            }
            fclose($stream);
            print $data;
        }
    }
</php>
```

```perl
!/usr/bin/perl
use strict;
use warnings;

sub get_sorted_files {
   my $path = shift;
   opendir my($dir), $path or die "no puedo abrir $path: $!";
   my %hash = map {$_ => (stat($_))[9] || undef} # saltar listas vacias
    map  { "$path$_" }
    grep { m/.dbf/i }
    readdir $dir;
    closedir $dir;
    return %hash;
}

my %files = get_sorted_files("/oracle/arclog/DBID/");
print "
    <table class='inline'> 
    <th>Archivo</th><th>Timestamp</th>";
foreach my $key (sort{$files{$a} <=> $files{$b}} keys %files) {
    my $filename = $key;
    $filename =~ s/\.\///g;
    $filename =~ s/\.\.//g;
    print "<tr><td>$filename</td><td>", scalar localtime($files{$key}), "</td></tr>\n";
}
print "</table>";
```

si queremos mostrar los datos parseados de Dokuwiki como un simple HTML en algún sector fuera de la Wiki, podemos hacer uso de este script :

```php
<?php //DokuWiki exporter - copylefted by Harvie.cz 2o1o (copy this file to dokuwiki root)
header('Content-Type: text/html; charset=utf-8');
#if(!defined('DOKU_INC')) define('DOKU_INC',dirname(__FILE__).'/');
define('DOKU_INC','/usr/local/nagios/share/wiki/');
require_once(DOKU_INC.'inc/init.php');
require_once(DOKU_INC.'inc/common.php');
require_once(DOKU_INC.'inc/events.php');
require_once(DOKU_INC.'inc/parserutils.php');
require_once(DOKU_INC.'inc/auth.php');

function p_file_xhtml($id, $excuse=false){
    if(@file_exists($id)) return p_cached_output($id,'xhtml',$id);
    return p_wiki_xhtml($id, '', $excuse);
}

if($_GET['pagina']) {
  echo p_file_xhtml($_GET['pagina'], false);
}
?>
```

#### Plugins interesantes

**check_multiaddr**

A menudo sucede que tenemos hosts a monitorear con multiples ip disponibles, y dado el caso por ejemplo que necesitemos chequear por cualquiera de las dos IP para consultar disponibilidad, y que nuestro plugin intente por una o por otra con un timeout definido, para eso ya existe la solucion *check_multiaddr*, con la cual no necesitamos realizar ninguna modificacion a el código existente de nuestros plugins, por ejemplo en la direccion ip del host a monitorear puede ir *192.168.0.1,192.168.0.11,192.168.0.21*. Luego en la entrada de nuestro comando de chequeo realizamos modificaciones para que quede algo como esto :

```
define command{
        command_name check_multiple_dns
        command_line $USER1$/check_multiaddr.pl $USER1$/check_dns -H $ARG1$ -s $HOSTADDRESS$
}
```

Con lo cual el utilitario *check_multiaddr* actuara simplemente de envoltorio de nuestros plugins, encargandose del timeout entre cada consulta a cada direccion y de devolver su salida con su exit status correspondiente.

Adjunto : check_multiaddr y una version alternativa basada en el mismo código

[check_multiaddr.pl.txt.gz](/manuales/nagios/check_multiaddr.pl.txt.gz)

[check_all_ips.pl.txt.gz](/manuales/nagios/check_all_ips.pl.txt.gz)

**apache**

*/etc/apache2/conf.d/security*

Ciertos parametros son útiles para evitar cierta exposición de nuestro Apache.

```
ServerTokens Prod
ServerSignature Off
TraceEnable Off
```
