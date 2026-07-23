---
title: "Implementacion de un servidor OpenLDAP"
date: 2015-04-08T16:36:19-03:00
---

OpenLDAP es una implementación libre y de código abierto del protocolo Lightweight Directory Access Protocol (LDAP) desarrollada por el proyecto OpenLDAP. Está liberada bajo su propia licencia OpenLDAP Public License. LDAP es un protocolo de comunicación independiente de la plataforma. Muchas distribuciones GNU/Linux incluyen el software OpenLDAP para el soporte LDAP. Este software también corre en plataformas BSD, AIX, HP-UX, Mac OS X, Solaris, Microsoft Windows (NT y derivados, incluyendo 2000, XP, Vista), y z/OS.

El problema de la autenticación en Linux (y en Unix en general) esta dividido en dos partes, la primera es comprobar el usuario y password, y la segunda es obtener la información del usuario para poder completar el login (user ID, home directory, group membership, shell inicial). En la mayoría de las distribuciones de Linux esta problemática esta resuelta por dos componentes:

- **PAM:** que realiza la autenticación (comprueba el usuario y password, ademas de verificar la validez de la cuenta de usuario).
- **NSS:** que se encarga de obtener la información del usuario mencionada anteriormente.

Ambos componentes presentan una modularidad que les permite acceder a la información desde cualquier tipo de sistema de base de datos, como puede ser los archivos locales (/etc/passwd y / etc/group) o desde servidores como LDAP, Mysql, Windows.

<img src="/notas/diagrama_ldap_radius.png" class="align-center" alt="diagrama_ldap_radius.png" />

En este caso, para mantener centralizada toda la información de autenticación, se utilizo LDAP, el cual fue instalado en los equipos que al momento de la implementación cumplan la función de servidor **Radius**. **LDAP** permite que la información sea accedida desde cualquier lugar a travez de la red, es rápido para realizar búsquedas y tiene una opción de replicación de datos para obtener alta disponibilidad. Radius podía ser utilizado solamente como sistema de autenticación pero no provee la funcionalidad de guardar información de las cuentas de usuario que Linux necesita para efectuar el login. En los servidores que usan esta autenticación se instalo el paquete de Redhat llamado **nss_ldap**, el cual provee los módulos de **PAM** y **NSS** necesarios para integrarse con **LDAP**. Ambos utilizan un archivo de configuración en común (/etc/ldap.conf). En el caso de la replicación y alta disponibilidad, el esquema es del tipo master-slave, donde todas las modificaciones se realizan sobre el master y se propagan al slave (el cual es solo para lectura de datos no para modificación). La alta disponibilidad se logra configurando dos o mas servidores en el archivo de configuración /etc/ldap.conf, resolviendo automáticamente los módulos de **NSS** y **PAM** la conexión con uno u otro equipo. La configuración del servicio **Radius** se modifico para que la *autenticación se realizara a travez de* **PAM** y *no usando los archivos locales* como estaba configurada anteriormente. Con esto se logra una integración total entre la autenticación de los router y los servidores Linux.

## Instalación

Deberemos bajar los siguientes paquetes

DB 4 <http://www.oracle.com/database/berkeley-db/index.html>

OpenLDAP <http://www.openldap.org/>

Para compilar DB4 solo deberemos ejecutar :

```
../dist/configure   --enable-cxx
```

En cambio para compilar OpenLDAP deberemos correr :

```
CPPFLAGS="-I/usr/local/BerkeleyDB.4.8/include" LDFLAGS="-L/usr/local/lib -L/usr/local/BerkeleyDB.4.8/lib" LD_LIBRARY_PATH="/usr/local/BerkeleyDB.4.8/lib"  ./configure    --enable-crypt       --enable-modules    --enable-overlays     --enable-bdb     --enable-hdb     --enable-ldap      --enable-monitor      --enable-passwd     --enable-relay   --enable-shell    --enable-sock
```

Una vez hecho esto tendremos lista nuestra instalación para poder correr.

