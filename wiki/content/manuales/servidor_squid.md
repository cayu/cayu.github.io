---
title: "Squid Proxy Server"
date: 2012-04-16T12:39:30-03:00
---

Squid es un popular programa de software libre que implementa un servidor proxy y un dominio para caché de páginas web, publicado bajo licencia GPL. Tiene una amplia variedad de utilidades, desde acelerar un servidor web, guardando en caché peticiones repetidas a DNS y otras búsquedas para un grupo de gente que comparte recursos de la red, hasta caché de web, además de añadir seguridad filtrando el tráfico. Está especialmente diseñado para ejecutarse bajo entornos tipo Unix. Squid ha sido desarrollado durante muchos años y se le considera muy completo y robusto. Aunque orientado a principalmente a HTTP y FTP es compatible con otros protocolos como Internet Gopher. Implementa varias modalidades de cifrado como TLS, SSL, y HTTPS.

## Características

Squid posee las siguientes características:

**Proxy y Caché de HTTP, FTP, y otras URL**

-  Squid proporciona un servicio de Proxy que soporta peticiones HTTP, HTTPS y FTP a equipos que necesitan acceder a Internet y a su vez provee la funcionalidad de caché especializado en el cual almacena de forma local las páginas consultadas recientemente por los usuarios. De esta forma, incrementa la rapidez de acceso a los servidores de información Web y FTP que se encuentra fuera de la red interna.

**Proxy para SSL**

-  Squid también es compatible con SSL (Secure Socket Layer) con lo que también acelera las transacciones cifradas, y es capaz de ser configurado con amplios controles de acceso sobre las peticiones de usuarios.

**Jerarquías de caché**

-  Squid puede formar parte de una jerarquía de caches. Diversos proxys trabajan conjuntamente sirviendo las peticiones de las páginas. Un navegador solicita siempre las páginas a un sólo proxy, si este no tiene la página en la caché hace peticiones a sus hermanos, que si tampoco las tienen las hacen a su/s padre/s… Estas peticiones se pueden hacer mediante dos protocolos: HTTP e ICMP. ICP, HTCP, CARP, caché digests.
  -  Squid sigue los protocolos ICP, HTCP, CARP y caché digests que tienen como objetivo permitir a un proxy “preguntarle” a otros proxys caché si poseen almacenado un recurso determinado.

**Caché transparente**

-  Squid puede ser configurado para ser usado como proxy transparente de manera que las conexiones son enrutadas dentro del proxy sin configuración por parte del cliente, y habitualmente sin que el propio cliente conozca de su existencia. De modo predefinido Squid utiliza el puerto 3128 para atender peticiones, sin embargo se puede especificar que lo haga en cualquier otro puerto disponible o bien que lo haga en varios puertos disponibles a la vez.

**WCCP**

-  A partir de la versión 2.3 Squid implementa WCCP (Web Cache Control Protocol). Permite interceptar y redirigir el trafico que recibe un router hacia uno o más proxys caché, haciendo control de la conectividad de los mismos. Además permite que uno de los proxys caché designado pueda determinar como distribuir el tráfico redirigido a lo largo de todo el array de proxys caché.

**Control de acceso**

-  Ofrece la posibilidad de establecer reglas de control de acceso. Esto permite establecer políticas de acceso en forma centralizada, simplificando la administración de una red.

**Aceleración de servidores HTTP**

-  Cuando un usuario hace petición hacia un objeto en Internet, este es almacenado en el caché, si otro usuario hace petición hacia el mismo objeto, y este no ha sufrido modificación alguna desde que lo accedió el usuario anterior, Squid mostrará el que ya se encuentra en el caché en lugar de volver a descargarlo desde Internet.
  -  Esta función permite navegar rápidamente cuando los objetos ya están en el caché y además optimiza enormemente la utilización del ancho de banda.

**SNMP**

-  Squid permite activar el protocolo SNMP, este proporciona un método simple de administración de red, que permite supervisar, analizar y comunicar información de estado entre una gran variedad de máquinas, pudiendo detectar problemas y proporcionar mensajes de estados.

**Caché de resolución DNS**

-  Squid está compuesto también por el programa dnsserver, que se encarga de la búsqueda de nombres de dominio. Cuando Squid se ejecuta, produce un número configurable de procesos dnsserver, y cada uno de ellos realiza su propia búsqueda en DNS. De este modo, se reduce la cantidad de tiempo que la caché debe esperar a estas búsquedas DNS.

## Protocolo HTTP

El procotolo HTTP se basa principalmente en pedir objetos por medio de mensajes, los cuales se cachean individualmente. Entonces si un objeto esta incluido en dos o mas paginas, este puede ser reutilizado desde el cache.

### Mensaje de pedido

El **mensaje de pedido** esta compuesto por diferentes partes, *comandos* y *encabezados*.

Por ejemplo

```
GET http://cayu.com.ar HTTP/1.0 Accept : Text/html If-Modified-Since : Saturday, 11-January-2011 11:32:11 GMT User-Agent : Mozilla/5.0 (X11; U; Linux i686; en-US) AppleWebKit/534.16 (KHTML, like Gecko) Chrome/10.0.648.204 Safari/534.16
```

#### Comandos

| Comando | Descripción                                                       |
|---------|-------------------------------------------------------------------|
| GET     | Solicita el recurso ubicado en la URL especificada                |
| HEAD    | Solicita el encabezado del recurso ubicado en la URL especificada |
| POST    | Envía datos al programa ubicado en la URL especificada            |
| PUT     | Envía datos a la URL especificada                                 |
| DELETE  | Borra el recurso ubicado en la URL especificada                   |

#### Encabezados

| Nombre del encabezado | Descripción |
|----|----|
| Accept | Tipo de contenido aceptado por el navegador |
| Accept-Charset | Juego de caracteres que el navegador espera |
| Accept-Encoding | Codificación de datos que el navegador acepta |
| Accept-Language | Idioma que el navegador espera |
| Authorization | Identificación del navegador en el servidor |
| Content-Encoding | Tipo de codificación para el cuerpo de la solicitud |
| Content-Language | Tipo de idioma en el cuerpo de la solicitud |
| Content-Length | Extensión del cuerpo de la solicitud |
| Content-Type | Tipo de contenido del cuerpo de la solicitud |
| Date | Fecha en que comienza la transferencia de datos |
| Forwarded | Utilizado por equipos intermediarios entre el navegador y el servidor |
| Link | Vínculo entre dos direcciones URL |
| Referer | Dirección URL desde la cual se realizó la solicitud |
| User-Agent | Cadena con información sobre el cliente, como versiones de navegador y sistema operativo |

