---
title: "Tips de Apache"
date: 2015-11-20T14:54:30-03:00
---

*Cosas varias con las que me tope cuando tuve que modificar la configuración de Apache.*

## Directorio sin autenticacion dentro de otro con autenticacion

Ejemplo en algunos casos tenemos un directorio autenticado

```
<Directory "/usr/local/nagios/share">
    AuthType    Basic
    AuthName    "Nagios Access"
    Options     All
    Order       allow,deny
    Allow       from all
    AllowOverride None
    AuthUserFile  /usr/local/nagios/etc/passwd.nagios
    require     valid-user
</Directory>
```

Pero a la vez dentro de el tenemos un directorio que no precisa privilegios y queremos que sea visible para los demas usuarios de la red sin necesidad de autenticación, para ello deberemos configurarlo de la siguiente manera :

```
<Directory "/usr/local/nagios/share/wiki">
    Options     +Indexes
    Satisfy     Any
    Allow       from All
    AllowOverride All
</Directory>
```

## Auto Autenticar Usuario

A veces necesitamos que cierta aplicación web o un directorio en particular crea que esta autenticado con “x” usuario, ejemplo.

```
    RewriteBase	/nagvis
    RewriteRule	/nagvis - [E=REMOTE_USER:nagiosadmin]
```

## Redireccionar de HTTP a HTTPS

```
    RewriteEngine On
    RewriteCond %{HTTPS} off
    RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
```

## Autenticacion LDAP

Fragmento de configuración para Nagios

```
LDAPSharedCacheSize 200000
LDAPCacheEntries 1024
LDAPCacheTTL 600
LDAPOpCacheEntries 1024
LDAPOpCacheTTL 600

ScriptAlias /nagios/cgi-bin "/usr/local/nagios/sbin"

<Directory "/usr/local/nagios/sbin">
    SSLRequireSSL
    Options ExecCGI
    AllowOverride None
    Order allow,deny
    Allow from all
    AuthType               	Basic
    AuthName               	"Ingrese usuario y contraseña de Nagios"
    AuthBasicProvider 		ldap
    AuthLDAPURL 		"ldap://10.1.1.15:389/ou=users,dc=organizacion?uid"
    AuthLDAPURL 		"ldap://10.1.1.16:389/ou=users,dc=organizacion?uid"
    AuthLDAPBindDN 		uid=ldapadmin,ou=users,dc=organizacion
    AuthLDAPBindPassword 	123456
    Require 			valid-user
    Require group 		cn=linux-admin,ou=groups,dc=organizacion
</Directory>
```

## Autenticacion contra dos LDAP diferentes

```
<AuthnProviderAlias ldap ldap-dominio>
    AuthLDAPBindDN usuarioconsulta@dominio.net
    AuthLDAPBindPassword password
    AuthLDAPURL ldap://10.1.0.4:389/OU=Usuarios,DC=dominio,DC=net?SamAccountName?sub?(&(objectClass=user)(memberOf=CN=Apache,OU=Usuarios,DC=dominio,DC=net))
</AuthnProviderAlias>

<AuthnProviderAlias ldap ldap-segundodominio>
    AuthLDAPBindDN usuarioconsulta@segundodominio.net
    AuthLDAPBindPassword password
    AuthLDAPURL ldap://10.15.0.61:389/OU=Usuarios,DC=segundodominio,DC=net?SamAccountName?sub?(&(objectClass=user)(memberOf=CN=Apache,OU=Usuarios,DC=segundodominio,DC=net))
</AuthnProviderAlias>


<VirtualHost *:443>
CustomLog ${APACHE_LOG_DIR}/wiki-access.log combined
ErrorLog ${APACHE_LOG_DIR}/wiki-error.log
SSLEngine on
SSLCertificateFile /etc/apache2/ssl.crt/server.crt
SSLCertificateKeyFile /etc/apache2/ssl.key/server.key

ServerName   wiki
ServerAlias  wiki.dominio.net

DocumentRoot /var/www/wiki/

<Directory />
    AuthBasicProvider ldap-dominio ldap-segundodominio
    AuthType Basic
    AuthName LDAP_Protected_Place
    AuthzLDAPAuthoritative off
    AuthName "Wiki Access"
    Options All
    Order allow,deny
    Allow from all
    SSLRequireSSL
    AllowOverride None
    Require valid-user
</Directory>
</VirtualHost>
```

## Seguridad

**/etc/apache2/conf.d/security**

```
#
# Disable access to the entire file system except for the directories that
# are explicitly allowed later.
#
# This currently breaks the configurations that come with some web application
# Debian packages. It will be made the default for the release after lenny.
#
#<Directory />
#	AllowOverride None
#	Order Deny,Allow
#	Deny from all
#</Directory>

# Changing the following options will not really affect the security of the
# server, but might make attacks slightly more difficult in some cases.

#
# ServerTokens
# This directive configures what you return as the Server HTTP response
# Header. The default is 'Full' which sends information about the OS-Type
# and compiled in modules.
# Set to one of:  Full | OS | Minimal | Minor | Major | Prod
# where Full conveys the most information, and Prod the least.
#
# En Prod no mostramos ninguna info de version ni nada
ServerTokens Prod

#
# Optionally add a line containing the server version and virtual host
# name to server-generated pages (internal error documents, FTP directory
# listings, mod_status and mod_info output etc., but not CGI generated
# documents or custom error documents).
# Set to "EMail" to also include a mailto: link to the ServerAdmin.
# Set to one of:  On | Off | EMail
#
# No mostrar info de version ni nada
ServerSignature Off

#
# Allow TRACE method
#
# Set to "extended" to also reflect the request body (only for testing and
# diagnostic purposes).
#
# Set to one of:  On | Off | extended
#
TraceEnable Off
```

**/etc/php5/apache2/php.ini**

```
expose_php = Off
```

### Mod Security

Mod Security es un módulo de Apache, que mediante del filtrado de los distintos métodos HTTP (GET, POST, etc) adquiere un comportamiento de Firewall Web, filtrando ataques potenciales a nuestros sitios web.

```
apt-get install libapache-mod-security
a2enmod mod-security
```

**/etc/apache2/conf.d/security**

```
ServerTokens Full
SecServerSignature Servidor HTTP Blablabla 7.0
```

### Mod SSL

En la siguiente página wiki se pueden encontrar algunos tips de [OpenSSL](/notas/openssl/) referidos al Servidor Web Apache.

## Calcular Apache prefork

- Determinar RAM disponible para Apache
- Determinar RAM usada por proceso de Apache
- MaxClients en concordancia con (RAM disponible para Apache) / (RAM usada por proceso Apache)

MaxClients: ((Total_Memory)(1024)(MB) - Other_processes_memory) / 73

For Example: I have 16 GB RAM, I might leave free 2 GB for any other processes

MaxClients: ((16\*1024) - 2048) / 73 MaxClient: 196

<http://serverfault.com/questions/266907/how-to-calculate-the-maxclient-value-in-apache>

<https://fuscata.com/kb/set-maxclients-apache-prefork>
