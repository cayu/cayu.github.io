---
title: "NDO2Utils"
date: 2015-10-22T14:37:57-03:00
---

NdoUtils, es una herramienta complementaria a Nagios, que nos permite almacenar en una base de datos la información Nagios.

```
root@localhost # tar xvfzp ndoutils-1.4b7.tar.gz
root@localhost ndoutils-1.4b7 # ./configure

*** Configuration summary for ndoutils 1.4b7 10-31-2007 ***:

 General Options:
 -------------------------
 NDO2DB user:    nagios
 NDO2DB group:   nagios


Review the options above for accuracy.  If they look okay,
type 'make' to compile the NDO utilities.

root@localhost ndoutils-1.4b7 # make
root@localhost ndoutils-1.4b7 # cd db
root@localhost db #./installdb -u nagios -p nagios -h localhost -d nagios
```

Hay 4 componentes principales que inician las utilidades NDO:

1.  NDOMOD Event Broker Module (Modulo de evento corredor)
2.  LOG2NDO Utility
3.  FILE2SOCK Utility
4.  NDO2DB Daemon

##### El NDOMOD Event Broker Module

Las utilidades NDO incluyen un Nagios Even Broker Module (NDOMOD.O) que exporta datos desde el demonio de nagios.

Asumiendo que nagios fue compilado con el Modulo Event Broker activado (esto es por default), usted puede configurar que nagios cargue el modulo NDOMOD en tiempo de ejecucion. Una vez que el modulo fue cargado por el daemon de nagios, este puede acceder a todos los datos y logicamente presente el el proceso de nagios que esta corriendo.

El modulo NDOMOD tiene designado exportar la configuracion, como informacion variada de eventos en tiempo de ejecucion que ocurre en el proceso de monitoreo, por el daemon de nagios. El modulo puede enviar esta informacion a un archivo estandar, a un Socket Unix de Dominio o un a socket TCP.

El NDOMOD escribe la info en un formato que el demonio NDO2DB puede entender.

Si el NDOMOD esta escrito para un archivo de salida, usted puede configurarlo para rotarlo periodicamente y/o procesarlo en otra maquina fisicamente (usando SSH, etc.) y envia este contenido al daemon NDO2DB usando la utilidad FILE2SOCK (que describiremos mas adelante).

##### La utilidad LOG2NDO

Esta es designada para permitir importar un historial de logs de nagios a una BD via el NDO2DB daemon (describiremos luego). La utilidad trabaja enviando archivos de logs históricos a un archivo estandar, un unix sock o un tcp sock en un formato que NDO2DB daemon entienda. El NDO2DB daemon puede luego usarlo para procesar la salida y almacenar en un archivo de log historico informandolo en una BD.

##### La utilidad FILE2SOCK

Esta utilidad es muy simple, solo lee de un archivo estandar (o STDIN) y escribe todo sobre un socket de dominio unix o un tcp socket. Estos datos son leidos y no son procesados por nada, antes de ser enviados al socket.

##### El demonio NDO2DB

La utilidad es diseñada para tomar los datos de salida de los componentes NDOMOD y LOG2NDO y almacenarlos en una BD MySQL o BD PostgreSQL.

Cuando este inicia, el daemon NDO2DB crea un socket y espera que los clientes se conecten. NDO2DB puede correr independientemente, bajo un demonio multiproceso o bajo inetd (si esta usando un socket TCP).

Multiples clientes pueden conectarse al daemon NDO2DB y transmitir simultáneamente.

##### Instalación

Una vez compilado el modulo **NDO** procederemos a instalarlo manualmente

- **cp src/ndomod-3x.o /usr/local/nagios/bin/ndomod.o**
  - *Con esto copiaremos el modulo al directorio de ejecución de Nagios*
- **cp config/ndomod.cfg /usr/local/nagios/etc**
  - *De esta manera instalaremos la configuración inicial del modulo*

Igualmente lo haremos con el daemon **NDO2DB**

- **cp src/ndo2db-3x /usr/local/nagios/bin/ndo2db**
  - *Con esto copiaremos el daemon al directorio de ejecución de Nagios*
- **cp config/ndo2db.cfg /usr/local/nagios/etc**
  - *De esta manera instalaremos la configuración inicial del daemon*

##### Configuración

```sql>
**NDO**

Cuando la base de datos esta cargada con una gran cantidad de registros, el NDO comienza a tener un comportamiento erratico, y los datos reflejados no son los correctos, por lo tanto cada cierto tiempo hay que purgar algunas tablas, ese tiempo sera dependiendo de la cantidad de objetos a monitorear.

Podemos crear un script que realize dicha tarea

<code bash>
#!/bin/bash
echo "TRUNCATE TABLE nagios_servicechecks" | /usr/bin/mysql -u root --batch --database=nagios
echo "TRUNCATE TABLE nagios_logentries" | /usr/bin/mysql -u root --batch --database=nagios
echo "TRUNCATE TABLE nagios_service_contactgroups" | /usr/bin/mysql -u root --batch --database=nagios
echo "TRUNCATE TABLE nagios_hostchecks" | /usr/bin/mysql -u root --batch --database=nagios
```