### Mensaje de respuesta

Es el mensaje recibido por parte del servidor

Un ejemplo :

```
HTTP/1.0 200 OK Date: Sat, 14 Jan 2011 11:32:12 GMT Server : Apache/1.3 Content-Type : text/HTML Content-Length : 1245
```

#### Códigos de respuesta

| Código | Mensaje | Descripción |
|----|----|----|
| 200 | OK | La solicitud se llevó a cabo de manera correcta |
| 201 | CREATED | Sigue a un comando POST e indica el éxito, la parte restante del cuerpo indica la dirección URL donde se ubicará el documento creado recientemente |
| 202 | ACCEPTED | La solicitud ha sido aceptada, pero el procedimiento que sigue no se ha llevado a cabo |
| 203 | PARTIAL | INFORMATION Cuando se recibe este código en respuesta a un comando de GET indica que la respuesta no está completa |
| 204 | NO RESPONSE | El servidor ha recibido la solicitud, pero no hay información de respuesta |
| 205 | RESET CONTENT | El servidor le indica al navegador que borre el contenido en los campos de un formulario |
| 206 | PARTIAL CONTENT | Es una respuesta a una solicitud que consiste en el encabezado range. El servidor debe indicar el encabezado content-Range |
| 301 | MOVED | Los datos solicitados han sido transferidos a una nueva dirección |
| 302 | FOUND | Los datos solicitados se encuentran en una nueva dirección URL, pero, no obstante, pueden haber sido trasladados |
| 303 | METHOD | Significa que el cliente debe intentarlo con una nueva dirección; es preferible que intente con otro método en vez de GET |
| 304 | NOT MODIFIED | Si el cliente llevó a cabo un comando GET condicional (con la solicitud relativa a si el documento ha sido modificado desde la última vez) y el documento no ha sido modificado, este código se envía como respuesta |
| 400 | BAD REQUEST | La sintaxis de la solicitud se encuentra formulada de manera errónea o es imposible de responder |
| 401 | UNAUTHORIZED | Los parámetros del mensaje aportan las especificaciones de formularios de autorización que se admiten. El cliente debe reformular la solicitud con los datos de autorización correctos |
| 402 | PAYMENT REQUIRED | El cliente debe reformular la solicitud con los datos de pago correctos |
| 403 | FORBIDDEN | El acceso al recurso simplemente se deniega |
| 404 | NOT FOUND | El servidor no halló nada en la dirección especificada. Se ha abandonado sin dejar una dirección para redireccionar |
| 500 | INTERNAL ERROR | El servidor encontró una condición inesperada que le impide seguir con la solicitud |
| 501 | NOT IMPLEMENTED | El servidor no admite el servicio solicitado |
| 502 | BAD GATEWAY | El servidor que actúa como una puerta de enlace o proxy ha recibido una respuesta no válida del servidor al que intenta acceder |
| 503 | SERVICE UNAVAILABLE | El servidor no puede responder en ese momento debido a que se encuentra congestionado (todas las líneas de comunicación se encuentran congestionadas, inténtelo de nuevo más adelante) |
| 504 | GATEWAY TIMEOUT | La respuesta del servidor ha llevado demasiado tiempo en relación al tiempo de espera que la puerta de enlace podía admitir |

## Proxy Web

El proxy caché es una manera de guardar los objetos solicitados de Internet (por ejemplo, datos como páginas web) disponibles vía protocolos HTTP, FTP y Gopher en un sistema más cercano al lugar donde se piden. Los navegadores web pueden usar la caché local Squid como un servidor proxy HTTP, reduciendo el tiempo de acceso así como el consumo de ancho de banda.

Esto es muchas veces útil para los proveedores de servicios de Internet para incrementar la velocidad de sus consumidores y para las redes de área local que comparten la conexión a Internet.

Squid tiene algunas características que pueden facilitar establecer conexiones anónimas. Características tales como eliminar o modificar campos determinados de la cabecera de peticiones HTTP de los clientes. Esta política de eliminación y alteración de cabeceras se establece en la configuración de Squid. El usuario que solicita páginas a través de una red que utiliza Squid de forma transparente, normalmente no es consciente de este proceso o del registro de información relacionada con el proceso.

### Ejemplo 1: Un cliente accediendo a un sitio HTTP

Peticion HTTP del browser al proxy:

```
GET http://www.google.com.ar/ HTTP/1.0.
User-Agent: Wget/1.10.2.
Accept: */*.
Host: www.google.com.ar.
```

Repuesta HTTP que viene del proxy:

```
HTTP/1.0 200 OK
Date: Mon, 11 Apr 2011 18:29:40 GMT
Expires: -1
Cache-Control: private, max-age=0
Content-Type: text/html; charset=ISO-8859-1
Set-Cookie: PREF=ID=254d91dfb90cf60a:FF=0:TM=1302546580:LM=1302546580:S=duVgVs06FzQnoCCm; expires=Wed, 10-Apr-2013 18:29:40 GMT; path=/; domain=.google.com.ar
Set-Cookie: NID=45=vuPgKmuchyxsG7F3TLbslY9ct2oGYjvUQ4F5e3mYwzKHABfMfStVkmnALugH1VSNkWbGISJ4Hr-xw_w9qXlmYn8qvcCg6GzuhZv89VDqU4aDpptEQLM1pNpcMS4o9VPm; expires=Tue, 11-Oct-2011 18:29:40 GMT; path=/; domain=.google.com.ar; HttpOnly
Server: gws
X-XSS-Protection: 1; mode=block
X-Cache: MISS from proxy.cayu.com.ar
X-Cache-Lookup: MISS from proxy.cayu.com.ar:8080
Via: 1.0 proxyfree:8080 (squid/2.6.STABLE21)
Proxy-Connection: close
```

### Ejemplo 2: Un cliente accediendo a un sitio FTP

Mensaje del browser al proxy:

```
GET ftp://ftp.gnu.org/ HTTP/1.0.
User-Agent: Wget/1.10.2.
Accept: */*.
Host: ftp.gnu.org.
```

Respuesta del proxy:

```
HTTP/1.0 200 OK
Server: squid/2.6.STABLE21
Date: Mon, 11 Apr 2011 18:40:08 GMT
Content-Type: text/html
X-Cache: MISS from proxy.cayu.com.ar
X-Cache-Lookup: MISS from proxy.cayu.com.ar:8080
Via: 1.0 proxyfree:8080 (squid/2.6.STABLE21)
Proxy-Connection: close
```