### Datos iniciales

#### Organización

```
dn: dc=cayu,dc=com,dc=ar
objectClass: top
objectClass: dcObject
objectClass: organization
o: cayu
dc: cayu
structuralObjectClass: organization
```

#### Usuario administrador

```
dn: cn=admin,dc=cayu,dc=com,dc=ar
objectClass: simpleSecurityObject
objectClass: organizationalRole
cn: admin
description: LDAP administrator
userPassword:: e1NTSEF9U1pIeTBlSG9vdGFlVnJWNVNrc0pDM3k4STNRdEordFU=
structuralObjectClass: organizationalRole
```

#### Contenedor de Grupos

```
dn: ou=groups,dc=cayu,dc=com,dc=ar
objectclass:organizationalunit
ou: groups
description: Grupos
```

##### Grupos

```
dn: cn=users-admin,ou=groups,dc=cayu,dc=com,dc=ar
objectClass: posixGroup
cn: users-write
gidNumber: 10001
```

```
dn: cn=users-read,ou=groups,dc=cayu,dc=com,dc=ar
objectClass: posixGroup
cn: users-read
gidNumber: 10002
```

#### Contenedor de Usuarios

```
dn: ou=users,dc=cayu,dc=com,dc=ar
objectclass:organizationalunit
ou: users
description: Usuarios
```

##### Usuarios

```
dn: uid=linux-admin,ou=users,dc=cayu,dc=com,dc=ar
objectClass: top
objectClass: account
objectClass: posixAccount
objectClass: shadowAccount
cn: switch-write
uid: switch-write
uidNumber: 16001
gidNumber: 10001
homeDirectory: /home/linux-admin
loginShell: /bin/bash
gecos: adam
userPassword: {crypt}x
shadowLastChange: 0
shadowMax: 0
shadowWarning: 0
```

```
dn: uid=linux-user,ou=users,dc=cayu,dc=com,dc=ar
objectClass: top
objectClass: account
objectClass: posixAccount
objectClass: shadowAccount
cn: switch-read
uid: switch-read
uidNumber: 16002
gidNumber: 10002
homeDirectory: /home/linux-user
loginShell: /bin/bash
gecos: adam
userPassword: {crypt}x
shadowLastChange: 0
shadowMax: 0
shadowWarning: 0
```

#### Agregar usuarios a grupo

```
dn: cn=users-write,ou=groups,dc=cayu,dc=com,dc=ar
changetype: modify
add: memberUid
memberUid: linux-admin
```

```
dn: cn=users-read,ou=groups,dc=cayu,dc=com,dc=ar
changetype: modify
add: memberUid
memberUid: linux-user
```

## Replicación

### Multimaster

**REQUIERE Openldap 2.4**

La replicación multimaster se puede hacer en 1 o más nodos, la única premisa es que todos deben estar iguales al momento de comenzar esta configuración (misma configuración y misma base).

Vamos a suponer que tenemos los servidores ldap1 y ldap2.

En el servidor ldap1 agregar estas línas al final del slapd.conf:

```
serverID       1 

overlay syncprov

syncRepl rid=2
        provider=ldap://ldap2.com
        binddn="cn=admin,ou=users,dc=dominio,dc=com"
        bindmethod=simple
        credentials=contraseña
        searchbase="dc=dominio,dc=com"
        type=refreshAndPersist
        retry="5 + 5 +"
        interval=00:00:00:05
        starttls=yes
        tls_reqcert=never


mirrormode      true
database monitor
```

En el servidor ldap2:

```
serverID       2 

overlay syncprov

syncRepl rid=1
        provider=ldap://ldap1.com
        binddn="cn=admin,ou=users,dc=dominio,dc=com"
        bindmethod=simple
        credentials=contraseña
        searchbase="dc=dominio,dc=com"
        type=refreshAndPersist
        retry="5 + 5 +"
        interval=00:00:00:05
        starttls=yes
        tls_reqcert=never


mirrormode      true
database monitor
```

