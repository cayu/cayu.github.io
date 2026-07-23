---
title: "Sistema de LOG centralizado"
date: 2010-03-12T14:44:31-03:00
---

## Introducción

Se planteo la necesidad de un sistema que reciba y almacenes archivos de registros determinados de n\* servidores. Que se disponga de una interfaz de acceso a esos datos, para la organizacion y procesamiento de los mismos, con lo cual se podran detectar eventos, resolver situaciones y emitir distintos tipos de alarmas.

#### Generalidades de un sistema de log.

*Log es el registro de acciones y eventos que tienen lugar en un sistema.*

*Los logs son el primer registro de la actividad en los sistemas y redes.*

*Los logs de un sistema son una parte primaria de la seguridad y pueden ser usados en la detección de ataques e intrusiones, así como en el análisis de fallas de hardware y software.*

El programa syslog, es una interface que provee un framework standard para que tanto programas como el mismo sistema operativo puedan generar mensajes que peuden ser almacenados localmente o ser enviados a un host remoto. Originalmente escrito para Unix, se convirtió en un standard que se usa en muchos sistemas operativos y dispositivos de red.

**¿ Cual es la utilidad de un sistema de syslog centralizado ?**

En un sistema de syslog centralizado, un server común recibe todos los mensajes de syslog de todos los sistemas de la red. Esto incluye logs de los servers Unix/linux/Windows etc, firewalls, y dispositivos de red (routers, switches, etc)

Hay varias ventajas de un sistema de syslog centralizado

- El syslog puede ser conectado en un segmento de red diferente protegido por un firewall

para mantener más segura la información

- Teniendo los mensajes de todos los equipos, puede hacerse una correlación de ataques o

fallas en distintos puntos de una manera mucho más sencilla. Por ejemplo si en el syslog aparece un mensaje de desconexión de la interface de red de varios servers en el mismo momento, es lógico suponer una falla en el switch donde estos servers estan conectados.

- Un usuario no deseado que haya ingresado en un server, no podrá alterar los mensajes que

se hayan almacenado en el server central.

- Se pueden generar alertas usando sistemas de monitoreo de logs.

##### Sistema de monitoreo de logs

El análisis de logs es una herramienta muy importante para mantener el control de servers y dispositivos de red.

Sin embargo esta es una de las tareas que más tiempo consume y por consiguiente que menos se hace.

Con la cantidad de mensajes informativos que se generan en un sistema de log, detectar en forma manual los mensajes de problemas es muy dificultoso y con mucha probabilidad de error. Esto se vé aumentado cuando se usa un sistema de syslog centralizado, donde la información proviene de varias fuentes distintas. Muchas soluciones de monitoreo se basan en sumarizar la información de archivos de log de dias previos.

Esto es muy útil para la generación de estadísticas y análisis posterior a una falla o intrusión, pero no tanto para la resolución de problemas.

Un administrador no puede actuar en forma proactiva, previamente a que el error ocurra.

Muchas fallas o accesos no autorizados se ven precedidos por mensajes que de haber sido detectados, podrían haber permitido tomar acciones preventivas.

Por ejemplo, un acceso no autorizado via ssh, puede haber estado precedido por una gran cantidad de intentos fallidos de acceso.

Disponer una solución online de monitoreo, permite disponer de herramientas que pueden ayudar a prevenir problemas graves antes que ocurran.

Detectar eventos en el momento que ocurren permite generar acciones en ese mismo instante y no luego de las consecuencias.

Siguiendo con el ejemplo del acceso ssh, podría bloquearse el acceso ssh desde determinada dirección IP despues de un número de intentos fallidos de acceso.

Un concepto que aparece aquí es el de correlación de eventos.

Un sistema automatizado de análisis de logs que pueda hacer una correlación de varios eventos simplifica y acelera el monitoreo de eventos consolidando alertas y mensajes de error en un mensaje más corto y fácil de entender.

Una serie de operaciones están relacionadas con la correlación de eventos.

Compresión toma varias apariciones del mismo evento y se examina la duplicación de información, se remueve las redundacias y se reporta como un único evento. De esta manera 1000 mensajes “route failed” se convierte en un único alerta que dice “route failed 1000 times” Recuento reporta un número específico de eventos similares como uno solo. Esto se diferencia de la compresión en que no solo cuenta en que sea el mismo evento, sino que se supere un determinado umbral para generar un reporte.

Supresión asocia prioridades con alarmas y permite que el sistema suprima un alarma de prioridad más baja si ha ocurrido un evento de prioridad mayor.

Generalización asocia alarmas con eventos de más alto nivel que son los que son reportados.

Esto puede ser útil para por ejemplo para correlacionar eventos de múltiples discos de un array.

No se necesita ver cada mensaje específico si se puede determinar que el array completo tiene problemas.