### Ejemplo 3: Un cliente accediendo a un sitio HTTPS

Mensaje del cliente:

```
CONNECT gmail.google.com:443 HTTP/1.0
User-Agent: Wget/1.10.2.
```

Respuesta del proxy:

```
HTTP/1.1 200 Connection Established.
```

Este método arma un túnel a través del proxy. Como el tráfico esta cifrado, Squid no puede hacer nada con esos datos.

## Requisitos del sistema

Squid puede ejecutarse en los siguientes Sistemas Operativos:

- AIX
- BSDI
- Digital Unix
- FreeBSD
- HP-UX
- IRIX
- GNU/Linux
- Mac OS X
- NetBSD
- NeXTStep
- OpenBSD
- SCO Unix
- SunOS/Solaris
- Windows NT

Para Windows se utilizan los paquetes Cygwin/GnuWin32. Obs.: Existen, actualmente, paquetes que corren squid en W32 sin necesidad de Cygwin.

## Implementación

### Compilación

Para la puesta en marcha de un servidor Squid, su instalación puede realizar desde paquetes provisto por la distribución:

```
apt-get install squid squid-common
```

O desde el código fuente. Siempre se recomienda la primera por cuestiones de mantenibilidad y upgrades, pero en algunos casos es necesario usar la versión desde las fuentes.

A continuación se muestran los pasos de instalación desde el source.

```
# ./configure --enable-storeio=diskd --prefix=/usr/local --enable-snmp --enable-delay-pools --sysconfdir=/etc/squid --enable-external-acl-helpers=ldap_group --enable-auth=basic --enable-arp-acl --enable-cache-digests --enable-linux-netfilter
# make
# make install
```

El “configure” tiene una serie de opciones para habilitar características especiales del proxy. Algunos ejemplos:

- –enable-snmp, para que Squid muestre OID’s sobre su funcionamiento.
- –enable-auth, para habilitar esquemas de autenticación, por ejemplo:
- –enable-auth=“basic”, para activar autenticación básica.
- –enable-ssl, SSL para proxies reversos.
- –enable-basic-auth-helpers=, para activar alguno/s de los módulos que vienen con squid, LDAP por ejemplo.
- –enable-linux-netfilter Soporte de la capa de Netfilter de Linux para Squid.
- –help, muestra todas las opciones disponibles de compilación.

### Configuración

Por la opción de compilación **–sysconfdir**, el directorio donde se almacenaran los archivos de configuración sera **/etc/squid/**.

#### Servicio

El archivo de configuración principal lo tendremos en */etc/squid/squid.conf*. Este archivo tiene activado una serie de parámetros default para ayudarnos a ponerlo en funcionamiento

El primer paso es vericar el path de los logs y del cache, y también el usuario con el que corre el Squid, observando el valor de los siguientes parámetros (los default se encuentran comentados, pero están activos):

- cache_access_log
  - Archivo de log principal
  - Default : */var/log/squid/access.log*
- cache_dir
  - Directorio donde se almacenara el cache
  - Default : *cache_dir diskd /var/spool/squid 6000 64 256*
- cache_effective_user
  - Usuario propietario del contenido del directorio de cache
- cache_effective_group
  - Grupo propietario del contenido del directorio de cache

Y por ultimo, inicializamos el cache y levantamos el servicio:

```
# /usr/local/squid/sbin/squid -z
# /usr/local/squid/sbin/squid
```

Corriendo también su script de inicio ejecutará automáticamente todos estos pasos :

```
# /etc/init.d/squid
```

Ejemplo de configuración mínima para que arranque el servicio :

```
http_port 3128

hierarchy_stoplist cgi-bin ?

refresh_pattern ^ftp:           1440    20%     10080
refresh_pattern ^gopher:        1440    0%      1440
refresh_pattern -i (/cgi-bin/|\?) 0     0%      0
refresh_pattern .               0       20%     4320

acl all		src 0.0.0.0/0.0.0.0
acl manager 	proto cache_object
acl localhost 	src 127.0.0.1/32 ::1
acl to_localhost dst 127.0.0.0/8 0.0.0.0/32 ::1

acl localnet src 10.0.0.0/8     # RFC 1918 possible internal network
acl localnet src 172.16.0.0/12  # RFC 1918 possible internal network
acl localnet src 192.168.0.0/16 # RFC 1918 possible internal network
acl localnet src fc00::/7       # RFC 4193 local private network range
acl localnet src fe80::/10      # RFC 4291 link-local (directly plugged) machines

acl SSL_ports port 443
acl Safe_ports port 80          # http
acl Safe_ports port 21          # ftp
acl Safe_ports port 443         # https
acl Safe_ports port 70          # gopher
acl Safe_ports port 210         # wais
acl Safe_ports port 1025-65535  # unregistered ports
acl Safe_ports port 280         # http-mgmt
acl Safe_ports port 488         # gss-http
acl Safe_ports port 591         # filemaker
acl Safe_ports port 777         # multiling http
acl CONNECT method CONNECT

http_access allow manager localhost
http_access deny manager
http_access deny !Safe_ports
http_access deny CONNECT !SSL_ports
http_access allow localhost
http_access allow localnet
http_access deny all
```

### Información de Squid

Si queremos ver los mensajes de error de Squid traducidos

```
error_directory	/usr/local/squid/share/errors/Spanish
```

Si queremos ocultar la versión del Squid a mostrar

```
httpd_suppress_version_string	on
```

Si queremos modificar el nombre del host a mostrar en el mensaje de error

```
visible_hostname mihostproxy
```

Enlace interesante: <http://techspalace.blogspot.com/2008/01/remove-squid-footer.html>

#### Información en tiempo real

Por medio del objeto `cache_object://` podemos acceder a la información de ejecución del servicio y programar script para obtención y parseo de la misma.

Ejemplo :

