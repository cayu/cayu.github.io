---
title: "OpenSSH"
date: 2017-08-17T12:10:58-03:00
---

## Configuración general

##### TCPKeepAlive yes

Significa que el servidor sshd enviara mensajes de keepalive a el cliente después de que detecta alguna inactividad (idle), este método puede ser spoofable.

##### UsePrivilegeSeparation yes

Significa que después de que la sesión ssh se ha establecida se pasaran los privilegios de ese proceso a el usuario de quien inicie la conexión, sin esto el proceso estará a nombre de root, muy bueno esto para evitar elevaciónn de privilegios.

##### LoginGraceTime 30

El número indica la cantidad de segundos en que la pantalla de login estará disponible para que el usuario capture su nombre de usuario y contraseña, si no lo hace el login se cerrará, evitando así dejar por tiempo indeterminado pantallas de login sin que nadie las use, o peor aun, que alguien este intentando mediante un script varias veces el adivinar un usuario y contraseña. Aqui conviene identificar en nuestros usuarios el tiempo promedio que tardan en ingresar su usuario y contraseña y darles unos cuantos segundos más de margen por los usuarios lentos para que ingresen sus credenciales. Si somos el único usuario del sistema considero que con 20 o 30 segundos es mas que suficiente.

##### PermitRootLogin no

El usuario root no tendrá permiso de acceder mediante ssh y por lo tanto cualquier intento de ataque directo a root será inútil. Con esto siempre tendremos que ingresar como un usuario normal y ya estando adentro entonces mediante su o sudo podremos usar funciones de root.

##### MaxAuthTries 2

El número indica la cantidad de veces que podemos equivocarnos en ingresar el usuario y/o contraseña, en este caso después de dos intentos, se perderá o cerrará la conexión. Claro, es totalmente posible volver a intentarlo, pero como son dos intentos por vez, evitaremos ataques basados en la persistencia de la conexión, como se perderá al tercer intento de conexión, el ataque cesará.

##### MaxStartups 3

El número indica la cantidad de pantallas de login, o cantidad de conexiones simultaneas de login que permitirá el sshd por ip que intente conectarse. Hay ataques muy efectivos que dividen el ataque en decenas y puede ser que en cientos (si el sistema atacado lo permite) de conexiones de login. Es decir, el ataque divide en una gran cantidad de logins los intentos por ingresar, aumentando sus posibilidades de más rapidamente adivinar al usuario y contraseña. Con esta directiva limitamos a tan solo 3 pantallas de login. Que quede claro, una vez logueados en el sistema, es posible tener mas de 3 terminales de ssh, se refiere exclusivamente a pantallas de login.

##### AllowUsers

En sistemas donde se tiene varios usuarios, quizás existan varios que solo pueden acceder desde la LAN por ejemplo, o quizás solo desde ciertos equipos. Con esta directiva podemos indicar los usuarios que pueden ingresar via ssh. Si solo indicamos al usuario:

```
AllowUsers sergio
```

El usuario sergio podrá ingresar desde cualquier PC en cualquier lugar, no se está validando el host. Si se quiere mas seguridad, es posible indicar también el host mediante el símbolo @

- **Solo desde la IP indicada**
  - AllowUsers sergio@192.168.0.25
- **Toda la red indicada**
  - AllowUsers sergio@192.168.0.\*
- **sergio desde su equipo del dominio**
  - AllowUsers sergio@pc.dominio.com
- **sergio desde cualquier equipo del dominio indicado**
  - AllowUsers sergio@\*.dominio.com

##### AllowGroups

Idem a la directiva anterior, pero define que grupos de usuarios se pueden logguear por SSH.

```
AllowGroups linux-admin linux-ssh
```

## TIPS varios

## Autenticación con clave pública/privada (RSA)

Algunos tips interensantes, ej Ej pares de claves ssh para no tener que poner el password por cada sesion, si no utilizar llaves

Ejecutar

```
$ ssh-keygen -t rsa
```

Con este comando se genera el par de claves publica/privada, esto generara en el path \$HOME/.ssh/ los archivos: id_rsa (clave privada) y id_rsa.pub (clave pública). El archivo id_rsa.pub debe publicarse incluirse en el archivo \$HOME/.ssh/authorized_keys del sistema remoto.

```
$ scp .ssh/id_rsa.pub host_remoto:$HOME/
```

Luego, en el equipo remoto

**Por si no estuviera generado**

```
$ mkdir .ssh
```

**Agrega esta clave pública al archivo de claves públicas autorizadas en el directorio .ssh**

```
$ cat id_dsa.pub >> ~/.ssh/authorized_keys
```

Luego setear permisos

```
$ chmod 755 ~
$ chmod 700 ~/.ssh
$ chmod 644 ~/.ssh/authorized_keys
```

## Conexion SSH reverso

```
ssh -nN -R 1234:localhost:22 cayu@cayu.com.ar
```

o sea si estamos dentro de un lugar donde la entrada SSH esta denegada pero si la salida, podemos dejar una conexion abierta para entrar desde afuera

```
ssh -p 1234 locahost
```

## Compartir conexion SSH

La idea es compartir una conexion ssh, por ejemplo:

```
hostA# ssh -M -S /tmp/hostB hostB
```

Esa linea hace una conexion normal de ssh, pero crear un Unix socket en /tmp/hostB para compartir, entonces:

```
hostA# ssh -S /tmp/hostB hostB
```

Y van a ver como entran al hostB en forma inmediata y sin clave.

O tambien pueden copiar archivos:

```
hostA# scp -o 'ControlPath /tmp/hostA' /etc/fstab hostB:/tmp
```

Es util para scripts o para conexiones ssh de esas que son muy lentas para el login (generalmente por que el destino tiene problemas de DNS).

Fuente: DiegoW - Lista de correo de Lanux

## SSH detras de un proxy

Ejecutamos un

```
apt-get install corkscrew
```

en nuestro ssh_config pondremos,

```
ProxyCommand /usr/bin/corkscrew proxy.com.ar 3128 %h %p
```

aca un link de la lista de correo de lanux de como compartir una conexion SSH <http://www.lanux.org.ar/pipermail/lista/2007-December/004851.html>

otro link interesante <http://www.malditainternet.com/Web_Squid_SSH>

comparacion de ejemplos de comandos debian y SuSE

En Debian

```
sudo update-rc.d -f openssh remove
```

```
sudo update-rc.d openssh defaults
```

En SuSE

```
sudo insserv -r /etc/init.d/sshd
sudo insserv -d /etc/init.d/sshd
```

<http://www.forosuse.org/forosuse/archive/index.php/t-5058.html>

## Eliminar banner de OpenSSH en Debian

```
DebianBanner " "
```

## Actualizar en masa los host keys

Ejemplo si usamos algun sistema que nos deja la configuración en archivos con nombres de ip y queremos que se actualicen todas sus host keys podemos ejecutar el siguiente comando :

```
for i in `find  |grep -v CVS | grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b"` ; do ssh-keyscan $i >> ~/.ssh/known_hosts ; done
```