La configuración en ambos servidor es similar, solo cambia el ServerID, el rid y el provider, que van invertidos, como se observa en al configuración.

El usuario especificado en el binddn debe tener acceso completo a la base.

### Master / Slave

En el Master

```
moduleload 		syncprov
overlay 		syncprov
syncprov-checkpoint 	100	10
syncprov-sessionlog 	100
```

En el Slave

```
syncrepl rid=123
    provider=ldap://ldap1.com:389
    type=refreshAndPersist
    retry="60 10 300 +"
    searchbase="dc=dominio,dc=com"
    schemachecking=off
    bindmethod=simple
    binddn="cn=admin,ou=users,dc=dominio,dc=com"
    credentials=contraseña
```

## Politicas

Para poder manejar policies de passwords con LDAP y hacer que estos se bloqueen luego de determinados intentos se debe agregar lo siguiente:

RFC <http://www.potaroo.net/ietf/old-ids/draft-behera-ldap-password-policy-00.txt> RFC <http://www.mozilla.org/directory/ietf-docs/draft-behera-ldap-password-policy-05.txt>

## /etc/openldap/slapd.conf

Despues de la definicion de la base de datos se deben agregar las siguientes lineas

```
overlay         ppolicy
ppolicy_default "cn=default,ou=policies,dc=dominio,dc=com"
```

## Registros a agregar en el arbol LDAP

Se debe agregar la “ou” *Policies* para que contenga todas la policies.

```
dn: ou=policies,dc=dominio,dc=com
ou: policies
objectClass: top
objectClass: organizationalUnit
structuralObjectClass: organizationalUnit
```

Ahora se deben agregar las politicas por defecto, en este ejemplo se bloquea el usuario por 120 segundos (2 minutos) luego de tres intentos fallidos.

```
dn: cn=default,ou=policies,dc=dominio,dc=com
pwdFailureCountInterval: 30
pwdSafeModify: FALSE
pwdGraceAuthNLimit: 5
pwdLockoutDuration: 120
objectClass: pwdPolicy
objectClass: person
objectClass: top
objectClass: pwdPolicyChecker
pwdMaxFailure: 3
pwdAllowUserChange: TRUE
pwdMinLength: 5
cn: default
pwdAttribute: userPassword
pwdMinAge: 5
pwdLockout: TRUE
pwdCheckQuality: 2
pwdInHistory: 5
sn: default policy
pwdMustChange: FALSE
pwdExpireWarning: 600
```

```
# Default Password Policy

dn: cn=default,ou=pwdpolicies,dc=zes_example,dc=com

objectClass: pwdPolicy

cn: default

# User can change his/her password

pwdAllowUserChange: TRUE

# Return warning to bind attempt (seconds) -- 3 days

pwdExpireWarning: 259200

# Interval in seconds to reset failure pwd count

pwdFailureCountInterval: 100

# Do not allow to bind on expired passwords

pwdGraceAuthNLimit:  0

# Reject any password changes in this list

pwdInHistory: 3

# Lock out account when user tries more than x attempts using invalid password

pwdLockout: TRUE

# Do not allow the system to unlock the account

pwdLockoutDuration: 0

# Consecutinve # of failure attempts

pwdMaxFailure: 5

# How long the password lasts before user has to change it (seconds)  -- 90 days

pwdMaxAge: 77760000

# Password length

pwdMinLength: 6
```

Modificado todo esto se reinicia el LDAP.

<img src="/notas/ppolicy.png" class="align-center" alt="ppolicy.png" />

### Chequeos de password

Este es un modulo para chequear o establecer dificultades a los passwords.

Hay que agregar el objectClass ‘pwdPolicyChecker’ con el atributo del modulo LDPA ej ‘check_password.so’ dentro de las politicas.

Parametros de configuración :