```
# telnet localhost 3128
GET cache_object://localhost/info HTTP/1.0
HTTP/1.0 200 OK
Server: squid
Mime-Version: 1.0
Date: Fri, 15 Apr 2011 15:02:23 GMT
Content-Type: text/plain
Expires: Fri, 15 Apr 2011 15:02:23 GMT
Last-Modified: Fri, 15 Apr 2011 15:02:23 GMT
X-Cache: MISS from proxybuenoaires
Via: 1.0 proxybuenoaires (squid)
Connection: close

Squid Object Cache: Version 3.1.11
Start Time:	Fri, 18 Mar 2011 23:11:52 GMT
Current Time:	Fri, 15 Apr 2011 15:02:23 GMT
Connection information for squid:
	Number of clients accessing cache:	34
	Number of HTTP requests received:	1424751
	Number of ICP messages received:	0
	Number of ICP messages sent:		0
	Number of queued ICP replies:		0
	Number of HTCP messages received:	0
	Number of HTCP messages sent:		0
	Request failure ratio:	 		0.00
	Average HTTP requests per minute since start:	35.8
	Average ICP messages per minute since start:	0.0
	Select loop called: 161640210 times, 14.785 ms avg
Cache information for squid:
	Hits as % of all requests:		5min: 0.0%, 60min: 0.0%
	Hits as % of bytes sent:		5min: -149.6%, 60min: -331.3%
	Memory hits as % of hit requests:	5min: 0.0%, 60min: 0.0%
	Disk hits as % of hit requests:		5min: 0.0%, 60min: 0.0%
	Storage Swap size:		0 KB
	Storage Swap capacity:	 	0.0% used, 100.0% free
	Storage Mem size:		112 KB
	Storage Mem capacity:	 	0.0% used, 100.0% free
	Mean Object Size:		0.00 KB
	Requests given to unlinkd:	0
Median Service Times (seconds)  5 min    60 min:
	HTTP Requests (All):   0.28853  0.19742
	Cache Misses:          0.28853  0.19742
	Cache Hits:            0.00000  0.00000
	Near Hits:             0.00000  0.00000
	Not-Modified Replies:  0.00000  0.00000
	DNS Lookups:           0.00000  0.03868
	ICP Queries:           0.00000  0.00000
Resource usage for squid:
	UP Time:			2389830.846 seconds
	CPU Time:			683.391 seconds
	CPU Usage:			0.03%
	CPU Usage, 5 minute avg:	0.08%
	CPU Usage, 60 minute avg:	0.15%
	Process Data Segment Size via sbrk(): 24284 KB
	Maximum Resident Size: 110864 KB
	Page faults with physical i/o: 3
Memory usage for squid via mallinfo():
	Total space in arena:   24416 KB
	Ordinary blocks:        20766 KB    920 blks
	Small blocks:               0 KB      0 blks
	Holding blocks:           808 KB      3 blks
	Free Small blocks:          0 KB
	Free Ordinary blocks:    3649 KB
	Total in use:           21574 KB 86%
	Total free:              3649 KB 14%
	Total size:             25224 KB
Memory accounted for:
	Total accounted:         5179 KB  21%
	memPool accounted:       5179 KB  21%
	memPool unaccounted:    20044 KB  79%
	memPoolAlloc calls: 433622248
	memPoolFree calls:  436329756
File descriptor usage for squid:
	Maximum number of file descriptors:   1024
	Largest file desc currently in use:    313
	Number of file desc currently in use:  277
	Files queued for open:                   0
	Available number of file descriptors:  747
	Reserved number of file descriptors:   100
	Store Disk files open:                   0
Internal Data Structures:
	    29 StoreEntries
	    29 StoreEntries with MemObjects
	    25 Hot Object Cache Items
	     0 on-disk objects
```

##### Cache manager

Para un manejo mas amigable de esta información tenemos el **Cache manager** de Squid, que es un componente que muestra estadísticas vía web del estado del servicio en tiempo real, como consultas DNS de determinada ip cliente, información del consumo de memoria, cpu, cache y mucho más.

Se componen de diferentes archivos

- **cachemgr.cgi** - Es la utilidad CGI que nos muestra los reportes vía web
- **cachemgr.conf** - Es el archivo de configuración principal del **Cache manager**

##### Archivos de LOG

- **/var/log/squid/access.log**
  - En este archivo se registran todos los pedidos HTTP, siendo sus campos :
  - timestamp de la recepción de la petición
  - Tiempo transcurrido entre la petición y la recepción
  - Dirección IP del cliente
  - Código de resultado HTTP
  - Cantidad de bytes entregados al cliente
  - Método HTTP utilizado - GET, POST, CONNECT
  - URL solicitada
  - Si hay autenticación, usuario que realizo la petición
  - Desde donde se resolvió la petición, DIRECT o desde un PARENT proxy
  - Tipo de contenido mime
- **/var/log/squid/cache.log**
  - Mensajes informativos o de error sobre el cache interno de Squid
- **/var/log/squid/store.log**
  - Información de los objetos que pasaron por el cache
- **/var/log/squid/squidGuard.log**
  - Información general del proceso squidGuard
- **/var/log/squid/squidguard.log**
  - Información del filtrado de reglas de squidGuard

## Controles de acceso

El control de acceso se divide básicamente en dos parametros

- acl
  - Expresión coincidente con algo
- http_access
  - Acción a llevar a cabo con dicha expresión coincidente

Ejemplo :

```
acl 		red_denegada 	src 78.0.0.0/8
http_access 	deny 		red_denegada
http_access 	allow 		all
```

*En este caso estamos indicando que se deniegue cualquier pedido de la red 78.0.0.0/8 y que se permita cualquier pedido de otra red.* Es un caso hipotético igualmente podria aplicarse a una red 192.168.2.0/255.255.255.0.

Lista de opciones mas usuales de la directiva **acl**

- **arp** - *Control por MAC Address*
  - acl ACLARP arp 11:12:13:14:15:16
- **src** - *Control por ip del Cliente*
- **dst** - *Dirección de destino*
- **dstdomain** - *Dominio de destino*
- **time** - *Franja horaria de acceso*
  - acl ACLTIME time M T W H F 9:00-17:00
- **url_regex** - *Expresión regular de destino*
- **port** - *Puerto de destino*
- **proto** - *Procotolo*
  - acl aclname proto HTTP FTP
- **method** - *Método del mensaje de pedido*
  - acl aclname method GET POST CONNECT

Algunos ejemplos :

##### Definición

acl facebook dstdomain facebook.com → es una acl igual a el domino destino facebook.com\
acl mired src 192.168.0.0/24 → es igual a la red 192.168.0.0/24\
acl mp3 url_path_regex \\mp3\$ → es igual a todas las URLs que terminen en .mp3.\

##### Aplicación

http_access allow facebook → permito navegar cuando coincida la ACL llamada facebook\
http_access deny clarin → deniego la navegacion cuando haya coincidencia con la ACL facebook\
http_access allow mired → permito navegar si hay una coincidencia con la ACL mired\

### Autenticacion

Squid puede utilizar diferentes métodos de validación de usuarios, los mismo pueden ser independientes de Squid y se llamados por un helper.

