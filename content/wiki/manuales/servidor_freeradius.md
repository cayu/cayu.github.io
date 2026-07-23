---
title: "Servidor FreeRadius"
date: 2015-04-07T16:42:30-03:00
---

RADIUS (acrónimo en inglés de Remote Authentication Dial-In User Server). Es un protocolo de autenticación y autorización para aplicaciones de acceso a la red o movilidad IP. Utiliza el puerto 1813 UDP para establecer sus conexiones.

Cuando se realiza la conexión con un ISP mediante módem, DSL, cablemódem, Ethernet o Wi-Fi, se envía una información que generalmente es un nombre de usuario y una contraseña. Esta información se transfiere a un dispositivo NAS (Servidor de Acceso a la Red o Network Access Server (NAS)) sobre el protocolo PPP, quien redirige la petición a un servidor RADIUS sobre el protocolo RADIUS. El servidor RADIUS comprueba que la información es correcta utilizando esquemas de autenticación como PAP, CHAP o EAP. Si es aceptado, el servidor autorizará el acceso al sistema del ISP y le asigna los recursos de red como una dirección IP, y otros parámetros como L2TP, etc.

Una de las características más importantes del protocolo RADIUS es su capacidad de manejar sesiones, notificando cuando comienza y termina una conexión, así que al usuario se le podrá determinar su consumo y facturar en consecuencia; los datos se pueden utilizar con propósitos estadísticos.

RADIUS fue desarrollado originalmente por Livingston Enterprises para la serie PortMaster de sus Servidores de Acceso a la Red(NAS), más tarde se publicó como RFC 2138 y RFC 2139. Actualmente existen muchos servidores RADIUS, tanto comerciales como de código abierto. Las prestaciones pueden variar, pero la mayoría pueden gestionar los usuarios en archivos de texto, servidores LDAP, bases de datos varias, etc. A menudo se utiliza SNMP para monitorear remotamente el servicio. Los servidores Proxy RADIUS se utilizan para una administración centralizada y pueden reescribir paquetes RADIUS al vuelo (por razones de seguridad, o hacer conversiones entre dialectos de diferentes fabricantes).

RADIUS es extensible; la mayoría de fabricantes de software y hardware RADIUS implementan sus propios dialectos.

FreeRadius es el primer OpenSource Radius Server, entre sus principales caracteristicas esta la factibilidad de usar software de Base de Datos como OpenLDAP, MySQL, PostgreSQL, Oracle y soporta varios protocolos de autenticacion como EAP, con EAP-MD5, EAP-SIM, EAP-TLS, EAP-TTLS, EAP-PEAP, MSCHAPv2 y subtipos de Cisco LEAP. Todos estos protocolos son usados en la mayoria de los equipos wireless de equipos portatiles y Access point del mercado, por lo que es un servidor bastante completo.

Permite usar los estandares de encriptacion WEP, WPA, WPA2, etc.

<img src="/manuales/radius/radius_diagrama.png" class="align-center" alt="radius_diagrama.png" />

# Implementacion

### Archivos

#### /etc/raddb/users

Configuración de la autenticación y autorización. En este archivo se determina el método a través del cual se validan los usuarios (PAM, LDAP, etc) y el acceso que tiene cada uno.

#### /etc/raddb/clients.conf

Este archivo contiene el listado de equipo que tienen permitido autenticación contra el servicio Radius. El permiso se hace por dirección IP y a travez de una clave compartida.

#### /etc/raddb/radius.conf

Configuración general del servicio Radius. Aquí se agregaron los parametros para validar por LDAP.

#### /var/log/radius/radius.log

Esta archivo contiene registros generales del servicio radius y principalmente la información de que usuarios se loguearon, desde donde y si la operacion exitosa o no.

# Integracion con eDirectory

eDirectory, desarrollado por Novell, es un servicio de directorio que soporta múltiples arquitecturas incluyendo Windows,NetWare, Linux, incluyendo algunas distribuciones de Unix. Se ha utilizado durante tiempo para la administración de usuarios, gestión de configuraciones y gestión de software. eDirectory se ha desarrollado como componente central en una gama más amplia de productos para la gestión de identidad. Fue conocido previamente como servicios de directorio de Novell.

Documentos de referencia :

[freeradius-edir.pdf](/manuales/radius/freeradius-edir.pdf)

[radiusadmin-edirectory.pdf](/manuales/radius/radiusadmin-edirectory.pdf)

# Integracion con LDAP

Si el LDAP es el mismo utilizado para autenticarse al servidor donde se ejecuta el servidor Radius

Ejemplo para usuarios de Routers Cisco :

**raddb/users**

```
DEFAULT Auth-Type := Pam, Pam-Auth := radiusd,  Group == "radius-admin"
        Service-Type = Login-User,
        Cisco-AVPair = "shell:priv-lvl=15",
        Fall-Through = 0

DEFAULT Auth-Type := Pam, Pam-Auth := radiusd, Group == "radius-user"
        Service-Type = Login-User,
        Cisco-AVPair = "shell:priv-lvl=5",
        Fall-Through = 0
```

Ejemplo para usuarios de Wireless :

```
sergio-wifi    Auth-Type := Local, User-Password == "13wrepw3"
      Service-Type = Login-User,
      Cisco-AVPair = "shell:priv-lvl=0",
      Fall-Through = 0
```

En cambio si vamos a realizar la integración con Novell eDirectory

**raddb/users**

```
DEFAULT Auth-Type := LDAP
	Fall-Through = No
```

**raddb/modules/ldap**

```
ldap {
	server = "10.1.1.4 10.1.1.5"
	identity = "cn=usuariobind,ou=PlantaBA,ou=AR,o=Dominio"
	password = pasword1234
	basedn = "o=Dominio"
	filter = "(&(uid=%{Stripped-User-Name:-%{User-Name}})(groupmembership=cn=UsuariosWireless,ou=PlantaBA,ou=AR,o=Dominio))"
	base_filter = "(objectClass=person)"
	ldap_connections_number = 20
	timeout = 4
	timelimit = 3
	net_timeout = 1
	tls {
		start_tls = yes
		require_cert = "allow"
	}
	dictionary_mapping = ${confdir}/ldap.attrmap
	password_attribute = nspmPassword
	edir_account_policy_check = no
	groupname_attribute = cn
	groupmembership_filter = "(&(objectClass=groupOfNames)(member=%{control:Ldap-UserDn})(cn=UsuariosWireless))"
	set_auth_type = yes

}
```

Referencia : [Documentacion de implementacion de servidor OpenLDAP](/notas/openldap/)