- useCracklib: integer. Usar o no CrackLib
- minPoints: integer. Puntaje minimo, cada punto se asigna por cada clase de caracter en el password.
- minUpper: integer. Cantidad minima de mayusculas
- minLower: integer. Cantidad minima de minusculas
- minDigit: integer. Cantidad minima de digitos
- minPunct: integer. Cantidad minima de signos

Default : **check_password.conf**

```
useCracklib     1
minPoints       3
minUpper        0
minLower        0
minDigit        0
minPunct        0
```

Mensaje de error ldappasswd

```
  Result: Constraint violation (19)
  Additional info: Password fails quality checking policy
```

Ref.:

<http://ltb-project.org/wiki/documentation/openldap-ppolicy-check-password>

<http://www.symas.com/blog/?page_id=66>

## En los clientes

### Configuracion servidor LDAP con políticas

**/etc/pam.d/system-auth**

```
auth [success=1 default=ignore] pam_unix.so
auth required pam_ldap.so use_first_pass
auth required pam_permit.so

account [success=1 default=ignore] pam_unix.so
account required pam_ldap.so
account required pam_permit.so

password requisite pam_cracklib.so try_first_pass retry=3
password sufficient pam_unix.so md5 shadow nullok try_first_pass use_authtok
password required pam_deny.so

session optional pam_keyinit.so revoke
session required pam_limits.so
session [success=1 default=ignore] pam_succeed_if.so service in crond quiet use_uid
session required pam_unix.so
```

**/etc/nsswitch.conf**

```
...
passwd:     files ldap
shadow:     files
group:      files ldap
...
```

**/etc/openldap/ldap.conf**

```
TLS_REQCERT allow
TLSREQCERT never
```

*Igualmente estos datos pueden ir en el archivo siguiente como para tener todo mas junto.*

**/etc/ldap.conf**

```
host ldap-primario ldap-secundario

binddn uid=usuario-query,ou=users,dc=dominio,dc=com
bindpw password1234

bind_policy soft

bindlimit 3
timelimit 10

base dc=dominio

pam_filter objectclass=posixAccount

pam_login_attribute uid

nss_base_passwd		ou=users,dc=dominio,dc=com?one
nss_base_group		ou=groups,dc=dominio,dc=com?one

ssl yes
pam_password md5
TLS_REQCERT never
```

Y si tenemos una aplicacion que utiliza **PAM** para autenticar deberemos configurarla asi (ejemplo FreeRADIUS) :

\*\*/etc/pam.d/radiusd \*\*

```
auth       include	system-auth
account    required	pam_nologin.so
account    include	system-auth
password   include	system-auth
session    include	system-auth
```

### Configuracion servidor LDAP sin políticas

#### Configuracion PAM

**PAM** **P**luggable **A**uthentication **M**odules. PAM es un sistema de autenticación que controla el acceso a diferentes recursos.

##### /etc/nsswitch.conf

```perl>
== /etc/pam.d/system-auth ==

<code perl>
auth      required      /lib/security/$ISA/pam_env.so
auth      sufficient    /lib/security/$ISA/pam_ldap.so
auth      sufficient    /lib/security/$ISA/pam_unix.so likeauth nullok use_first_pass
auth      required      /lib/security/$ISA/pam_permit.so
auth      required      /lib/security/$ISA/pam_deny.so
account   sufficient    /lib/security/$ISA/pam_ldap.so 
account   required      /lib/security/$ISA/pam_unix.so
password  required      /lib/security/$ISA/pam_cracklib.so retry=3 type=
password  sufficient    /lib/security/$ISA/pam_ldap.so debug use_first_pass
password  sufficient    /lib/security/$ISA/pam_unix.so nullok use_authtok md5 shadow use_first_pass
password  required      /lib/security/$ISA/pam_deny.so
session   required      /lib/security/$ISA/pam_limits.so
session   required      /lib/security/$ISA/pam_unix.so
session   optional      /lib/security/$ISA/pam_mkhomedir.so
```

#### Radius