Básicamente el helper nos devolvera **OK** ó **ERR** en base a la información que le hayamos proporcionado

#### LDAP

Ejemplo de configuración para un LDAP común

```
auth_param basic program /usr/lib/squid/squid_ldap_auth -v 3 -b "dc=yourcompany,dc=com" -D uid=some-user,ou=People,dc=yourcompany,dc=com  -w password -f uid=%s ldap.yourcompany.com

auth_param basic children 5
auth_param basic realm Web-Proxy
auth_param basic credentialsttl 1 minute

acl ldap-auth proxy_auth REQUIRED

http_access allow ldap-auth
http_access allow localhost
http_access deny all
```

#### ActiveDirectory W2003 - LDAP

```
auth_param basic program /usr/lib/squid/squid_ldap_auth -v 3 -b ou="something",ou=something,dc=svbmt,dc=net -D cn=LDAPUSER,ou="Generic User Accounts",ou=something",dc=svbmt,dc=net -w password -f sAMAccountName=%s -h ldap.yourcompany.com
```

### Permisos

#### Permisos especiales

##### Bloquear MSN apto para 7 y 8

MSN Messenger 7 y MSN 8 tienen a buscar por todo lados como conectarse, entonces esto se vuelve algo medio problematico con Squid y mas cuando tenemos muchos grupos LDAP configurados y queremos que algunos si puedan acceder etc. Mi solución por el momento es :

```
acl MSN_Method 	method 		POST
acl MSN_Agent 	browser 	^Mozilla.compatible;.MSN Messenger
acl MSN_Agent 	browser 	^Mozilla/4\.0.*MSIE.6\.0.MSN
acl MSN_Mime 	req_mime_type 	^application/x-msn-messenger$
acl MSN_Port 	port 		1863
acl MSN_Dom 	dstdom_regex -i messenger.hotmail.com 207.46.110.40 gateway.messenger.hotmail.com msn.com local-bay.contacts.msn.com messenger.live.com proxy-sn.contacts.msn.com proxy-bay.contacts.msn.com gateway.edge.messenger.live.com bay.messenger.services.live.com geo.messenger.services.live.com
acl MSN_Url 	url_regex -i 	gateway.dll sqmserver.dll ADSAdClient31.dll gateway.dll?
acl MSN_Usr     ext_user        usuario1 usuario2 usuario3
```

```
http_access       deny MSN_Agent MSN_Mime MSN_Dom MSN_Url !MSN_Usr
http_reply_access deny MSN_Agent MSN_Mime MSN_Dom MSN_Url !MSN_Usr

http_access       deny MSN_Method MSN_Url !MSN_Usr
http_reply_access deny MSN_Method MSN_Url !MSN_Usr

http_access       deny CONNECT MSN_Port !MSN_Usr
http_reply_access deny CONNECT MSN_Port !MSN_Usr
```

El reply acces es para forzar a que si o si se cumpla dicha condición, ya que podemos tener una gran cantidad de ACL’s y algunas se pueden superponer, entonces con esto las forzamos.

##### Delay pools

Por ejemplo si tengo usuarios que abusan del uso de internet, dado el caso de que bajan una iso y la dejan horas agotando el ancho de banda, lo que podemos hacer con Squid, es que cuando llegen a un tope, por ejemplo 15MB el ancho de banda baje considerablemente para todo el segmento de red que pasa por squid.

Ejemplo si un usuario baja un archivo ISO, cuando llega a los 15MB la velocidad baja a 16k/s para todo el segmento

```
delay_pools          1
delay_class          1       1
delay_parameters     1       16384/15728640
acl ficheros_iso     url_regex       \.iso$
delay_access         1       allow   ficheros_iso
```

Se puede hacer tambien

**acl restringir url_regex -i “/etc/squid/extensions”**

```
\.ace$

\.af$

\.afx$

\.arj$

\.asf$

\.asx$

\.au$

[...]

\.xla$

\.xls$

\.xlt$

\.xlw$

\.z$

\.zip$ 

youtube.com
```

Delay pool clase 2 para afectar a usuarios individuales

```
delay_pools          1
delay_class          1 2
delay_parameters     1 -1/-1 102400/3000
delay_access         1 allow ficheros_iso
```

Ejemplo de Delay pool por horarios

```
acl winupdate dstdomain .windowsupdate.com
acl peakperiod time 10:00-16:00
delay_pools 1
delay_class 1 1
# 64 Kbit/s
delay_parameters 1 8000/8000
delay_access 1 allow winupdate peakperiod
```

Sacado de : <http://www.linux.com/archive/feature/153221>

Otros ejemplos de delay pools

```
# 157 kB/s
delay_pools 1
delay_class 1 2
delay_parameters 1 98304/131072 49152/131072
delay_access 1 allow all ancho_de_banda
```

```
# 8 kB/s
delay_pools 1
delay_class 		1 1
delay_parameters 	1 8192/8192
delay_access 1 allow all ancho_de_banda
```

```
# 32 kB/s
delay_pools 1
delay_class             1       2
delay_parameters	1 	32768/32768 16384/32768
```

```
# 50 kB/s
delay_pools 1
delay_class             1       2
delay_parameters	1 	65536/65536 32768/65536
delay_access 1 allow all ancho_de_banda
```

**Clases de Delay Pools**

- Clase 1 : Limita el ancho de banda total para todos los que sean afectados por el delay pool
- Clase 2 : Limita el ancho de banda total y por cada usuario
- Clase 3 : Limita en total, por usuario y por red

#### Filtrado

Dentro del archivo **/etc/squid/squid.conf** deberemos incluir lo siguiente :

```
redirect_program	/usr/bin/squidGuard -c /etc/squid/squidguard.conf
redirect_children	6
```

##### Configuración de SquidGuard

**/etc/squid/squidguard.conf**

