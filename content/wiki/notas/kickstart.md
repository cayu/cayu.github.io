---
title: "Kickstart"
date: 2015-01-09T16:02:09-03:00
---

//Muchos administradores de sistemas preferieren usar un método de instalación automatizado para instalar Red Hat Enterprise Linux en sus máquinas. Para cubrir esta necesidad, Red Hat creó el método de instalación kickstart. Usando kickstart, un administrador de sistemas puede crear un archivo único que contiene las respuestas a todas las preguntas que normalmente se hacen durante una instalación típica.

Los archivos kickstart se pueden mantener en un servidor de sistema único y ser leídos por computadores individuales durante la instalación. Este método de instalación puede soportar el uso de un sólo archivo kickstart para instalar Red Hat Enterprise Linux en múltiples máquinas, haciéndolo ideal para administradores de sistemas y de red.

Kickstart proporciona una forma para que los usuarios puedan automatizar la instalación de Red Hat Enterprise Linux.//

En resumen cuando realizamos la instalacion de Red Hat, en este caso un EL5 se crea un archivo /root/anaconda-ks.cfg que tiene los datos necesarios para hacer una instalacion duplicada

```bash>
En este paso podemos ver que tiene informacion de los volumenes logicos a crear, el tipo de sistema de archivos y los grupos de paquetes a instalar, en este caso solo spanish-support y virtualizacion. 

Ademas se incluyo el numero de suscripcion de Red Hat Netwok, dispositivo de red, medio de instalacion, la zona horaria y el password de root. Al realizarse la instalacion lo unico que nos pide es que metamos los cds en la lectora de uno en uno. Habria que pobrar el metodo http aver si es mas automatizada la instalacion cosa que pregunte menos o sea mas "SIMPLE"

Red Hat 5 en algunas maquinas para poder bootear debe tener la opcion de booteo acpi=off ya que si no se cuelga el booteo


===== Pasos necesarios =====


 Las instalaciones Kickstart pueden realizarse usando un CD-ROM, un disco duro local o a través de NFS, FTP o HTTP.

Para usar kickstart se debe:

  -       Crear un archivo kickstart.
  -       Crear un medio de arranque con el archivo kickstart o hacer que el archivo kickstart esté disponible a través de la red.
  -       Hacer que el árbol de instalación esté disponible.
  -       Iniciar la instalación kickstart.




Luego una vez configurador el kickstart, necesitamos tener definidos estos parametros

 Para actualizaciones kickstart, se requieren los siguientes items:

    *   Idioma
    *   Método de instalación
    *   Especificación de dispositivos (si el dispositivo es necesario para ejecutar la instalación)
    *   Configuración del teclado
    *   Configuración del gestor de arranque


{{notas:kickstart.png|}}


===== Explicacion del archivo Kickstart =====


  * **install** //Aca se explica que debe hacer la ejecucion de Kickstart, en este caso intalar, en este caso le informa al sistema que instale un sistema en vez de que actualice uno ya existente. Este es el modo por defecto. Debe especificar el tipo de instalación: cdrom, harddrive, nfs o url (para FTP o HTTP).// (Opcional)
    * **cdrom**  //Especifica desde que medio se va instalar, http, cdrom etc// (Requerido)
    * **harddrive** --partition=hdb2 --dir=/tmp/install-tree
    * **url** --url http://servidorlocal/directorio
    * **nfs** --server=nfsserver.example.com --dir=/tmp/install-tree

  * **key 6c2e-0645-686a-4a06** //Codigo de activacion de suscripcion, necesario para Xen//

  * **lang es_ES.UTF-8** //Configuracion de los locales// (Opcional)

  * **keyboard es** //Configura el tipo de teclado del sistema// (Requerido)
    * be-latin1, bg, br-abnt2, cf, cz-lat2, cz-us-qwertz, de, de-latin1, de-latin1-nodeadkeys, dk, dk-latin1, dvorak, es, et, fi, fi-latin1, fr, fr-latin0, fr-latin1, fr-pc, fr_CH, fr_CH-latin1, gr, hu, hu101, is-latin1, it, it-ibm, it2, jp106, la-latin1, mk-utf, no, no-latin1, pl, pt-latin1, ro_win, ru, ru-cp1251, ru-ms, ru1, ru2,  ru_win, se-latin1, sg, sg-latin1, sk-qwerty, slovene, speakup,  speakup-lt, sv-latin1, sg, sg-latin1, sk-querty, slovene, trq, ua,  uk, us, us-acentos

  * **network --device eth0 --bootproto dhcp** // Configura la información de la red para el sistema. Si la instalación kickstart no requiere redes (en otras palabras, no es instalado sobre NFS, HTTP, o FTP), no se configurará la red para el sistema. Si la instalación no requiere redes y la información de redes no se proporciona en el archivo kickstart, el programa de instalación asume que la instalación debería ser realizada sobre eth0 a través de una dirección IP dinámica (BOOTP/DHCP) y configura el sistema instalado final a que determine su dirección IP dinámicamente. La opción network configura la información de red para instalaciones kickstart a través de la red así como también para el sistema instalado. Se muestra placa de red a usar y metodo de configuracion, statico, dhcp etc // (Opcional)
     * static --ip=10.0.2.15 --netmask=255.255.255.0 --gateway=10.0.2.254 --nameserver=10.0.2.1

  * **rootpw --iscrypted $1$56iPwDr8$C59yfOfHe/LZNdmEaGYh4/** //Password para el usuario root// (Requerido)

  * **firewall --enabled --port=22:tcp** //Configuracion de iptables// (Opcional)
     * firewall --enabled|--disabled [--trust=] <dispositivo> [--port=]

  * **authconfig --enableshadow --enablemd5** //Establece las opciones de autenticación para el sistema. Es similar al comando authconfig, el cual puede ser ejecutado después de la instalación. Por defecto, las contraseñas son encriptadas y no se utilizan sombras.//

  * **selinux --enforcing** //Configuracion de selinux, recomendado targeted//

  * **timezone --utc America/Argentina/Buenos_Aires** //Zona horaria//

  * **bootloader --location=mbr --driveorder=cciss/c0d0** //Especifica cómo se debe instalar el gestor de arranque. Esta opción es requerida tanto para las instalaciones como para las actualizaciones. En este caso en el primer disco de controlador RAID HP// (Requerido)

  * **clearpart --all --drives=cciss/c0d0** //Remueve las particiones del sistema antes de la creación de nuevas particiones. Por defecto no se remueve ninguna partición.//

  * **part /boot --fstype ext3 --size=100 --ondisk=cciss/c0d0** //Crear particion a montar en boot//

  * **part pv.11 --size=0 --grow --ondisk=cciss/c0d0** //Crear particion LVM//

  * **volgroup VolGroup00 --pesize=32768 pv.11** //Crear grupo de volumenes logicos//

  * **logvol / --fstype ext3 --name=LogVol00 --vgname=VolGroup00 --size=15184** //Crear y formatear volumen logico dentro de un LVM// (Opcional)

  * **logvol swap --fstype swap --name=LogVol01 --vgname=VolGroup00 --size=596 --grow --maxsize=992** //Crear volumen logico de swap//
    * logvol <mntpoint> --vgname=<nombre> --size=<tamaño> --name=<nombre><opciones>

  * **%packages** //Paquetes a instalar en particular si hubiera//

  * **@spanish-support** //Instalar soporte español//

  * **@virtualization** //Instalar soporte de virtualizacion//

  * **%post** //Comandos a ejecutar despues de terminada la instalacion de Red Hat//



====== Kickstart + XEN ======

Kickstart para proxys virtuales, veremos un ejemplo de recuperacion de un proxy almacenado en [[manuales:servidor_systemimager|systemimager]] y automágicamente vuelto a generar con Kickstart.

===== Introducción y tareas=====



Confeccion del archivo de configuracion de arranque, en este archivo indicamos que el sistema base Domain0, solo utilize 96M de memoria fisica,  deje el resto para la ejecucion de la maquina virtual.


== /boot/grub/grub.conf ==

<code>
default=0
timeout=5
splashimage=(hd0,0)/grub/splash.xpm.gz
hiddenmenu
title Red Hat Enterprise Linux Server (2.6.18-8.el5xen)
        root (hd0,0)
        kernel /xen.gz-2.6.18-8.el5  dom0_mem=96M
        module /vmlinuz-2.6.18-8.el5xen ro root=/dev/VolGroup00/LogVol00
        module /initrd-2.6.18-8.el5xen.img
```