**RADIUS** (acrónimo en inglés de **R**emote **A**uthentication **D**ial-In **U**ser **S**erver). Es un protocolo de autenticación y autorización para aplicaciones de acceso a la red o movilidad IP. Utiliza el puerto 1813 UDP para establecer sus conexiones.

*Cuando se realiza la conexión con un ISP mediante módem, DSL, cablemódem, Ethernet o Wi-Fi, se envía una información que generalmente es un nombre de usuario y una contraseña. Esta información se transfiere a un dispositivo NAS (Servidor de Acceso a la Red) sobre el protocolo PPP, quien redirige la petición a un servidor RADIUS sobre el protocolo RADIUS. El servidor RADIUS comprueba que la información es correcta utilizando esquemas de autentificación como PAP, CHAP o EAP. Si es aceptado, el servidor autorizará el acceso al sistema del ISP y le asigna los recursos de red como una dirección IP, y otros parámetros como L2TP, etc.*

Configuracion de autenticacion, con **PAM** previamente configurado contra LDAP

##### /etc/raddb/users

```python>
==== NSCD ====

El NSS (Name Service Switch) provee una interface para configurar y acceder a diferentes bases de datos de cuentas de usuarios y claves como /etc/passwd, /etc/group, /etc/hosts, LDAP , etc.


**nsswitch.conf**


Un sistema configurado por defecto generalmente usa NSS y su archivo de configuración etc/nsswitch.conf para resolver nombres de usuario o grupo, este archivo se compone de llamados a bases de datos como passwd, shadow y group, y de otras fuentes como archivos locales, directorios de datos OpenLDAP, nis (Network Information Service), nisplus (NIS+), bases de datos PostgreSQL o MySQL.

<code>
passwd: files ldap
shadow: files ldap
group: files ldap
hosts: files ldap
```

En el mismo orden en que son listadas seran utilizadas al momento de resolver las peticiones.

NSS es la implementación de una Biblioteca estándar de C que llama a las funciones getpwent o getgrent dependiendo del modulo NSS, exiten modulos LDAP, PostgreSQL, MySQL o MDNS.

Diagrama de funcionamiento :

<img src="/notas/nss_ldap.png" class="align-center" alt="nss_ldap.png" />

Diagrama de funcionamiento con NSCD :

<img src="/notas/nssldap_nscd.png" class="align-center" alt="nssldap_nscd.png" />

NSCD es un demonio que gestiona las búsquedas en «passwd», «group» y «host» para los programas en ejecución y guarda los resultados para la siguiente consulta, es para servicios lentos como LDAP, NIS o NIS+.

Ref.: <http://code.google.com/p/nsscache/wiki/BackgroundOnNameServiceSwitch>

## Notas y extras

Para configurar el HASH por default del servidor, deberemos agregar dentro del archivo slapd.conf

```
password-hash   {crypt}
```

o

```
password-hash   {ssha}
```

*Para que las politicas tengan efecto, deberemos utilizar siempre SSHA*

Script para chequear vencimientos de passwords avisar antes de tiempo, o bloquear cuando esta vencido. Con modificaciones hechas por mi.

El original esta aca : <http://ltb-project.org>

```bash>
&1 | nawk -F= \
				'/^time\(\)/ {gsub(/ /,"",$2);print $2}'`
		else
			now=`date +"%Y %m %d %H %M %S" -u`
			date=`getTimeInSeconds "$now"`
		fi
	fi

	echo ${date}
}

#====================================================================
# Script
#====================================================================

## Variables initialization
tmp_dir="/tmp/$$.checkldap.tmp"
result_file="${tmp_dir}/res.tmp.1"
buffer_file="${tmp_dir}/buf.tmp.1"
ldap_param="-LLL -H ${MY_LDAP_HOSTURI} -x"
nb_users=0
nb_expired_users=0
nb_warning_users=0

## Some tests
if [ -d ${tmp_dir} ]; then
	echo "Error : temporary directory exists (${tmp_dir})"
	exit 1
fi
mkdir ${tmp_dir}