```
dbhome /var/lib/squidguard/db
logdir /var/log/squid

destination bl_homerepair {
	urllist blacklists/homerepair/urls
	domainlist blacklists/homerepair/domains
	logfile squidguard.log
}

destination bl_gardening {
	urllist blacklists/gardening/urls
	domainlist blacklists/gardening/domains
	logfile squidguard.log
}

destination bl_culinary {
	domainlist blacklists/culinary/domains
	logfile squidguard.log
}

destination bl_porn {
	urllist blacklists/porn/urls
	domainlist blacklists/porn/domains
	expressionlist blacklists/porn/expressions
	logfile squidguard.log
}

destination bl_ringtones {
	domainlist blacklists/ringtones/domains
	logfile squidguard.log
}

destination bl_whitelist {
	urllist blacklists/whitelist/urls
	domainlist blacklists/whitelist/domains
	logfile squidguard.log
}

destination bl_weapons {
	urllist blacklists/weapons/urls
	domainlist blacklists/weapons/domains
	logfile squidguard.log
}

destination bl_kidstimewasting {
	urllist blacklists/kidstimewasting/urls
	domainlist blacklists/kidstimewasting/domains
	logfile squidguard.log
}

destination bl_spyware {
	domainlist blacklists/spyware/domains
	logfile squidguard.log
}

destination bl_audio-video {
	urllist blacklists/audio-video/urls
	domainlist blacklists/audio-video/domains
	logfile squidguard.log
}

destination bl_webmail {
	urllist blacklists/webmail/urls
	domainlist blacklists/webmail/domains
	logfile squidguard.log
}

destination bl_financial {
	domainlist blacklists/financial/domains
	logfile squidguard.log
}

destination bl_weather {
	urllist blacklists/weather/urls
	domainlist blacklists/weather/domains
	logfile squidguard.log
}

destination bl_childcare {
	urllist blacklists/childcare/urls
	domainlist blacklists/childcare/domains
	logfile squidguard.log
}

destination bl_virusinfected {
	urllist blacklists/virusinfected/urls
	domainlist blacklists/virusinfected/domains
	logfile squidguard.log
}

destination bl_chat {
	urllist blacklists/chat/urls
	domainlist blacklists/chat/domains
	logfile squidguard.log
}

destination bl_desktopsillies {
	urllist blacklists/desktopsillies/urls
	domainlist blacklists/desktopsillies/domains
	logfile squidguard.log
}

destination bl_searchengines {
	domainlist blacklists/searchengines/domains
	logfile squidguard.log
}

destination bl_naturism {
	urllist blacklists/naturism/urls
	domainlist blacklists/naturism/domains
	logfile squidguard.log
}

destination bl_aggressive {
	urllist blacklists/aggressive/urls
	domainlist blacklists/aggressive/domains
	logfile squidguard.log
}

destination bl_clothing {
	domainlist blacklists/clothing/domains
	logfile squidguard.log
}

destination bl_ecommerce {
	urllist blacklists/ecommerce/urls
	domainlist blacklists/ecommerce/domains
	logfile squidguard.log
}

destination bl_marketingware {
	domainlist blacklists/marketingware/domains
	logfile squidguard.log
}

destination bl_astrology {
	domainlist blacklists/astrology/domains
	logfile squidguard.log
}

destination bl_jobsearch {
	urllist blacklists/jobsearch/urls
	domainlist blacklists/jobsearch/domains
	logfile squidguard.log
}

destination bl_gambling {
	urllist blacklists/gambling/urls
	domainlist blacklists/gambling/domains
	logfile squidguard.log
}

destination bl_updatesites {
	urllist blacklists/updatesites/urls
	domainlist blacklists/updatesites/domains
	logfile squidguard.log
}

destination bl_ads {
	urllist blacklists/ads/urls
	domainlist blacklists/ads/domains
	expressionlist blacklists/ads/expressions
	logfile squidguard.log
}

destination bl_entertainment {
	urllist blacklists/entertainment/urls
	domainlist blacklists/entertainment/domains
	logfile squidguard.log
}

destination bl_cleaning {
	domainlist blacklists/cleaning/domains
	logfile squidguard.log
}

destination bl_artnudes {
	urllist blacklists/artnudes/urls
	domainlist blacklists/artnudes/domains
	logfile squidguard.log
}

destination bl_vacation {
	urllist blacklists/vacation/urls
	domainlist blacklists/vacation/domains
	logfile squidguard.log
}

destination bl_sports {
	urllist blacklists/sports/urls
	domainlist blacklists/sports/domains
	logfile squidguard.log
}

destination bl_blog {
	urllist blacklists/blog/urls
	domainlist blacklists/blog/domains
	logfile squidguard.log
}

destination bl_religion {
	urllist blacklists/religion/urls
	domainlist blacklists/religion/domains
	logfile squidguard.log
}

destination bl_mixed_adult {
	domainlist blacklists/mixed_adult/domains
	logfile squidguard.log
}

destination bl_frencheducation {
	urllist blacklists/frencheducation/urls
	domainlist blacklists/frencheducation/domains
	logfile squidguard.log
}

destination bl_pets {
	urllist blacklists/pets/urls
	domainlist blacklists/pets/domains
	logfile squidguard.log
}

destination bl_mobile-phone {
	domainlist blacklists/mobile-phone/domains
	logfile squidguard.log
}

destination bl_mail {
	urllist blacklists/mail/urls
	domainlist blacklists/mail/domains
	logfile squidguard.log
}

destination bl_dating {
	urllist blacklists/dating/urls
	domainlist blacklists/dating/domains
	logfile squidguard.log
}

destination bl_guns {
	urllist blacklists/guns/urls
	domainlist blacklists/guns/domains
	logfile squidguard.log
}

destination bl_verisign {
	domainlist blacklists/verisign/domains
	logfile squidguard.log
}

destination bl_cellphones {
	domainlist blacklists/cellphones/domains
	logfile squidguard.log
}

destination bl_beerliquorinfo {
	domainlist blacklists/beerliquorinfo/domains
	logfile squidguard.log
}

destination bl_onlinepayment {
	domainlist blacklists/onlinepayment/domains
	logfile squidguard.log
}

destination bl_government {
	urllist blacklists/government/urls
	domainlist blacklists/government/domains
	logfile squidguard.log
}

destination bl_drugs {
	urllist blacklists/drugs/urls
	domainlist blacklists/drugs/domains
	logfile squidguard.log
}

destination bl_beerliquorsale {
	domainlist blacklists/beerliquorsale/domains
	logfile squidguard.log
}

destination bl_hygiene {
	domainlist blacklists/hygiene/domains
	logfile squidguard.log
}

destination bl_onlineauctions {
	urllist blacklists/onlineauctions/urls
	domainlist blacklists/onlineauctions/domains
	logfile squidguard.log
}

destination bl_warez {
	urllist blacklists/warez/urls
	domainlist blacklists/warez/domains
	logfile squidguard.log
}

destination bl_dialers {
	urllist blacklists/dialers/urls
	domainlist blacklists/dialers/domains
	logfile squidguard.log
}

destination bl_antispyware {
	urllist blacklists/antispyware/urls
	domainlist blacklists/antispyware/domains
	logfile squidguard.log
}

destination bl_adult {
	urllist blacklists/adult/urls
	domainlist blacklists/adult/domains
	logfile squidguard.log
}

destination bl_instantmessaging {
	urllist blacklists/instantmessaging/urls
	domainlist blacklists/instantmessaging/domains
	logfile squidguard.log
}

destination bl_news {
	urllist blacklists/news/urls
	domainlist blacklists/news/domains
	logfile squidguard.log
}

destination bl_socialnetworking {
	urllist blacklists/socialnetworking/urls
	domainlist blacklists/socialnetworking/domains
	logfile squidguard.log
}

destination bl_jewelry {
	domainlist blacklists/jewelry/domains
	logfile squidguard.log
}

destination bl_filehosting {
	domainlist blacklists/filehosting/domains
	logfile squidguard.log
}

destination bl_reaffected {
	domainlist blacklists/reaffected/domains
	logfile squidguard.log
}

destination bl_phishing {
	urllist blacklists/phishing/urls
	domainlist blacklists/phishing/domains
	logfile squidguard.log
}

destination bl_medical {
	urllist blacklists/medical/urls
	domainlist blacklists/medical/domains
	logfile squidguard.log
}

destination bl_violence {
	urllist blacklists/violence/urls
	domainlist blacklists/violence/domains
	logfile squidguard.log
}

destination bl_personalfinance {
	urllist blacklists/personalfinance/urls
	domainlist blacklists/personalfinance/domains
	logfile squidguard.log
}

destination bl_sportnews {
	urllist blacklists/sportnews/urls
	domainlist blacklists/sportnews/domains
	logfile squidguard.log
}

destination bl_banking {
	urllist blacklists/banking/urls
	domainlist blacklists/banking/domains
	logfile squidguard.log
}

destination bl_sexuality {
	urllist blacklists/sexuality/urls
	domainlist blacklists/sexuality/domains
	logfile squidguard.log
}

destination bl_games {
	urllist blacklists/games/urls
	domainlist blacklists/games/domains
	logfile squidguard.log
}

destination bl_sect {
	domainlist blacklists/sect/domains
	logfile squidguard.log
}

destination bl_onlinegames {
	urllist blacklists/onlinegames/urls
	domainlist blacklists/onlinegames/domains
	logfile squidguard.log
}

destination bl_hacking {
	urllist blacklists/hacking/urls
	domainlist blacklists/hacking/domains
	logfile squidguard.log
}

destination bl_shopping {
	domainlist blacklists/shopping/domains
	logfile squidguard.log
}

destination bl_radio {
	urllist blacklists/radio/urls
	domainlist blacklists/radio/domains
	logfile squidguard.log
}

destination bl_proxy {
	urllist blacklists/proxy/urls
	domainlist blacklists/proxy/domains
	logfile squidguard.log
}



destination excepciones {
	expressionlist	excepciones.destexprlist
	urllist		excepciones.desturllist
	domainlist	excepciones.destdomainlist
	logfile 	squidguard.log
}

destination Restricciones_a_mano {
	urllist		Restricciones_a_mano.desturllist
	domainlist	Restricciones_a_mano.destdomainlist
	expressionlist	Restricciones_a_mano.destexprlist
	logfile 	squidguard.log
}

src admin {
    ip
}
	    

acl {
	default {
		pass excepciones !denegados_a_mano !bl_audio-video !bl_proxy !bl_warez !bl_porn !bl_filehosting !bl_adult
		redirect http://localhost/Accesodenegado.php?url=%u
	}
}
```