Para luego incluirlo en el **crontab**, por ejemplo si tenemos alrededor de mas de 600 hosts y alrededor de 2000 servicios o mas, chequeando ambos en intervalos de ente 1 y 5 minutos, podemos establecer su ejecucion en 15 minutos.

```
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

#### MySQL

MySQL es uno de los Sistemas Gestores de Bases de Datos Relacional multihilo y multiusuario, más populares,

##### Compilacion

Debemos descargar el paquete de su web oficial y proceder a compilarlo.

```
root@localhost # groupadd mysql
root@localhost # useradd -g mysql mysql
root@localhost # gunzip < mysql-VERSION.tar.gz | tar -xvf -
root@localhost # cd mysql-VERSION
root@localhost # ./configure --prefix=/usr/local/mysql
root@localhost # make
root@localhost # make install
root@localhost # cp support-files/my-medium.cnf /etc/my.cnf
root@localhost # cd /usr/local/mysql
root@localhost # bin/mysql_install_db --user=mysql
root@localhost # chown -R root  .
root@localhost # chown -R mysql var
root@localhost # chgrp -R mysql .
root@localhost # bin/mysqld_safe --user=mysql &
```

##### Establecer la contraseña del usuario root

Por defecto, el usuario root no tiene asignada una contraseña y esto no es nada recomendable, así que vamos a establecer una. Utilizamos el comando:

*mysqladmin -u root password ‘contraseñadificil’*

Cambiar ‘loquesea’ por la contraseña que desemos establecer, pero es importante no olvidarse de teclear las comillas simples.

Comprobamos la nueva contraseña abriendo una sesión en mysql:

*mysql -u root -p*

Nos pedirá la contraseña, la tecleamos, y si todo es correcto entraremos en la interfaz del cliente de MySQL, podemos teclear algún comando de mysql para interactuar con el servidor, por ejemplo:

*mysql\> show databases;*

Nos mostrará las bases de datos que existan en el servidor, normalmente y si acabamos de instalar, aparecerán las bases de datos mysql y test.

Deberemos crear una base de datos llamada Nagios

*mysql\>create database nagios;*

```
Query OK, 1 row affected (0.00 sec)
mysql>show databases;

+----------+
| Database |
+----------+
| mysql    |
| nagios   |
| test     |
+----------+
3 rows in set (0.01 sec)
```

Ahora deberemos crear un usuario con privilegios de SELECT, INSERT, UPDATE, DELETE

```
CREATE USER nagios IDENTIFIED BY 'nagios123';
```

Y darle privilegios sobre la base de datos nagios

```
mysql> GRANT ALL ON nagios.* TO nagios@localhost IDENTIFIED BY "nagios";
Query OK, 0 rows affected (0.00 sec)

mysql> quit
```

Tecleamos exit o quit para salir del programa cliente.

##### Paquetes

La instalacion de **MySQL**, en el caso de tenerlo empaquetado en nuestra distribucion Linux, es bastante simple

\*\* CentOS y RedHat \*\*

Para instalar el paquete mysql haremos uso de la utilidad de sistema **up2date** o **yum**

*up2date mysql-server*

*yum install mysql-server*

Y para dejar el servicio corriendo en segundo plano

*service mysqld start*

Para configurar el arranque del servicio

Lo habitual será que cuando arranque o se pare nuestro servidor tambien se inicie o detenga el MySQL, para ello deberemos ejecutar:

*chkconfig –level 35 mysqld on*

Esto activa el demonio mysqld en los runlevel 3 y 5, y lo detiene en el resto. Si queremos comprobar el estado del servicio podemos utilizar lo siguiente:

*chkconfig –list mysqld*

\*\* Debian \*\*

Para instalar mysql en Debian, deberemos ejecutar :

*apt-get install mysql-server mysql-common mysql-client*

Para arrancar el servicio y dejarlo corriendo :

*/etc/init.d/mysql start*

Y para setear su arranque por defecto :

*update-rc.d -f mysql defaults*

\*\* Nota SuSE \*\*

Para setear el arranque de un servicio en SuSE Linux deberemos ejecutar el comando *insserv -d nombre_servicio* con el parametro *-d* estariamos indicando una opcion similar a *defaults* como en Debian.

#### Ejemplo de consultas MySQL

Podemos realizar plugins que dependan de la salida de otros plugins sin necesidad de consultar o ejecutar nuevamente estos, uilizando los datos contenidos en la base de datos MySQL

Por ejemplo si queremos consultar la lista de miembros de un grupo de hosts

```sql>
Para conocer el estado de un servicio de un determinado host

<code sql>
    SELECT
    nagios_servicestatus.perfdata,nagios_servicestatus.output
    FROM
    nagios_services,nagios_servicestatus,
    nagios_objects,nagios_instances
    WHERE
    nagios_services.instance_id=nagios_instances.instance_id
    AND
    nagios_servicestatus.service_object_id=nagios_services.service_object_id
    AND
    nagios_servicestatus.service_object_id=nagios_objects.object_id
    AND
    nagios_objects.name2 = '{servicio_a_consultar}'
    AND
    nagios_services.config_type='1'
    AND
    nagios_objects.name1 = '{host_que_lo_contiene}'
