---
title: "Proyecto Servidor de Backups"
date: 2015-01-09T16:01:16-03:00
---

## Solución requerida

En este caso se necesita un sistema de backup y restauración automática de servidores Linux. La idea es mantener un equipos en el cual se almacenen las *Imagenes* de los diferentes servidores de la red y que a su vez sea simple agregar equipos a este sistema de backup.

La automatización básica se debe dar en :

- Actualización periódica de las Imágenes de los servidores
- Recuperación de un equipo a partir de una Imagen, minimizando la intervención manual dentro del procedimiento
- Que se pueda utilizar cualquier hardware disponible sin tener que perder tiempo generando configuraciones especiales
- Proceso lo mas desatendido posible

## Esquema de implementación

Para cumplir estos requerimientos se utilizó el software System Imager

### System Imager

*Se trata de una herramienta que facilita la instalación y administración de toda una red local con máquinas Linux. Los cambios hechos en un PC concreto se actualizan en el resto de la red. Incluso podemos crear un disquete de arranque que permite la clonación de un equipo, de forma que hasta se particiona y formatea el disco duro de forma automática. Systemimager le permite crear imágenes de sus instalaciones Linux. Para ello, es necesario un servidor de imágenes (debería haber suficiente espacio en disco para almacenar imágenes) y un cliente cliente denominado “golden” (es decir, el sistema del que desea hacer una imagen). Esto significa que usted tiene que instalar algún software en el servidor de imágenes y en el de su cliente “golden” para ejecutar Systemimager. Systemimager, es software libre, se basa en la arquitectura cliente-servidor, por lo que deberemos disponer de una máquina que ejecutará la función de “servidor de imágenes”, y que será aquella en la cual se guardarán los clones que realicemos y, posteriormente, podrán ser instalados. La ventaja de este sistema es que podemos llegar a clonar una máquina en Europa y, a través de Internet, instalar el mismo sistema en otra situada en los Estados Unidos. A parte del código fuente, en la página web de Systemimager también podemos encontrar paquetes precompilados para Debian (y todas las otras distribuciones que utilicen el mismo sistema de paquetes, como Ubuntu) y Red Hat (RPM, también para muchas otras distribuciones como SuSE o Mandrake).*

Puntos principales :

- Centralizado, todas las Imágenes se almacenan en un servidor central
- Actualiza los cambios realizados dentro de la Imagen no la Imagen completa, evitando así comprometer el ancho de banda de la red
- Automatizado, las actualizaciones de los datos de los servidores es automática y su intervención manual para su posterior recuperación es miníma

##### Software requerido

| Sistema Operativo | Red Hat Enterprise Linux 5.5 | <http://www.redhat.com> |
|----|----|----|
| System Imager | 4.1.6 | <http://wiki.systemimager.org> |

## Comienzo

Terminología utilizada

| Image Server | Es el servidor en el cual se almacenan las imágenes de los servidores a los cuales se les realiza el backup |
|----|----|
| Image Client | Es cualquiera de los servidores a los cuales se les realiza backup en el imageserver |
| Imagen | Se hace referencia al nombre que tendrá la imagen almacena en el **Image Sever**, preferentemente debera ser el hostname del **Image Client** |

### Servidor

Pasos para la implementación

- Se realizó una instalación base del sistema operativo
- Se descargaron los paquetes oficiales de System Imager
- Se procedieron a instalar dichos paquetes de la siguiente manera

```
# rpm -i perl-AppConfig-1.66-1.el5.rf.noarch.rpm
# rpm -i systemconfigurator-2.2.11-1.noarch.rpm 
# yum install perl-XML-Simple
# rpm -i systemimager-common-4.1.6-1.noarch.rpm 
# rpm -i systemimager-server-4.1.6-1.noarch.rpm 
# rpm -i systemimager-i386boot-standard-4.1.6-1.noarch.rpm 
```

### Nomenclatura y estructura de archivos y directorios