En el apartado **redirect** indicamos al squidGuard que nos muestre un contenido especial cuando intentamos acceder a ubicaciones filtradas. Cuando vemos **%u** nos referimos a la variable de url, y podemos obtenerla por medio de los datos GET

Otras variables que podemos exportar por GET son :

- **%a** La dirección IP del cliente
- **%i** El nombre de usuario del cliente
- **%n** Dominio al que se intento acceder
- **%p** Variable that holds the REQUEST_URI, i.e. the path and the optional query string of %u, but note for convenience without the leading “/”.
- **%s** Variable that holds the matched source group (client group) or “unknown” if no groups were matched.
- **%t** Variable that holds the matched destination group (target group) or “unknown” if no groups were matched.
- **%u** Variable that holds the requested URL.

##### Blacklist

Listado de sitios prohibidos organizados por categorias. A continuacion describimos cada unas de las mismas:

| Categoria | Descripcion |
|----|----|
| ads | Servidores de Publicidad |
| adult | Sitios que contienen material adulto |
| aggressive | Sitios que promueven la violencia |
| antispyware | Sitios que remueven Spyware |
| artnudes | Sitios de arte que contienen desnudos aritsticos |
| astrology | Sitios de Astrologia |
| audio-video | Sitios de descarga de videos o audio |
| banking | Sitios de Banca en linea |
| beerliquorinfo | Sitios con informacion relacionada con Cervezas o Licores |
| beerliquorsale | Sitios de Compra de Cervezas o licores |
| Blog | Sitios de bítacoras |
| cellphones | Material para Telefonos Celulares |
| chat | Sitios con salas de chat |
| childcare | Sitios relacionados con el Cuidado de niños |
| cleaning | Sitios relacionados con limpieza |
| clothing | Sitios relacionados con la venta de indumentaria |
| culnary | Sitios relacionados con la cocina |
| dating | Sitios relacionados con citas en linea |
| desktopsillies | Sitios que contienen salvapantallas, backgrounds, cursores, punteros, temas de escritorio y material peligroso |
| dialers | Sitios con marcadores automaticos como los utilizados por Troyanos o sitios pornograficos |
| drugs | Sitios Reladionados Con Drogas |
| ecommerce | Sitios que proveen compras en linea |
| entertainment | Sitios que promocionan peliculas, libros, revistas, humor |
| filehosting | Sitios para hostear archivos |
| frencheducation | Sitios relacionados con educacion francesa |
| gambling | Sitios de Apuestas |
| games | Sitios relacionados a los juegos |
| gardening | Sitios de Jardineria |
| government | Sitios Gubernamentales |
| guns | Sitios relacionados con armas |
| hacking | Sitios con información sobre Hacking/cracking |
| homerepair | Sitios sobre reparaciones hogareñas |
| hygiene | Sitios sobre higiene y material relacionado al aseo personal |
| instantmessaging | Sitios que contienen descargas de clientes de mensajeria y mensajeria basada en la web |
| jewelry | Sitios relacionados con la joyeria y venta de la misma |
| jobsearch | Sitios de busqueda de empleo |
| kidstimewasting | Sitios donde los niños suelen perder el tiempo |
| mail | Sitios de Webmail / email |
| marketingware | Sitios sobre productos de Marketing |
| medical | Sitios web medicos |
| mixed_adult | Sitios con material adulto mezclado |
| naturism | Sitios que contienen fotos de desnudos y/o promueven el nudismo |
| news | Sitios de Noticias |
| onlineauctions | Sitios de Acciones en linea |
| onlinegames | Sitios de Juegos en linea |
| onlinepayment | Sitios de pago online |
| personalfinance | Sitios de Finanzas Personales |
| pets | Sitios sobre mascotas |
| phishing | Sitios que intentan engañar a la gente para que ingrese información privada. |
| porn | Pornografía |
| proxy | Sitios con proxies para saltearse filtros |
| radio | Sitios relacionados a la radio y television que no tienen relacion con noticias |
| religion | Sitios que promueven la religion |
| ringtones | Sitios que contienen ring tones, juegos, dibujos y otros |
| searchengines | Motores de Busqueda, como Google |
| sect | Sitios sobre grupos religiosos |
| sexuality | Sitios dedicados a la sexualidad, que probablemente incluyen material adulto |
| shopping | Sitios de Compras |
| socialnetworking | Sitios de Redes Sociales |
| sportnews | Sitios de noticias deportivas |
| sports | Sitios sobre deportes (todos) |
| spyware | Sitios que corren o tienen software espia para descargar |
| updatesites | Sitios donde hay actualizaciones de software para descargar que podrian contener virus |
| vacation | Sitios sobre vacacionar |
| violence | Sitios que contienen violencia |
| virusinfected | Sitios que contienen archivos infectados con virus |
| warez | Sitios que contienen software pirateado |
| weather | Sitios de noticias del Clima y relacionados con el Clima |
| weapons | Sitios que detallan o venden armas |
| webmail | Sitios de Webmail |
| whitelist | Contienen Sitios 100% adecuados para niños |

