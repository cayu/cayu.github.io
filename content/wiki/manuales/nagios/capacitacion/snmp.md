---
title: "SNMP (Simple Network Management Protocol)"
date: 2016-07-27T11:30:09-03:00
---

Protocolo Simple de Administración de Red o SNMP es un protocolo de la capa de aplicación que facilita el intercambio de información de administración entre dispositivos de red. Es parte de la familia de protocolos TCP/IP. SNMP permite a los administradores supervisar el desempeño de la red, buscar y resolver sus problemas, y planear su crecimiento. Podemos conocer datos internos de dispositivos a monitorear, ej uso de CPU, Memoria, Disco, Uptime etc.

Se basa entre gestor y agentes.

Los agentes se centran en recopilar cierta información y mantenerla organizada para que el gestor pueda acceder a ella cuando lo necesite. El agente también puede mandar notificaciones al gestor sobre problemas o información relevante sin que el gestor se lo solicite.

El gestor controla y actúa sobre los elementos de la red, controlando la información que recopilan los agentes se pueden tomar decisiones y actuar sobre la red para mejorar los aspectos que se necesiten. Para ello se basa en tres elementos:

- **SNMP**: Es el protocolo entre el gestor y el elemento de red, se suele utilizar UDP
- **SMI**: (Structure of Management Information): conjunto de estructuras y esquemas de identificación para definir las MIB´s
- **MIB**: (Management Information Base): especifica qué variables mantienen los elementos de la red (agentes)

##### SMI (Structure of Management Information)

SMI es el conjunto de estructuras y esquemas para definir elementos de las MIB. Dichas estructuras están formateadas en ASN.1 (Abstract Synstax Notation One), indicando cómo debe ser el nombre, la sintaxis y el método de codificación de los datos para su transmisión por la red.

Un objeto en SMI consta de los siguientes campos:

- Objeto: Literal (Object descriptor) con la etiqueta que identifica el objeto

<!-- -->

- Sintaxis: Puede ser alguno de los siguientes tipos ASN.1:
  - SimpleSyntax (INTEGER, OCTET STRING, OBJECT IDENTIFIER, NULL)
  - ApplicationSyntax (NetworkAddress (direcciones ip de 32 bits), Counter (enteros no negativos de tamaño 32bits), Gauge (contadores que mantienen el maximo valor alcanzado, pueden decrementarse en algun momento), TimeTicks (centésimas de segundo desde el evento deseado), Opaque (información arbitraria))
  - Otros tipos (RFC 1155).
- Acceso: Puede ser: read-only, read-write, write-only, not-acessible. SMIv2 incluye además read-create, y deja de usar write-only
- Status: Puede ser: mandatory (en SMIv2 se indica como current), que tienen que ser implementadas por cualquier versión de la MIB que incluya ese modulo, optional, que pueden faltar sin que eso cause ningún problema ( en vez de optional, SMIv2 incluye deprecated, para objetos que ya no se usan, y que por tanto, no tienen por qué estar implementados), u obsolete, que han dejado de mantenerse y revisarse.
- Descripción: Definición textual de la semántica del tipo de objeto. No es codificable mediante una computadora, es una descripción para programadores y administradores que pueden leerla para entender el funcionamiento de la MIB.

La definición termina indicando bajo qué nodo del árbol de OIDs (ver la siguiente sección) debe situarse y con qué número a efectos de identificación

##### MIB: (Management Information Base)

La información requerida de un sistema se almacena en las MIBs usando una estructura jerárquica que contiene los identificadores de objeto descritos mediante ASN.1.

Esta jerarquía de árbol contiene los elementos que pueden ser de tipo escalar o tablas de datos. Estos objetos cuelgan en el árbol de la MIB de la rama correspondiente a la organización que mantiene dicha estructura. Se nombra a los ejemplares de la MIB mediante su identificador de objeto que es una cadena de enteros que representa como cuelga el objeto del nodo raíz. Por ejemplo IP es el (1.3.6.1.2.1.4.)

Esta es la estructura de la MIB-II (RFC 1213) y sus diferentes subgrupos. Estas son algunas de las variables que se mantienen en cada grupo:

- Grupo system: Descripción de la entidad, identificador, tiempo desde arranque, nombre del administrador, localización física, servicios ofrecidos
- Grupo interfaces: Número de interfaces del sistema
- Grupo at: Número de interfaz, Dirección física, Dirección IP
- Grupo ip: Si el sistema hace forward, valor del TTL, número de datagramas recibidos y enviados, errores, datagramas con protocolo no válido, etc.
- Grupo icmp: cuatro contadores generales: número total de mensajes ICMP de entrada y salida con o sin errores y 22 contadores para los diferentes mensajes ICMP
- Grupo tcp: Algoritmo de retransmisión, timeout en milisegundos, número de conexiones TCP, número de transiciones entre los diferentes estados de TCP, número de segmentos recibidos y enviados, número de segmentos retransmitidos, con error y con el flag RST activado.
- Grupo udp: Número de datagramas enviados y recibidos, datagramas sin proceso receptor
- Grupo egp: Número de mensajes de encaminamiento recibidos con y sin error, número de mensajes generados en el sistema, estado del sistema.

##### Funcionamiento del protocolo

Como sigue el paradigma gestor-agente la los comandos son para solicitar, modificar o devolver información de la MIB, así como envíar notificaciones (traps). Las operaciones disponibles en SNMPv1 son:

- Get-request : Solicitar el valor de una o varias variables
- Get-next-request : Solicitar el valor de la siguiente variable o grupo de variables. Se utiliza para recorrer tablas, celda a celda.
- Set-request : Dar valor a una variable
- Get-response : Devolver el valor de una o varias variables
- Trap : El agente notifica al gestor de alguna situación especial sucede en el elemento de red. Reinicialización, fallo y recuperación en un enlace

<!-- -->

      Posteriormente, a partir de la versión 2 se introdujo la operación de get-bulk-request, que permite realizar varios get-next seguidos sin tener que hacer varias peticiones. También se introdujo la operación de inform, para que distintos gestores puedan intercambiarse notificaciones.

**SNMPv1**

Ejemplo de configuración SNMPv3 para un sistema típico Linux/UNIX/AIX/Solaris etc

```
rocommunity public 192.168.1.4
syscontact Sysadmin (root@localhost)
syslocation Server Room

view all included .1
access notConfigGroup "" any noauth exact systemview none none
access notConfigGroup "" any noauth exact all none none
```

Donde rocommunity es una comunidad de solo lectura, de nombre public y que solo se le permita el acceso desde la ip 192.168.1.4, el nombre de la comunidad define la dificultad de adivinar su acceso, por eso es recomendable que tenga un nombre similar a una contraseña.

**SNMPv3**

Configuramos un usuario que se llama nagios con autenticacion, en la siguiente linea lo creamos con una clave almacenada en texto plano en este archivo y luego encriptada en MD5 y DES

Ejemplo de configuración SNMPv3 para un sistema típico Linux/UNIX/AIX/Solaris etc

```
rouser  nagios auth 
createUser nagios MD5 "claveNagios" DES
```

Debemos reiniciar el servicio SNMP y luego podemos hacer algo como esto

*snmpget -v 3 -u nagios -l authNoPriv -a MD5 -A claveNagios 127.0.0.1 sysUpTime.0*

**TIPS**

NOTA: En Debian deberemos editar el archivo */etc/default/snmpd*, modificando la siguiente linea

De:

```
SNMPDOPTS='-Lsd -Lf /dev/null -u snmp -I -smux -p /var/run/snmpd.pid 127.0.0.1'
```

A :

```
SNMPDOPTS='-Lsd -Lf /dev/null -u snmp -I -smux -p /var/run/snmpd.pid'
```

Para que el servicio se accesible por red.

A veces por causa de tener montados filesystems remotos NFS o CIFS, cuando tenemos retrasos en la conexion con ellos se puede colgar el servicio SNMP, para prevenir esto deberemos agregar la siguiente opción en el archivo de configuración *snmpd.conf*.

```
skipNFSInHostResources yes
```

##### Agregar variables a SNMP

Por ejemplo si queremos obtener desde SNMP la información de un script

**/usr/local/bin/var.sh**

```bash>
En el snmpd.conf le agregamos la siguiente configuración:

<code>
exec .1.3.6.1.4.1.2021.555 /bin/sh /usr/local/bin/var.sh
```

