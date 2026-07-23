---
title: "MK Livestatus"
date: 2016-03-21T15:49:35-03:00
---

La forma clásica de acceder a la informacion actual de sus hosts y servicios es mediante la lectura y análisis del archivo status.dat, que es creado por Nagios en una base regular. El intervalo de actualización se configura a través status_update_interval en nagios.cfg. Un valor típico es de 10 segundos. Si la instalación es cada vez más grande, usted podría tener que aumentar este valor con el fin de reducir al mínimo el uso de CPU y de E / S de disco. La interfaz web de Nagios utiliza status.dat para mostrar sus datos.

Analizar status.dat no es muy popular entre los desarrolladores de addons. Así que muchos utilizan otro enfoque: NDO. Este es un módulo de ORC que se carga directamente en el proceso de Nagios y envía todas las actualizaciones de estado a través de un socket UNIX a un proceso de ayuda. Eso crea sentencias SQL y actualizaciones de varias tablas en una base de datos MySQL o PostgreSQL. Este enfoque tiene varias ventajas sobre status.dat:

* Los datos se actualizan de inmediato, no sólo cada 10 o 20 segundos.
* Las solicitudes tienen acceso fácil a los datos a través de SQL. N analizador para status.dat es necesario.
* En las grandes instalaciones el acceso de los addons a los datos es más rápida que la lectura status.dat.

Lamentablemente, sin embargo, ha NDO también algunas deficiencias graves:

* Tiene una configuración compleja.
* Se necesita una (creciente) base de datos para ser administrado.
* Se alimenta de una parte significativa de sus Recursos de la CPU, solo con el fin de mantener la base estrictas disponibles.
* Limpieza periódica de la base de datos puede colgar Nagios.

El futuro

Desde la versión 1.1.0, Check_MK ofrece un enfoque totalmente nuevo para acceder a datos de estado y también histórico: Livestatus. Así como NDO, Livestatus hacer uso de la API de Nagios evento Broker y carga un módulo binario en su proceso de Nagios. Pero luego otros NDO, Livestatus no realiza escribir datos. En su lugar, se abre un socket en la que pueden consultar los datos a demanda.

La toma permite enviar una solicitud de los servicios u otros datos y obtener una respuesta inmediata. Los datos son directamente leídos de estructuras de datos internas de Nagios. Livestatus no crea su propia copia de esos datos. A partir de la versión 1.1.2 que también se pueden recuperar los datos históricos de los archivos de registro a través de Nagios Livestatus.

Esto es no sólo un enfoque increíblemente simple, si no también muy rápido. Algunas ventajas son:

* Livestatus no impone una carga mensurable de su CPU para nada. Sólo en el tratamiento de las consultas de una cantidad muy pequeña de la CPU es necesario. Pero eso ni siquiera se bloqueará Nagios.
* Livestatus produce cero / S de disco cuando quering datos de estado.
* Acceso a los datos es mucho más rápido que analizar status.dat o consultar una base de datos SQL.
* No se necesita configuración, base de datos no es necesaria. Ninguna administración es necesario.
* Livestatus escalas bastante bien a las grandes instalaciones, incluso más allá de 50,000 servicios.
* Livestatus le da acceso a los datos específicos de Nagios no se dispone de ningún otro método disponible acceder al estado - por ejemplo, la información del tiempo una multitud se encuentra actualmente en período de notificación.

En el mismo tiempo, ofrece a sus Livestatus propio lenguaje de consulta que es simple de entender, ofrece la mayoría de la flexibilidad de SQL e incluso más en algunos casos. Es un protocolo rápido, ligero y no necesita un cliente binario. Incluso, pueden obtener acceso a los datos sin ningún tipo de software especial de ayuda.

## Implementación

### Compilación