```

Para conocer el estado de un host

```sql>
Para consultar los servicios pertenecientes a un determinado grupo de servicios

<code sql>
    SELECT
    obj2.name1 AS host_name,
    obj2.name2 AS service_description
    FROM nagios_servicegroups
    INNER JOIN nagios_servicegroup_members ON
    nagios_servicegroups.servicegroup_id=nagios_servicegroup_members.servicegroup_id
    INNER JOIN nagios_services ON
    nagios_servicegroup_members.service_object_id=nagios_services.service_object_id
    INNER JOIN nagios_objects as obj1 ON
    nagios_servicegroups.servicegroup_object_id=obj1.object_id
    INNER JOIN nagios_objects as obj2 ON
    nagios_servicegroup_members.service_object_id=obj2.object_id
    INNER JOIN nagios_instances ON
    nagios_servicegroups.instance_id=nagios_instances.instance_id
    WHERE nagios_servicegroups.alias ='{alias_service_group}'
    AND nagios_services.display_name ='{alias_de_servicio}'
```

Para conocer los estados de los hosts

```sql>
Resultando en una tabla como esta

^display_name^current_state^
|firewall|0|
|apache|1|
|servidor-sql|0|
|sap-db|0|


Para conocer el estado de un servicio, independientemente de los hosts en los que se encuentre disponible

En este caso el servicio **HTTP**

<code sql>
SELECT obj1.name1 AS host_name,
nagios_services.service_object_id,
obj1.name2 AS service_description,
nagios_servicestatus.current_state
FROM `nagios_servicestatus`
LEFT JOIN nagios_objects AS obj1 ON nagios_servicestatus.service_object_id = obj1.object_id
LEFT JOIN nagios_services ON nagios_servicestatus.service_object_id = nagios_services.service_object_id
LEFT JOIN nagios_instances ON nagios_services.instance_id = nagios_instances.instance_id
WHERE nagios_services.config_type = '1'
AND obj1.name2 = 'HTTP'
```

Resultando en una tabla como esta

| host_name         | service_object_id | service_description | current_state |
|-------------------|-------------------|---------------------|---------------|
| concentrador_mpls | 18                | HTTP                | 0             |
| router-vpn        | 23                | HTTP                | 1             |
| central           | 28                | HTTP                | 2             |
| router-45         | 33                | HTTP                | 0             |

Consulta simple para obtener un pantallazo general del estado de servicios de un hostgroup

```sql>
Resultando en una tabla como esta

^states^number^
|OK|1754|
|WARNING|13|
|CRITICAL|84|
|UNKNOWN|20|

Consulta simple para obtener un conteo general de los estados generales ordenados por meses de un  año determinado.

<code sql>
SELECT
	CONCAT('',DATE_FORMAT(nagios_statehistory.state_time,'%M')) AS TEMPS,
	ELT(nagios_statehistory.state+1,'OK','WARNING','CRITICAL','UNKNOWN') AS ETAT,
	CONCAT('',COUNT(nagios_statehistory.state)) AS QUANTITE
FROM
	`nagios_statehistory`
	LEFT JOIN nagios_objects AS obj1 ON nagios_statehistory.object_id=obj1.object_id
	LEFT JOIN nagios_instances ON nagios_statehistory.instance_id=nagios_instances.instance_id
WHERE
	obj1.objecttype_id='2'
	AND nagios_statehistory.state_type = 1
	AND DATE_FORMAT(nagios_statehistory.state_time,'%Y') = 2010
GROUP BY
	TEMPS, ETAT
ORDER BY
	DATE_FORMAT(nagios_statehistory.state_time,'%m'),nagios_statehistory.state
```

Resultando en una tabla como esta

| TEMPS    | ETAT     | QUANTITE |
|----------|----------|----------|
| November | OK       | 1754     |
| November | WARNING  | 13       |
| November | CRITICAL | 84       |
| November | UNKNOWN  | 20       |

Trigger para guardar el último estado de los objetos

Nagios no guarda en ninguna tabla de MySQL el último estado de los objetos, pero guarda los cambios de estado en la tabla nagios_statehistory. Para mantener el último estado se puede usar un trigger:

```sql>
Ese trigger hace un update así que hay que cronear esto para mantener al día con los objetos la tabla.

<code sql>
insert into last_state
        select obj.object_id, 0
        from nagios.nagios_objects as obj
        where obj.object_id not in
                (select object_id from last_state);
```

Luego estas consultas podemos integrarlas en PHP o Perl, y obtener resultados que necesitemos para reflejarlos en un visor web, o en los resultados de un plugin.

**NOTA:** Importante el modulo mysql de PHP no soporta la consulta a los PROCEDURE de MySQL, para ello deberemos utilizar el modulo mysqli de PHP, otra cosa tambien importante es que mysqli soporta conecciones persistentes, asi que para evitarse tiempo de implementacion a veces es necesario o implementar un script en Perl, o hacer que el resultado del PROCEDURE se almacene en una tabla temporal y realizar una consulta simple desde ahi.