- **/etc/init.d/systemimager-server-rsyncd**
  - *Script de inicio para el servicio de SystemImager, parametros :* start\|stop\|status\|restart\|force-reload

<!-- -->

- **/var/lib/systemimager/**
  - *Directorio principal donde se almacenan las Imágenes y Scripts de recuperación*

<!-- -->

- **/var/lib/systemimager/images/**
  - *Directorio donde se almacenan las Imágenes de backup*

<!-- -->

- **/var/lib/systemimager/paquetes/**
  - *Directorio donde se eligieron ubicar los archivos necesarios a instalar en los clientes*

<!-- -->

- **/var/lib/systemimager/paquetes/scripts-clientes**
  - *Scripts preparados para facilitar la puesta en marcha de los clientes*
    - **activar_sync.sh**

```bash>
  * **/var/lib/systemimager/scripts/**
    * //Directorio donde se almacenan las Scripts de autoinstalación, no es recomendable modificar estos scripts//

  * **/etc/systemimager/getimage.exclude**
    * //Archivo donde configuramos los archivos y directorios que no creemos necesarios almacenar de la Imagen//<code perl>
# SELinux stuff
/selinux/*

# eventfs in SuSE
/lib/klibc/events/*

# mounted media devices not reported by mount
/media/*

# NFS stuff
/var/lib/nfs/state
/var/lib/nfs/*tab
/var/lib/nfs/*/*

# LVM caches and backups (automatically re-created at the first boot)
/etc/lvm/.cache
/etc/lvm/backup/*
/etc/lvm/archive/*

# Skip persistent names associated to the network interfaces (Ubuntu).
/etc/iftab
/etc/udev/rules.d/70-persistent-net.rules
/etc/udev/rules.d/75-persistent-net-generator.rules

# udev network persistent naming rules (Debian).
/etc/udev/rules.d/z25_persistent-net.rules
/etc/udev/rules.d/z45_persistent-net-generator.rules

# remove blkid mappings: this allows to use images cloned from golden clients
# with raid, lvm, etc. block devices and image other clients that use different
# block device types (non-raid, non-lvm, etc.) or vice versa.
/etc/blkid/blkid.tab
/etc/blkid.tab

# Cache de Squid
/var/spool/squid/*
/var/cache/squid/*
/var/log/squid/*

# Cache de paquetes de Red Hat
/var/cache/yum/*

# Otros
/tmp/*
/var/www/html/sarg/*
/var/www/squid-reports/*
```

### Clientes

- Si el equipo cliente es un Red Hat deberemos ejecutar lo siguiente

```
# rpm -i perl-AppConfig-1.66-1.el5.rf.noarch.rpm
# rpm -i systemconfigurator-2.2.11-1.noarch.rpm 
# yum install perl-XML-Simple
# rpm -i systemimager-common-4.1.6-1.noarch.rpm 
# rpm -i systemimager-i386initrd_template-4.1.6-1.noarch.rpm
# rpm -i systemimager-client-4.1.6-1.noarch.rpm
```

- En cambio si el equipo cliente es un Debian deberemos ejecutar lo siguiente

```
# apt-get install systemconfigurator 
# dpkg -i systemimager-initrd-template-i386_4.1.6_all.deb systemimager-common_4.1.6_all.deb systemimager-client_4.1.6_all.deb 
```

Para preparar el cliente System Imager