```
root@linux# wget 'http://www.mathias-kettner.de/download/mk-livestatus-1.2.6p16.tar.gz'
root@linux# tar xzf mk-livestatus-1.2.6p16.tar.gz
root@linux# cd mk-livestatus-1.2.6p16
root@linux#
root@linux# ./configure --with-nagios4
checking for a BSD-compatible install... /usr/bin/install -c
checking whether build environment is sane... yes
checking for a thread-safe mkdir -p... /bin/mkdir -p
checking for gawk... gawk
checking whether make sets $(MAKE)... yes
checking for g++... g++
checking for C++ compiler default output file name... a.out
checking whether the C++ compiler works... yes
checking whether we are cross compiling... no
checking for suffix of executables...
checking for suffix of object files... o
checking whether we are using the GNU C++ compiler... yes
checking whether g++ accepts -g... yes
...
configure: creating ./config.status
config.status: creating Makefile
config.status: creating src/Makefile
config.status: creating config.h
config.status: config.h is unchanged
config.status: executing depfiles commands
root@linux# make
g++ -DHAVE_CONFIG_H -I. -I..    -I../nagios -fPIC -g -O2 -MT livestatus_so-AndingFil...
g++ -DHAVE_CONFIG_H -I. -I..    -I../nagios -fPIC -g -O2 -MT livestatus_so-ClientQue...
g++ -DHAVE_CONFIG_H -I. -I..    -I../nagios -fPIC -g -O2 -MT livestatus_so-Column.o ...
g++ -DHAVE_CONFIG_H -I. -I..    -I../nagios -fPIC -g -O2 -MT livestatus_so-ColumnsCo...
g++ -DHAVE_CONFIG_H -I. -I..    -I../nagios -fPIC -g -O2 -MT livestatus_so-ContactsC...
g++ -DHAVE_CONFIG_H -I. -I..    -I../nagios -fPIC -g -O2 -MT livestatus_so-CustomVar...
g++ -DHAVE_CONFIG_H -I. -I..    -I../nagios -fPIC -g -O2 -MT livestatus_so-CustomVar...
....
root@linux# make install
Making install in src
make[1]: Entering directory `/d/nagvis-dev/src/mk-livestatus-1.2.6p16/src'
make[2]: Entering directory `/d/nagvis-dev/src/mk-livestatus-1.2.6p16/src'
test -z "/usr/local/bin" || /bin/mkdir -p "/usr/local/bin"
  /usr/bin/install -c 'unixcat' '/usr/local/bin/unixcat'
test -z "/usr/local/lib/mk-livestatus" || /bin/mkdir -p "/usr/local/lib/mk-livestatus"
 /usr/bin/install -c -m 644 'livestatus.so' '/usr/local/lib/mk-livestatus/livestatus.so'
 ranlib '/usr/local/lib/mk-livestatus/livestatus.so'
/bin/sh /d/nagvis-dev/src/mk-livestatus-1.2.6p16/install-sh -d /usr/local/lib/mk-livestatus
/usr/bin/install -c livestatus.o /usr/local/lib/mk-livestatus
rm -f /usr/local/lib/mk-livestatus/livestatus.so
make[2]: Leaving directory `/d/nagvis-dev/src/mk-livestatus-1.2.6p16/src'
make[1]: Leaving directory `/d/nagvis-dev/src/mk-livestatus-1.2.6p16/src'
make[1]: Entering directory `/d/nagvis-dev/src/mk-livestatus-1.2.6p16'
make[2]: Entering directory `/d/nagvis-dev/src/mk-livestatus-1.2.6p16'
make[2]: Nothing to be done for `install-exec-am'.
make[2]: Nothing to be done for `install-data-am'.
make[2]: Leaving directory `/d/nagvis-dev/src/mk-livestatus-1.2.6p16'
make[1]: Leaving directory `/d/nagvis-dev/src/mk-livestatus-1.2.6p16'
```

Despues tenemos que especificar que Nagios cargue el archivo objeto compilado *livestatus.o*, para eso debemos agregar a nagios.cfg:

```
broker_module=/usr/local/lib/mk-livestatus/livestatus.o /var/lib/nagios/rw/live
event_broker_options=-1
```

Y dentro del log de Nagios vamos a ver algo similar a esto

```
[1256144866] livestatus: Version 1.2.6p16 initializing. Socket path: '/var/lib
/nagios/rw/live'
[1256144866] livestatus: Created UNIX control socket at /var/lib/nagios/rw/
live
[1256144866] livestatus: Opened UNIX socket /var/lib/nagios/rw/live
[1256144866] livestatus: successfully finished initialization
[1256144866] Event broker module '/usr/local/lib/mk-livestatus/livestatus.o' initializ
ed successfully.
[1256144866] Finished daemonizing... (New PID=5363)
[1256144866] livestatus: Starting 10 client threads
[1256144866] livestatus: Entering main loop, listening on UNIX socket
```

### Opciones y configuración del módulo