if [ ${MY_LDAP_ROOTDN} ]; then
	ldap_param="${ldap_param} -D ${MY_LDAP_ROOTDN} -w ${MY_LDAP_ROOTPW}"
fi

## Performs global search
${MY_LDAP_SEARCHBIN} ${ldap_param} -s one -b "${MY_LDAP_SEARCHBASE}" \
	"${MY_LDAP_SEARCHFILTER}" "dn" > ${result_file}

## Loops on results
while read dnStr
do
	# Do not use blank lines
	if [ ! "${dnStr}" ]; then
		continue
	fi

	# Process ldap search
	dn=`echo ${dnStr} | cut -d : -f 2`

	# Increment users counter
	nb_users=`expr ${nb_users} + 1`
	
	${MY_LDAP_SEARCHBIN} ${ldap_param} -s base -b "${dn}" \
		${MY_LDAP_NAME_ATTR} ${MY_LDAP_LOGIN_ATTR} ${MY_LDAP_MAIL_ATTR} pwdChangedTime pwdPolicySubentry \
		> ${buffer_file}

	login=`grep -w "${MY_LDAP_LOGIN_ATTR}:" ${buffer_file} | cut -d : -f 2 \
		| sed "s/^ *//;s/ *$//"`
	name=`grep -w "${MY_LDAP_NAME_ATTR}:" ${buffer_file} | cut -d : -f 2\
		| sed "s/^ *//;s/ *$//"`
	mail=`grep -w "${MY_LDAP_MAIL_ATTR}:" ${buffer_file} | cut -d : -f 2 \
		| sed "s/^ *//;s/ *$//"`
	pwdChangedTime=`grep -w "pwdChangedTime:" ${buffer_file} \
		| cut -d : -f 2 | cut -c 0-15 | sed "s/^ *//;s/ *$//"`
	pwdPolicySubentry=`grep -w "pwdPolicySubentry:" ${buffer_file} \
		| cut -d : -f 2 | sed "s/^ *//;s/ *$//"`

	# Go to next entry if no pwdChangedTime
	if [ ! "${pwdChangedTime}" ]; then
		echo "${MY_LOG_HEADER} No hay fecha de cambio para ${login}" >&2
		continue
	fi

	# Go to next entry if no pwdPolicySubEntry and no default policy
	if [ ! "${pwdPolicySubentry}" -a ! "${MY_LDAP_DEFAULTPWDPOLICYDN}" ]; then
		echo "${MY_LOG_HEADER} No hay politica de password para ${login}" >&2
		continue
	fi

	# Retrieves user policy pwdMaxAge and pwdExpireWarning attributes
	ldap_search="${MY_LDAP_SEARCHBIN} ${ldap_param} -s base"
	if [ "${pwdPolicySubentry}" ]; then
		ldap_search="${ldap_search} -b ${pwdPolicySubentry}"
	else
		ldap_search="${ldap_search} -b ${MY_LDAP_DEFAULTPWDPOLICYDN}"
	fi

	ldap_search="$ldap_search pwdMaxAge pwdExpireWarning"
	pwdMaxAge=`${ldap_search} | grep -w "pwdMaxAge:" | cut -d : -f 2 \
		| sed "s/^ *//;s/ *$//"`
	pwdExpireWarning=`${ldap_search} | grep -w "pwdExpireWarning:" | cut -d : -f 2 \
		| sed "s/^ *//;s/ *$//"`
	# Replace MAIL_DELAY by pwdExpireWarning if exists
	MY_MAIL_DELAY=${MY_MAIL_DELAY:=$pwdExpireWarning}

	# Retrieves time difference between today and last change.
	if [ "${pwdChangedTime}" ]; then
		s=`echo ${pwdChangedTime} | cut -c 13-14`
		m=`echo ${pwdChangedTime} | cut -c 11-12`
		h=`echo ${pwdChangedTime} | cut -c 9-10`
		d=`echo ${pwdChangedTime} | cut -c 7-8`
		M=`echo ${pwdChangedTime} | cut -c 5-6`
		y=`echo ${pwdChangedTime} | cut -c 0-4`
		currentTime=`getTimeInSeconds`
		pwdChangedTime=`getTimeInSeconds "$y $M $d $h $m $s"`
		diffTime=`expr ${currentTime} - ${pwdChangedTime}`
	fi

	# Go to next user if password already expired
	expireTime=`expr ${pwdChangedTime} + ${pwdMaxAge}`
	if [ ${currentTime} -gt ${expireTime} ]; then

		# Sending mail...
		${MY_MAIL_BIN} ${mail} ${login} 'bloqueado' \'${EXPIROEN}\' >&2