```
# si_prepareclient --server ip_systemimager

Welcome to the SystemImager si_prepareclient command.  This command may modify
the following files to prepare your golden client for having it's image 
retrieved by the imageserver.  It will also create the /etc/systemimager 
directory and fill it with information about your golden client.  All modified
files will be backed up with the .before_systemimager-4.1.6 extension.

 /etc/services:
   This file defines the port numbers used by certain software on your system.
   Entries for rsync will be added if necessary.

 /tmp/filet3MvOH:
   This is a temporary configuration file that rsync needs on your golden client
   in order to make your filesystem available to your SystemImager server.

 inetd configuration:
   SystemImager needs to run rsync as a standalone daemon on your golden client
   until it's image is retrieved by your SystemImager server.  If rsyncd is 
   configured to run as a service started by inetd, it will be temporarily
   disabled, and any running rsync daemons or commands will be stopped.  Then,
   an rsync daemon will be started using the temporary configuration file
   mentioned above.
   
See "si_prepareclient --help" for command line options.

Continue? (y/[n]): 

*********************************** WARNING *********************************** 
This utility starts an rsync daemon that makes all of your files accessible
by anyone who can connect to the rsync port of this machine.  This is the 
case until you reboot, or kill the 'rsync --daemon' process by hand.  By 
default, once you use si_getimage to retrieve this image on your imageserver, 
these contents will become accessible to anyone who can connect to the rsync 
port on your imageserver.  See rsyncd.conf(5) for details on restricting 
access to these files on the imageserver.  See the systemimager-ssh package
for a more secure method of making images available to clients.
*********************************** WARNING *********************************** 

Continue? (y/[n]): 

Using "sfdisk" to gather information about disk:
    /dev/sda

Using "sfdisk" to gather information about disk:
    /dev/sdb

WARNING: cannot find the version of LVM or LVM version is not supported!
WARNING: Couldn't find module "af_packet", assuming it's built into the kernel.
>>> Copying modules to new initrd from: /lib/modules/2.6.32-5-686...
>>> Copying modules configuration from: /lib/modules/2.6.32-5-686...
skipping directory kernel
skipping directory misc
>>> Appending insmod commands to ./my_modules_dir/INSMOD_COMMANDS...
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/thermal/thermal_sys.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/fs/nls/nls_base.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/acpi/thermal.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/usb/core/usbcore.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/scsi/scsi_mod.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/ata/libata.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/net/phy/libphy.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/block/floppy.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/usb/host/ehci-hcd.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/ata/ata_piix.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/usb/host/uhci-hcd.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/net/tg3.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/ata/ata_generic.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/lib/crc-t10dif.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/scsi/sd_mod.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/fs/exportfs/exportfs.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/fs/xfs/xfs.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/input/serio/serio_raw.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/input/mouse/psmouse.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/platform/x86/wmi.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/acpi/button.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/input/evdev.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/char/tpm/tpm_bios.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/acpi/processor.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/input/misc/pcspkr.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/char/tpm/tpm.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/char/tpm/tpm_tis.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/char/hw_random/rng-core.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/acpi/video.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/i2c/i2c-core.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/i2c/algos/i2c-algo-bit.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/gpu/drm/drm.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/gpu/drm/drm_kms_helper.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/gpu/drm/i915/i915.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/block/loop.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/md/dm-mod.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/md/dm-log.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/md/dm-region-hash.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/md/dm-mirror.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/md/dm-snapshot.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/fs/fuse/fuse.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/fs/mbcache.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/fs/jbd/jbd.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/fs/ext3/ext3.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/fs/ext2/ext2.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/net/802/stp.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/net/bridge/bridge.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/cpufreq/cpufreq_conservative.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/cpufreq/cpufreq_powersave.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/cpufreq/cpufreq_stats.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/drivers/cpufreq/cpufreq_userspace.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/arch/x86/kernel/cpu/cpufreq/speedstep-lib.ko
 >> insmod /lib/modules/2.6.32-5-686/misc/vboxdrv.ko
 >> insmod /lib/modules/2.6.32-5-686/misc/vboxnetflt.ko
 >> insmod /lib/modules/2.6.32-5-686/misc/vboxnetadp.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/net/core/pktgen.ko
 >> insmod /lib/modules/2.6.32-5-686/kernel/net/netfilter/x_tables.ko
>>> Choosing filesystem for new initrd:  cpio
>>> Creating new initrd from staging dir:  /tmp/.systemimager.0
 >> cd /tmp/.systemimager.0 && find . ! -name "*~" | cpio -H newc --create | gzip -9 > /etc/systemimager/boot/initrd.img
170149 blocks
 >> ls -l /etc/systemimager/boot/initrd.img
-rw-r--r-- 1 root root 36636427 Aug 20 16:14 /etc/systemimager/boot/initrd.img

>> Evaluating initrd size to be added in the kernel boot options
>> (e.g. /etc/systemimager/pxelinux.cfg/syslinux.cfg):
 >>	suggested value -> ramdisk_size=95314.5

>>> Using kernel from:          /boot/vmlinuz-2.6.32-5-686
 >> ls -l /etc/systemimager/boot/kernel
-rw-r--r-- 1 root root 2287264 Aug 20 16:14 /etc/systemimager/boot/kernel

Starting or re-starting rsync as a daemon.....
done!

This client is ready to have its image retrieved.  You must now run 
the "si_getimage" command on your imageserver.

Your client has been successfully prepared.  Boot kernel (copied from
this Linux distribution) and an initrd.img (generated by the
initrd_template package) can be found in /etc/systemimager/boot.

Automatically create configuration file for systemconfigurator:
  >> /etc/systemconfig/systemconfig.conf
```