Correlación basada en tiempo puede ser útil estableciendo causalidad. A menudo una información puede ser obtenida relacionando eventos que tienen una relación temporal específica.

**Ejemplos genéricos:**

- El Evento A está seguido del Evento B.

<!-- -->

- Este es el primer Evento A desde el Evento B reciente.

<!-- -->

- El Evento A sigue al Evento B dentro de los dos minutos.

<!-- -->

- El Evento A no fue observado dentro del Intervalo I.

## Objetivos

Simplificar y optimizar la administración de diferentes servicios para conocer su estado minuto a minuto, y elaborar planes de acción. A su vez el sistema debe ser simple de utilizar por el administrador.

Además se presentaron requerimientos extra como:

- Rotacion diaria de Logs
- Compresión de los mismos
- Guardar los ultimos 60 días de registros
- Ver via web los registros del sistema, realizar busquedas etc

## Relevamiento y análisis

Se determino que la mejor solución adaptable a la magnitud del proyecto y sus plazos de tiempo, fuera la implementación del software libre syslog-ng debido a que a poseía todas las caracteristicas requeridas por el cliente:

- Loggueo por Red
- Su organización por {directorio con nombre de host}/{archivo_log.log}
- Facil configuracion de rotacion
- Proceso de los archivos logs por medio de expresiones regulares
- Posterior ejecución de un comando en base del resultado de la expresión regular

## Sobre Syslog-NG

Syslog-NG es un sistema para el envío de mensajes de registro en una red.

Es útil registrar, por ejemplo:

* Un intento de acceso con contraseña equivocada
* Un acceso correcto al sistema
* Anomalías: variaciones en el funcionamiento normal del sistema
* Alertas cuando ocurre alguna condición especial
* Información sobre las actividades del sistema operativo
* Errores del hardware o el software

También es posible registrar el funcionamiento normal de los programas; por ejemplo, guardar cada acceso que se hace a un servidor web, aunque esto suele estar separado del resto de alertas.

Puede logguear tanto por UDP como por TCP, teniendo compatibilidad con el viejo syslog, soportando a su vez muchas mas opciones y tareas que el syslog comun.

##### Explicacion tecnica simple

Syslog-NG se compone en capas de funcionamiento

![syslog-ng.png](/notas/syslog-ng.png)

Osea tengo en primer lugar opciopnes generales, luego fuentes de donde obtener datos de los registros, ya sean locales, externos por red (udp, tcp, archivos de texto), y luego tenemos destinos y filtros configurados, uniendolos forman los registros de sistema

## Tareas

Se instalo el software syslog-ng dentro de un servidor Debian Etch.

```
apt-get install syslog-ng
```

En la instalacion como resultante se tiene el siguiente archivo de configuracion principal

**/etc/syslog-ng/syslog-ng.conf**

##### Clientes

En los clientes se procedió a configurar el syslog convencional para que loggueara por UDP hacia el servidor syslog.

**/etc/syslog.conf**

*Configuracion para loguear datos de autenticacion*

```
auth.*;authpriv.*;auth.notice;auth.error;auth.info;authpriv.none;     @{ip syslog}
```

**/etc/syslog-ng/syslog-ng.conf**

```
destination df_auth { tcp( {ip syslog} port(514) ); };
filter f_auth { facility(auth, authpriv); };
log { source(src); filter(f_auth); destination(df_auth); };
```

### Configuración en clientes Linux

Archivo /etc/syslog.conf

```
*.*;mail.none;     @130.29.10.155
news.none;     @130.29.10.155
*.debug;*.info;     @130.29.10.155
auth.notice;     @130.29.10.155
kern.warning;     @130.29.10.155
*.err;authpriv.none;     @130.29.10.155
local0,local1.*;     @130.29.10.155
local2,local3.*;     @130.29.10.155
local4,local5.*;     @130.29.10.155
local6,local7.*;     @130.29.10.155
```

### Configuración del Servidor

Debian GNU/Linux Etch

Archivo /etc/syslog-ng/syslog-ng.conf

Opciones generales, fuentes de donde obtener información, filtros y destinos paracada filtro