#		echo "${logmsg}" | ${MY_MAIL_BIN} ${mail} ${expireTime} ${login} 'bloqueado'  >&2
		if [ "${login}" != "ldapadmin" ]; then
		    echo "
		    dn: uid=${login},ou=users,dc=dominio,dc=com
		    changetype: modify
		    add: pwdAccountLockedTime
		    pwdAccountLockedTime: 000001010000Z"| ldapmodify -x -D "uid=ldapadmin,ou=users,dc=dominio,dc=com" -w ..xtechpae -v

		    # Print debug information on STDERR
		    nb_expired_users=`expr ${nb_expired_users} + 1`
		fi		
		    echo "${MY_LOG_HEADER} Password expirado para ${login} mail enviado a <${mail}>" >&2
		continue
	fi

	# ALL LDAP attributes should be there, else continue to next user
#	if [ "${mail}" -a "${name}" -a "${login}" -a "${diffTime}" -a "${pwdMaxAge}" ]
	if [ "${mail}" -a "${login}" -a "${diffTime}" -a "${pwdMaxAge}" ]
	then
		EXPIRACION=`expr ${pwdMaxAge} - ${diffTime}`
		EXPIROEN="$(timerLengthString ${EXPIRACION})"
		# Ajusts time with delay
		diffTime=`expr ${diffTime} + ${MY_MAIL_DELAY}`
		if [ ${diffTime} -gt ${pwdMaxAge} ]; then
			logmsg="${MY_MAIL_BODY}"
			logmsg=`echo ${logmsg} | sed "s/%name/${name}/; \
				s/%login/${login}/"`
			    # Sending mail...
			    #echo "${logmsg}" | ${MY_MAIL_BIN} ${mail} \'${EXPIROEN}\' ${login} 'warning' >&2
			    #echo "${logmsg}" | ${MY_MAIL_BIN} \'${mail}\' \'${EXPIROEN}\' \'${login}\' 'warning' >&2
			    ${MY_MAIL_BIN} ${mail} ${login} 'warning' \'${EXPIROEN}\' >&2
			    #echo ${MY_MAIL_BIN} ${mail} ${login} 'warning' \'${EXPIRO}\' >&2
			    # Print debug information on STDERR
			    #echo "${MY_LOG_HEADER} Mail de advertencia ${login} <${mail}>" >&2
			    echo "${MY_LOG_HEADER} Password para "${login}" a punto de expirar en" ${EXPIROEN} >&2

			# Increment warning counter
			nb_warning_users=`expr ${nb_warning_users} + 1`
		fi
	fi

done < ${result_file}

# Print statistics on STDOUT
echo "${MY_LOG_HEADER} --- Statistics ---"
echo "${MY_LOG_HEADER} Users checked: ${nb_users}"
echo "${MY_LOG_HEADER} Account expired: ${nb_expired_users}"
echo "${MY_LOG_HEADER} Account in warning: ${nb_warning_users}"

# Delete temporary files
rm -rf ${tmp_dir}