Luego de ejecutado este paso, dentro de nuestro equipo cliente nos quedara un proceso como este

```
 9782 ?        Ss     0:00 rsync --daemon --port=873 --config=/tmp/filet3MvOH
```

Donde en el archivo **/tmp/filet3MvOH** tendremos algo como esto

```ini>
Aclaración : este es un archivo temporal que se genera automáticamente y su nombre no es importante.


También hay que configurar en **/etc/crontab**

<code>
02 1 * * * root /usr/local/bin/activar_sync.sh {ip_systemimager}
```

Para que re-ejecute el servicio cliente de ImageServer, ya que cada vez que se completa una recuperación de imagen se baja el servicio cliente.

**/usr/local/bin/activar_sync.sh**

```bash>
Dentro del servidor para capturar la Imagen del Cliente

<code>
# si_getimage --golden-client ip_cliente --image nombre_de_la_imagen
```

### Monitoreo

```php>
0) {
	if(@!$i) {
	    $i=1;
	}
	$imagen = preg_split("/[\s]+/",$imagen);
	$imagenes[$i]['nombre'] = $imagen[2];
	$imagenes[$i]['actualizada'] = $imagen[4];
	$imagenes[$i]['ip'] = $imagen[8];
	if($imagen[4] == $fecha_actual) {
	    $imagenes[$i]['estado'] = "ok" ;
	} else {
	    $imagenes[$i]['estado'] = "critical" ;
            $critical=1;
	}
	$i++;
    }
}

if(@$critical) {
    $head = "CRITICAL - Hubo un desfasaje en una o mas imagenes\n";
    $exit = 2;
} else {
    $head = "OK - Todas las imagenes actualizadas a la fecha\n";
    $exit = 0;
}
print $head;
foreach($imagenes as $imagen) {
    if(strlen($imagen['nombre'])<9) {
	$tab = "\t\t";
    } else {
	$tab = "\t";
    }
    if($imagen['estado'] == "ok") {
	print "OK - ".$imagen['nombre']." ".$tab.$imagen['ip']."\n";
    } else {
        print "CRITICAL - ".$imagen['nombre']."   ".$tab.$imagen['ip']." \t actualizado a : ".$imagen['actualizada']."\n";
    }
}
exit($exit);
?>
```

### Procedimiento de recuperación

Para recuperar la imagen almacenada en el servidor, para RH lo ideal es ejecutar el procedimiento kickstart, en este caso lo que hacemos es recuperar un script que ejecuta todos los comandos necesarios para generar una maquina virtual.

Dicho script obtiene el archivo /var/lib/systemimager/scripts/diskinfo/{maquinavirtual} que tiene informacion sobre la organizacion del disco y las particiones en diferentes lineas y separados por comas.

| Disco | Sistema de archivos | Tamaño | Punto de montaje |
|-------|---------------------|--------|------------------|
| hda4  | swap                | 1000   | swap             |
| hda1  | ext3                | 15119  | /                |
| hda2  | ext3                | 3024   | /var/log/squid   |
| hda3  | ext3                | 10080  | /var/spool/squid |