#### Referencias

| URL                                                      | Descripción |
|----------------------------------------------------------|-------------|
| <http://urlblacklist.com%7CSitio> con las blacklists     |             |
| <http://www.squid-cache.org%7CPagina> del proyecto Squid |             |
| <http://www.squidguard.org%7CPagina> de SquidGuard       |             |
| <http://dansguardian.org/%7CDansGuardian>                |             |

## Balanceo de Carga y Alta Disponibilidad

### LVS (Linux Virtual Server) + Heartbeat

### Red Hat Cluster Suite

Supongamos que tenemos dos servidores proxy 10.1.2.103 y 10.1.2.105 y ambos atienden por una ip virtual 10.1.2.106

#### LVS

Configuracion de la utilidad IPVS de Linux Virtual Server

##### /etc/ipvsadm.rules

```
-A -t 10.1.2.106:8080 -s wlc
-a -t 10.1.2.106:8080 -r 10.1.2.105:8080 -g -w 1
-a -t 10.1.2.106:8080 -r 10.1.2.103:8080 -g -w 1
```

##### ipvsadm -L

```
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Forward Weight ActiveConn InActConn
TCP  lvs-proxy.cayu.com.ar:       web wlc
  -> proxy2.cayu.com.ar:web Route            1      0          2         
  -> proxy1.cayu.com.ar:web Local            1      1          1         
```

El sistema balancea por medio del algoritmo wlc.

#### Piranha

Piranha es un producto de clustering de Red Hat, incluye el demonio de control de LVS de Red Hat, y una herramienta de configuracion web del cluster.

La herramienta de monitoreo de piranha tiene dos caracteristicas principales :

* Soporte integrado de Heartbeat.
* Chequeo de disponibilidad de servicios en los servidores.

La herramienta de monitoreo de piranha usa el proceso heartbeat por medio de mensajes UDP entre los nodos balanceados y un servicio que se encarga de monitorear que ciertos procesos esten corriendo.

Configuracion de Piranha en el servidor primario

##### /etc/sysconfig/ha/lvs.cf

```java>
Configuracion de Piranha en el servidor secundario

== /etc/sysconfig/ha/lvs.cf ==
 

<code java>
serial_no = 55
primary = 10.1.2.103
service = lvs
backup_active = 1 
backup = 10.1.2.105
heartbeat = 1
heartbeat_port = 539
keepalive = 4
deadtime = 18
network = direct
debug_level = NONE
monitor_links = 0
virtual proxy {
     active = 1
     address = 10.1.2.106 eth0:0
     vip_nmask = 255.255.255.0
     port = 8080
     pmask = 255.255.255.0
     send = "GET cache_object://localhost/info"
     use_regex = 0
     load_monitor = none
     scheduler = wlc
     protocol = tcp
     timeout = 2
     quiesce_server = 0 
     server proxy1 {
         address = 10.1.2.103
         active = 1
         weight = 1
     }
     server proxy2 {
         address = 10.1.2.105
         active = 1
         weight = 1
     }
}
```

Se agrego la configuracion para la interface virtual lo:0 /etc/sysconfig/network-scripts/ifcfg-lo:0

```
DEVICE=lo:0
IPADDR=10.1.2.106
NETMASK=255.255.255.255
#NETWORK=127.0.0.0
# If you're having problems with gated making 127.0.0.0/8 a martian,
# you can change this to something else (255.255.255.255, for example)
#BROADCAST=127.255.255.255
ONBOOT=yes
NAME=lvsIP
```

## Errores comúnes

Si nuestro Squid no responde y encontramos mensaje como este en nuestro *cache.log*

```
2011/05/31 02:13:19| WARNING! Your cache is running out of filedescriptors
```

Dentro de nuestro archivo **/etc/security/limits.conf** deberemos incluir lo siguiente

```
* - nofile 8192
```

Y en nuestro **squid.conf**

```
max_filedesc 8192
```

Otras opciones de configuración de Squid que nos pueden ayudar con cierto manejo de sockets o recursos son:

```
pconn_timeout 			30 seconds
server_persistent_connections 	off
```

## Referencias

<http://es.kioskea.net/contents/internet/http.php3>\
<http://www.cibernetia.com/manuales/introduccion_aplicaciones_web/2_1_fundamentos_web.php>\
<http://wiki.squid-cache.org/SquidFaq/ConfiguringSquid>\
<http://wiki.squid-cache.org/SquidFaq/SquidAcl>\
<http://cayu.com.ar> — *[Sergio Cayuqueo](mailto:cayu@cayu.com.ar) 2011/04/15 14:14*