# Exit
exit 0
```

Script que envia mails

```perl>
 ".$expiracion."";
    my $mensaje 	= "<font size='+1'><i><ul>
    <li>Su usuario de autenticaci&oacute;n de Radius vencer&aacute; en ".$expiracion.".</li><br>
    <li>Dirigirse a https://servidor-ldap/cambia_password para cambiarla.</li><br>
    <li>Leer atentamente las caracter&iacute;sticas que debe tener el password.</li><br>
    <li>Una vez vencida su cuenta solamente podr&aacute; ser cambiada por</li>
    <ul><li>Nombre int. 3994</li></ul>
    <ul><li>Nombre int. 4491</li></ul>
    </ul></i></font>";
    my $msg = MIME::Lite->new
    (
        Subject => $asunto,
	From    => 'Administracion LDAP ldapadmin@servidor-ldap',
	To      => $email,
	Type    => 'text/html',
	Data    => "<H1>".$asuntohtml."</H1><IMG SRC='http://servidor-ldap/logo.gif'><hr>".$mensaje.""
    );
    $msg->send();
    print " $0 : Mail de warning enviado a <".$email.">\n";
}

if($tipo_mail eq 'bloqueado') {
    my $asunto 		= "Su cuenta de usuario (".$usuario.") para equipos de Telecomunicaciones a sido bloqueada";
    my $asuntohtml 	= "Su cuenta de usuario ( ".$usuario." ) para equipos de Telecomunicaciones a sido bloqueada";
    my $mensaje 	= "<font size='+1'><i><ul>
    <li>Su usuario de autenticaci&oacute;n de Radius vencio.</li><br>
    <li>Una vez vencida su cuenta solamente podr&aacute; ser cambiada por</li>
    </ul></i></font>";
    my $msg = MIME::Lite->new
    (
        Subject => $asunto,
	From    => 'Administracion LDAP ldapadmin@servidor-ldap',
	To      => $email,
	Type    => 'text/html',
	Data    => "<H1>".$asuntohtml."</H1><IMG SRC='http://servidor-ldap/logo.gif'><hr>".$mensaje.""
    );
    $msg->send();
    print " $0 : Mail de bloqueo enviado a <".$email.">\n";
}
```

### Autocambio de contraseña

En el caso de que necesitemos obviamente que antes de que se les caduque la contraseña a los usuarios, ellos mismos puedan cambiar su contraseña, podremos utilizar un script via web

<img src="/notas/autocambio.png" class="align-center" alt="autocambio.png" />

En este apartado del archivo slapd.conf deberemos tener activado el self write

```
access to *
        by self write
```

Aca estan los archivos del script en PHP

[cambia_password.tgz](/notas/cambia_password.tgz)

### SUDO

La confiuración de SUDO almacenada dentro de LDAP la podemos encontrar en el archivo README.LDAP

Igualmente SUDO nos tomara la configuación actual para obtener los grupos, de los dato que obtiene del nss, asi que podemos configurar nuestro grupo ldap para ser tomado por el sudoers de esta forma.

La sintaxis es la misma que para cualquier sudoers

```
Cmnd_Alias     PASSWDROOT = /usr/bin/passwd root
Cmnd_Alias     USER_ADD   = /usr/sbin/useradd
Cmnd_Alias     PASSWDUSER = /usr/bin/passwd
Cmnd_Alias     USER_DEL   = /usr/sbin/userdel

administrador			ALL=(ALL) ALL
%Administradores	 	ALL = NOPASSWD: PASSWDROOT, USER_ADD, PASSWDUSER, USER_DEL
```

### Referencias

<table>
<thead>
<tr>
<th>OpenLDAP</th>
<th><a href="http://www.openldap.org/">http://www.openldap.org/</a></th>
</tr>
</thead>
<tbody>
<tr>
<td>Configuraciones de ejemplo</td>
<td><a href="http://itsecureadmin.com/wiki/index.php/OpenLDAP_Multi-Master_Replication">http://itsecureadmin.com/wiki/index.php/OpenLDAP_Multi-Master_Replication</a><br />
<a href="http://www.zytrax.com/books/ldap/ch7/#ol-syncrepl-mm">http://www.zytrax.com/books/ldap/ch7/#ol-syncrepl-mm</a></td>
</tr>
</tbody>
</table>