Si corremos esta consulta **snmpwalk -v 1 -c public localhost .1.3.6.1.4.1.2021.555** Obtendremos :

```
UCD-SNMP-MIB::ucdavis.555.1.1 = INTEGER: 1
UCD-SNMP-MIB::ucdavis.555.2.1 = STRING: "/bin/sh"
UCD-SNMP-MIB::ucdavis.555.3.1 = STRING: "/tmp/bar.sh"
UCD-SNMP-MIB::ucdavis.555.100.1 = INTEGER: 0
UCD-SNMP-MIB::ucdavis.555.101.1 = STRING: "hola"
UCD-SNMP-MIB::ucdavis.555.101.2 = STRING: "chau"
UCD-SNMP-MIB::ucdavis.555.102.1 = INTEGER: 0
UCD-SNMP-MIB::ucdavis.555.103.1 = ""
```

**SNMPv1 en Windows**

En los equipos Windows se configurara una community RO (read only) denominada public, acceso para la ip que corresponda al servidor que tiene acceso al SNMP, en este caso nuestro nagios.

Para ello nos situamos en el panel de control dentro de agregar programas en la sección de componentes.

<img src="/manuales/nagios/add-remove-button.gif" data-query="?715x519" width="715" height="519" alt="add-remove-button.gif" />

Una vez dentro nos situamos dentro de Herramientas de administración y supervisión o Management and Monitoring tools

![install_snmp_01_70p.gif](/manuales/nagios/install_snmp_01_70p.gif)

Dentro de ella nos situamos sobre la opcion SNMP y la tildamos

![install_snmp_02_70p.gif](/manuales/nagios/install_snmp_02_70p.gif)

Luego en la Administración de Servicios, nos dirigiremos a las propiedades del servicio SNMP

<img src="/manuales/nagios/servicios.png" data-query="?714x427" width="714" height="427" alt="servicios.png" />

Dentro de ella nos encontraremos con un cuadro de dialogo

![prop.png](/manuales/nagios/prop.png)

Configuraremos el accesso a la ip del servidor con acceso SNMP

![hosts.png](/manuales/nagios/hosts.png)

Y luego la comunidad de aceso a los datos SNMP

![cummunity.png](/manuales/nagios/cummunity.png)

Luego de realizados los cambios de configuración procederemos a reiniciar el servicio para que estos surtan efecto.

A veces en Windows es necesario configurar el servicio de Firewall para permitir las consultas por medio SNMP.

Ref.: <http://support.microsoft.com/kb/324263/es>

## SNMP Traps

Una trap es generado por el agente snmp en el dispositivo a monitorear para reportar ciertas condiciones y cambios de estado en un procesp

- Se “cae” un servicio
- Hay un problema de memoria o de hardware
- La carga de procesos excede un límite
- Se llena una partición de disco

En debian para instalar el manejador de traps SNMP deberemos ejecutar los siguiente :

```
apt-get install snmptt
```

**/etc/snmp/snmptt.ini**

```
mode = daemon
log_system_enable = 1
unknown_trap_log_enable = 1

mysql_dbi_enable = 1
mysql_dbi_host = localhost
mysql_dbi_port = 3306
mysql_dbi_database = snmptt
mysql_dbi_table = snmptt
mysql_dbi_table_unknown = snmptt_unknown
mysql_dbi_table_statistics = snmptt_statistics
mysql_dbi_username = snmptt
mysql_dbi_password = mytrap
```

**/etc/snmp/snmptt.conf**

```
EVENT linkDown .1.3.6.1.6.3.1.1.5.3 "Status Events" Critical
```

**/etc/snmp/snmptrapd.conf**

```
disableAuthorization yes
traphandle default /usr/sbin/snmptthandler
```

Configuración de permisos :

```bash>
**/etc/default/snmpd**
<code>
TRAPDRUN=yes
```

Schema MySQL

```sql>
Reiniciamos los servicios SNMP :
<code>
/etc/init.d/snmptt
/etc/init.d/snmpd
```

<img src="/manuales/nagios/nagios_snmptraps.png" class="align-center" alt="nagios_snmptraps.png" />

Ref.: <http://exchange.nagios.org/directory/Addons/SNMP/Nagios-SNMP-Trap-Interface-%28NSTI%29/details>