| Opción | Valor por default | Que significa |
|----|----|----|
| debug | 0 | Set this to 1 in order to make Livestatus log each query it executes in nagios.log |
| max_cached_messages | 500000 | Livestatus’ access to Nagios logfiles caches messages in-memory. Here you can set the maximum number of cached messages. Each message takes about 250 bytes (in the current implementation) |
| max_response_size | 104857600 | Livestatus constructs each response in-memory before sending it to the clients. In order to avoid a crash in case of extensive queries, the maximum response size is limited. The default limit is 100 MB |
| num_client_threads | 10 | Livestatus needs one thread for each concurrent client connection. A fixed number of threads is created when Nagios starts |
| thread_stack_size | 65536 | This parameter sets the size of the stack of each client thread. In versions before 1.1.4, the stack size was set to 8 MB (pthread default). The new default value is 64 KB. A small stack reduces virtual memory usage and also save CPU ressources. A too small value will probably crash your Nagios process, though. You have been warned |
| query_timeout | 10000 | This value is in ms. In order to avoid being hung by broken clients, Livestatus imposes a limit on the time for reading the query from the client. A value of 0 disables the timeout |
| idle_timeout | 300000 | This value is in ms. Livestatus is waiting at most that much time for the next query. A value of 0 disables the timeout |

Ejemplo de como agregar opciones

```
broker_module=/usr/local/lib/mk-livestatus/livestatus.o /var/run/nagios/rw/live debug=1
```

Ejemplo de como dejar MK Livestatus escuchando en un socket tcp para consultarlo por red, por ejemplo por un sistema de reportes al estilo Jasper Reports o alguna interfaz alternativa como Thruk.

```
service livestatus
{
	type		= UNLISTED
	port		= 6557
	socket_type	= stream
	protocol	= tcp
	wait		= no
# limit to 100 connections per second. Disable 3 secs if above.
	cps             = 100 3
# set the number of maximum allowed parallel instances of unixcat.
# Please make sure that this values is at least as high as
# the number of threads defined with num_client_threads in
# etc/mk-livestatus/nagios.cfg
        instances       = 500
# limit the maximum number of simultaneous connections from
# one source IP address
        per_source      = 250
# Disable TCP delay, makes connection more responsive
	flags           = NODELAY
	user		= nagios
	server		= /usr/local/nagios/bin/unixcat
	server_args     = /usr/local/nagios/var/rw/live
# configure the IP address(es) of your Nagios server here:
#	only_from       = 127.0.0.1 10.0.20.1 10.0.20.2
	disable		= no
}
```

## Ejemplos

### Python

#### livestatus

```python>
=== Monitoring::Livestatus ===
API de Perl para acceder a datos Nagios e Icinga por medio de check_mk livestatus
<code perl>
use Monitoring::Livestatus;
    my $ml = Monitoring::Livestatus->new(
      socket => '/var/lib/livestatus/livestatus.sock'
    );
    my $hosts = $ml->selectall_arrayref("GET hosts");
```

##### new (\[ARGS\])

Creates an Monitoring::Livestatus object. new takes at least the socketpath. Arguments are in key-value pairs. Crea el objeto Monitoring::Livestatus. Toma la ruta del socket.

| Argumentos |  |
|----|----|
| socket | Ruta al socket UNIX de check_mk livestatus |
| server | Usar este servidor para una conexión TCP |
| peer | Camino alternativo al socket o servidor, si se usa el valor ‘:’ va a ser usado servidor sino socket |
| name | Nombre para la conexión, default dirección socket/server |
| verbose | Modo verbose |
| line_seperator | Código ascii separador de líneas default 10, (newline) |
| column_seperator | Código ascii separador de columnas, defaults 0 (null byte) |
| list_seperator | Código ascii separador de listas, default 44 (comma) |
| host_service_seperator | Código ascii separador host/service, default 124 (pipe) |
| keepalive | keepalive. Default off |
| errors_are_fatal | Errores con mensaje de caída. Default: on |
| warnings | muestran advertencias en las consultas sin Columnas |
| timeout | Tiempo de espera general. Usado para conexión y consultas |
| query_timeout | set a query timeout. Used for retrieving querys, Default 60sec |
| connect_timeout | Tiempo de espera de conexión inicial. Por defecto 5 s |

##### Limit

```perl>
selectcol_arrayref(
       "GET hosts\nColumns: name contacts",
       { Limit => "10,20" }
    );
```

### Perl

#### Monitoring::Livestatus

```perl>
new(
	socket => '/usr/local/nagios/var/rw/live'
);

my $up = $ml->selectscalar_value("GET hosts\nFilter: host_groups >= $filtergrp\nStats: state = 0");
my $down = $ml->selectscalar_value("GET hosts\nFilter: host_groups >= $filtergrp\nStats: state = 1");
my $unknown = $ml->selectscalar_value("GET hosts\nFilter: host_groups >= $filtergrp\nStats: state = 2");
my $total = $up + $down + $unknown;

print "$down\/$total \n";
```