Ademas debieron crearse diferentes volumenes logicos dentro de grupo de volumenes ya existente para que el servidor virtual se ubique alli.

```
lvcreate -L 15G -n root Volgroup00
lvcreate -L 3G  -n var-log-squid VolGroup00
lvcreate -L 10G -n var-spool-squid VolGroup00
lvcreate -L 1G  -n swap VolGroup00
```

Dejando como resultante esta tabla. En la columna 1 se ve el volumen real, en la otra se ve la representacion virtual de como lo ve el sistema operativo virtual, su tamaño real y sistema de archivos.

| Volumenes                       | Virtual   | Tamaño | Montaje          | Filesystem |
|---------------------------------|-----------|--------|------------------|------------|
| /dev/VolGroup00/root            | /dev/hda1 | 15 GB  | /                | ext3       |
| /dev/VolGroup00/var-log-squid   | /dev/hda2 | 3 GB   | /var/log/squid   | ext3       |
| /dev/VolGroup00/var-spool-squid | /dev/hda3 | 10 GB  | /var/spool/squid | ext3       |
| /dev/VolGroup00/swap            | /dev/hda4 | 1 GB   | swap             | swap       |

Al terminar la instalacion se debian crear un archivo de configuracion para su posterior ejecucion con Xen.

##### /etc/xen/proxy1.cfg