El Kickstart para esa maquina

```
install
url --url http://10.1.2.24/redhat/
key 6c2e0645686a4a06

lang es_ES.UTF-8
keyboard es

#network --device eth0 --bootproto static --ip 10.1.2.142 --netmask 255.255.255.0 --gateway 10.1.2.1 --nameserver 10.1.1.3 --hostname proxy1-dom0
network --bootproto=dhcp

rootpw --iscrypted $1$56iPwDr8$C59yfOfHe/LZNdmEaGYh4/
firewall --disabled
authconfig --enableshadow --enablemd5
selinux --disabled
timezone --utc America/Argentina/Buenos_Aires
bootloader --location=mbr --driveorder=cciss/c0d0

clearpart --all --drives=sda
part /boot --fstype ext3 --size=100 --ondisk=sda
part pv.100000 --size=0 --grow --ondisk=sda
volgroup VolGroup00 --pesize=32768 pv.100000
logvol / --fstype ext3 --name=LogVol00 --vgname=VolGroup00 --size=15184
logvol swap --fstype swap --name=LogVol01 --vgname=VolGroup00 --size=596 --grow --maxsize=992

%packages
@spanish-support
@virtualization
wget
rsync
kernel-xen
xen-libs

%post  --interpreter=/bin/bash

echo "$(date) post install" >> /root/post-install.log && wget http://10.1.2.24/ks/recuperar  -O /usr/local/bin/recuperar && /bin/bash /mnt/sysimage/usr/local/bin/recuperar proxydmz1-new >>  /root/post-install.log 2>&1
```

Y el archivo /var/www/html/ks/xen/{maquina} tiene informacion de la configuracion de Xen para esa maquina virtual

```
name = "proxy1"
memory = "1916"
disk = [
         'phy:/dev/VolGroup00/proxy1-new-root,hda1,w',
         'phy:/dev/VolGroup00/proxy1-var-log-squid,hda2,w',
         'phy:/dev/VolGroup00/proxy1-var-spool-squid,hda3,w',
         'phy:/dev/VolGroup00/proxy1-swap,hda4,w'
       ]

vif = [ 'mac=00:16:3e:16:cc:78, bridge=xenbr0', ]

uuid = "57b1acec-e644-6ed7-0bb7-3153a510df0e"
bootloader="/usr/bin/pygrub"
vcpus=2
on_reboot   = 'restart'
on_crash    = 'restart'
```

**/boot/grub/menu.lst**

En este archivo se especifican las opciones de booteo de la maquina física que contiene la maquina virtual,

En la linea

```
kernel /xen.gz-2.6.18-164.6.1.el5
```

se especifica el kernel de Xen (dom0) y las opciones de booteo.\
Una vez que esta instalada la maquina virtual podemos aplicar la configuración de esta manera para que solo asigne 96M de RAM a la maquina fisica (dom0),

```
kernel /xen.gz-2.6.18-164.6.1.el5 dom0_mem=96M
```

luego en la configuración de la maquina virtual de Xen le asignamos la memoria RAM sobrante, ya que esta depende cuanta memoria tenga la maquina que consigamos para esta tarea.

```
#boot=/dev/cciss/c0d0
default=0
timeout=5
splashimage=(hd0,0)/grub/splash.xpm.gz
hiddenmenu
title Red Hat Enterprise Linux Server (2.6.18-164.6.1.el5xen)
        root (hd0,0)
        kernel /xen.gz-2.6.18-164.6.1.el5 dom0_mem=96M
        module /vmlinuz-2.6.18-164.6.1.el5xen ro root=/dev/VolGroup00/LogVol00
        module /initrd-2.6.18-164.6.1.el5xen.img
```

Script de recuperacion de la VM