```
options { sync(0); time_reopen(10); log_fifo_size(100); };

source src {
        unix-stream("/dev/log");
        tcp();
        udp();
        internal();
};

# eliminate pump renewals - pump makes lots of noise in the debug priority
filter pump_renewal {
   not program("pumpd") or not level(info,debug) ;
};

# imapd and ipop3d are noisy - remove their debug priority logs
filter imap_debug {
   not program("imapd") or not level(info,debug) ;
};
filter ipop3_debug {
   not program("ipop3d") or not level(info,debug);
};

# eliminate sshd debug messages
filter sshd_debug {
   not program("sshd") or not level(debug);
};

# eliminate ALL debug messages
filter no_debug { not level(debug); };

# eliminate messages below warning level
filter at_least_warn {
   level(warning..emerg) ;
};

# stuff that would go into /var/log/messages
filter f_messages { level(info..warn)
                    and not facility(auth, authpriv, mail, news, local7);
  };

filter f_bootlog { facility(local7); };
filter f_authlog { facility(auth,authpriv); };
filter f_maillog { facility(mail); };
filter f_cronlog { facility(cron); };
filter f_kernlog { facility(kern); };
filter f_daemonlog { facility(daemon) and
                     level(info,warn,notice,err,crit,alert,emerg);
     };

log { source(src);
      filter(f_messages);
      destination(d_mysql);
      };

log { source(src);
      filter(f_bootlog);
      destination(d_mysql);
      };

log { source(src);
      filter(f_maillog);
      destination(d_mysql);
      };

log { source(src);
      filter(f_authlog);
      destination(d_mysql);
      };

log { source(src);
      filter(f_cronlog);
      destination(d_mysql);
      };

log { source(src);
      filter(f_kernlog);
      destination(d_mysql);
      };

log { source(src);
      filter(f_daemonlog);
      destination(d_mysql);
};

log { source(src);
      filter(sshd_debug); filter(no_debug); filter(pump_renewal);
      destination(d_mysql);      
};

destination d_mysql { 
      program("/usr/bin/mysql --user=root syslog < /var/log/mysql.pipe");
      pipe ("/var/log/mysql.pipe" template("INSERT INTO logs (host, facility, priority, level, tag, datetime, program, msg) VALUES ( '$HOST', '$FACILITY', '$PRIORITY', '$LEVEL', '$TAG', '$YEAR-$MONTH-$DAY $HOUR:$MIN:$SEC','$PROGRAM', '$MSG' );\n")
      template-escape(yes)); 
};
```

Para poder realizar la insercion de datos en una base MySQL, debemo ejecutar el siguiente comando “mkfifo /var/log/mysql.pipe”

### PHP-Syslog-ng

*Para la visualizacion de los logs via web, busqueda y demas operaciones para su administracion se instalo, configuro y modifico a necesidad parte de codigo del sofware php-syslog-ng que provee de una interfaz web con soporte de busquedas y que servia para cubrir la necesidad planteada.*

Una vez instalado se configuro el apache para su puesta en marcha de la siguiente manera

##### /etc/apache2/sites-available/default

```
NameVirtualHost *:80
NameVirtualHost *:443
<VirtualHost *:80>
RewriteEngine On
RewriteCond %{SERVER_PORT} !^443$
#RewriteCond %{REQUEST_URI} !^/$
RewriteRule ^/$ https://%{SERVER_NAME}/syslog [L,R]

    
        ServerAdmin webmaster@localhost

        DocumentRoot /var/www
        <Directory />
                Options FollowSymLinks
                AllowOverride None
        </Directory>
        <Directory /var/www/syslog>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride None
                Order allow,deny
                allow from all
        </Directory>

        ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
        <Directory "/usr/lib/cgi-bin">
                AllowOverride None
                Options ExecCGI -MultiViews +SymLinksIfOwnerMatch
                Order allow,deny
                Allow from all
        </Directory>

        ErrorLog /var/log/apache2/error.log

        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel warn

        CustomLog /var/log/apache2/access.log combined
        ServerSignature On


<Directory "/var/www/syslog/scripts">
    Deny from all
</Directory>
<Directory "/var/www/syslog/includes">
    Deny from all
</Directory>
<Directory "/var/www/syslog/config">
    Deny from all
</Directory>

</VirtualHost>

<VirtualHost *:443>
SSLEngine on
SSLCertificateFile /etc/apache2/apache.pem
#SSLCertificateKeyFile /etc/apache2/ssl.key/server.key
    
        ServerAdmin webmaster@localhost

        DocumentRoot /var/www
        <Directory />
                Options FollowSymLinks
                AllowOverride None
        </Directory>
        <Directory /var/www/syslog>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride None
                Order allow,deny
                allow from all
                # This directive allows us to have apache2's default start page
                # in /apache2-default/, but still have / go to the right place
                SSLRequireSSL
        </Directory>

        ErrorLog /var/log/apache2/error.log

        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel warn

        CustomLog /var/log/apache2/access.log combined
        ServerSignature On


<Directory "/var/www/syslog/scripts">
    Deny from all
</Directory>
<Directory "/var/www/syslog/includes">
    Deny from all
</Directory>
<Directory "/var/www/syslog/config">
    Deny from all
</Directory>

</VirtualHost>
```

### Notas

Ejemplo de como habilitar o deshabilitar un servicio al inicio de un sistema SuSE por medio de su utilidad insserv.

Con el comando “insserv -d servicio” ponemos a ejecutar por default X servicio, en cambio con insserv -r lo removemos de la lista de ejecucion
{{% hint danger %}}
Documentacion desactualizada
{{% /hint %}}
Sergio Cayuqueo