```
# Automatically generated xen config file
name = "proxydmz1"
memory = "1916"
disk = [ 'phy:/dev/mapper/VolGroup00-root,hda1,w','phy:/dev/mapper/VolGroup00-var--log--squid,hda2,w','phy:/dev/mapper/VolGroup00-var--spool--squid,hda3,w','phy:/dev/mapper/VolGroup00-swap,hda4,w']

vif = [ 'mac=00:16:3e:16:aa:98, bridge=xenbr0', ]

uuid = "57b1acec-e644-6ed7-0bb7-3153a510df0e"
bootloader="/usr/bin/pygrub"
vcpus=2
on_reboot   = 'restart'
on_crash    = 'restart'
```

## Objetivos

En caso de desastre recuperar magicamente los servidores, que al ser virtuales no dependeran del hardware, por lo tanto el unico que se vera afectado sera el Domain0, al bootear el DomainU no notara ninguna diferencia y por lo tanto estara operativo la instante siendo antes obtenido desde el servidor de imagenes,

#### Archivos creados/o modificados

| Archivo   | Descripción         |
|-----------|---------------------|
| proxy.cfg | Kickstart del proxy |

## Pruebas realizadas

## Referencias y Notas personales

FIXME Ver en que servidor web de la red crear un repositorio o directorio que contenta todos los kickstart para ser accedido por las instalaciones de Red Hat.

# Referencias

Guia documentacion oficial de Red Hat sobre instalacion

<http://www.redhat.com/docs/manuals/enterprise/RHEL-5-manual/es-ES/Installation_Guide/ch-kickstart2.html>

# Notas

FIXME Terminar arreglar y completar

FIXME! Arreglar para ser presentable a un usuario o cliente, redactar hacerlo impersonal etc y completar