```bash>
> /tmp/vgs
#		read -p "Encontre mas de un VG, cual tengo que usar? $(vgdisplay) : " 
#		grep $REPLY /tmp/vgs && vg=$REPLY
#	done
#	;;
esac

echo -n "Obteiniendo informacion sobre las particiones para $domU desde $imageserver..."
rsync  $imageserver::kickstart/xen/$domU.diskinfo /tmp/diskinfo-file
error $?
mkdir -p /mnt/$domU

#Calculo de espacio libre en el VG
vgfree=$(vgs VolGroup00 --noheadings --units M -o vg_free|cut -dM -f1|tr -d " "| cut -d, -f1)
vgfree=`echo "tmp=$vgfree; tmp /= 1;tmp" | /usr/bin/bc`

for DISK  in $( cat /tmp/diskinfo-file ); do
	SIZE=$(echo $DISK | cut -d, -f3)
	TSIZE=$(($TSIZE +SIZE))
	TSIZE=`echo "tmp=$TSIZE; tmp /= 1; tmp" | /usr/bin/bc`
done

echo "En total voy a necesitar $TSIZE Mb "

if [ $vgfree -lt  $TSIZE   ]; then
	echo "No hay espacio suficiente en el VG seleccionado, tengo $vgfree Mb, necesito $TSIZE Mb"
	exit 1
fi

for DISK  in $(cat /tmp/diskinfo-file | sort -k4 -t ,); do

DEV=$(echo $DISK | cut -d, -f1)
FSTYPE=$(echo $DISK | cut -d, -f2)
SIZE=$(echo $DISK | cut -d, -f3)
MNT=$(echo $DISK | cut -d, -f4)

    case $MNT in
	'/')
	lvname=$domU-root
	;;
	'swap')
	lvname=$domU-swap
	;;
	*)
	lvname=$domU-$( echo $MNT | sed s%/%-%g | /bin/cut -c1 --complement)
	;;
    esac

echo -n  "Creando LV $lvname... "
if [ -e /dev/$vg/$lvname ]; then
	echo "$lvname YA existe, error"
	exit 1
else
    lvcreate -L$SIZE -n $lvname $vg > /dev/null
    error $?
    if [ $FSTYPE = "swap" ]; then
	mkswap /dev/$vg/$lvname
    else
	echo -n "Formateando /dev/$vg/$lvname..."
	mkfs.$FSTYPE -q  /dev/$vg/$lvname
	error $?
	mkdir -p /mnt/${domU}${MNT}
	echo -n "Montando ${MNT}..."
	mount /dev/$vg/$lvname /mnt/${domU}${MNT}
	error $?
    fi
fi

done

echo -n "Corriendo Rsync..."
rsync  -vaSH --quiet  --numeric-ids --exclude=/dev/* --exclude=/tmp/*  $imageserver::$domU/* /mnt/$domU/ > /dev/null
error $?

#for DISK in $(cat /tmp/diskinfo-file | sort -k4 -t ,); do
for DISK in $(cat /tmp/diskinfo-file ); do
    FSTYPE=$(echo $DISK | cut -d, -f2)
    MNT=$(echo $DISK | cut -d, -f4)
    if [ $FSTYPE != 'swap' ]; then 
	echo -n "Desmontando ${MNT}..."
	umount /mnt/${domU}${MNT}
	error $?
    fi
done

echo -n "Buscando configuracion de Xen..."
rsync  $imageserver::kickstart/xen/$domU.cfg /etc/xen/$domU.cfg > /dev/null
error $?

#if  [ $xm == '1' ]; then
#    echo -n "Arrancando vm..."
#    xm create -c /etc/xen/$domU.cfg
#fi

ln -s /etc/xen/$domU.cfg /etc/xen/auto/

# Ajustando parametros de booteo del sistema
sed -e 's/kernel.*/& dom0_mem=96/g' /boot/grub/grub.conf > /tmp/grub.conf
```

Lo único que tendremos que editar es el archivo de configuración de la maquina virtual de Xen, ajustandole el parametro memory acorde a los recursos prensentes en el hardware nuevo.

<img src="/manuales/procedimiento_imageserver.png" class="align-center" alt="procedimiento_imageserver.png" />
