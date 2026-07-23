---
title: "Linux"
date: 2018-11-14T17:16:43-03:00
---

- [Medición de velocidad de cambio en filesystems en un sistema Linux (físico o virtual)](/notas/linux/medicion_de_velocidad_de_cambio_en_filesystems_en_un_sistema_linux_fisico_o_virtual/)

## Hardware

## Sistema Operativo

#### Generar estructura de directorios del 1 al 12

```bash>
=== Mostrar permisos númericos de archivos ===
<code>
stat -c "%a %n" *
```

#### Renombrar archivos con \_ en vez de espacios

```bash>
=== Optimizar el formato de un disco USB para archivos grandes ===
<code>
mkfs -t ext4 -m 0 -O sparse_super -T largefile4 /dev/sdb1
echo 1024 > /sys/block/sdb/device/max_sectors
```

#### Reloading inittab without reboot

Para recargar nuestro inittab sin tener que reiniciar debemos ejecutar “/sbin/init q”

#### Ver cual de los archivos abiertos es el mas grande

```
# lsof  -nn | sort -k7 -n
```

#### Renombrar múltiples archivos

Por ejemplo si tenemos archivos **\*.cfg** y queremos cambiarle la extension de .cfg a .cfg.old

```
rename s/.cfg/.cfg.old/ *.cfg
```

#### Renombrar todos los archivos de minúsculas a mayúsculas (o viceversa)

De minúsculas a mayúsculas:

```
$ find . -type f|while read f; do mv $f `echo $f |tr "[:lower:]" "[:upper:]"`; done
```

De mayúsculas a minúsculas:

```
$ find . -type f|while read f; do mv $f `echo $f |tr "[:upper:]" "[:lower:]"`; done
```

Ref.: <http://www.linux-os.com.ar/linuxos/renombrar-todos-los-archivos-de-minusculas-a-mayusculas-o-viceversa/>

#### Ordenar procesos medio muertos

```
top -b -n 1 | awk '{if (NR <=7) print; else if ($8 == "D") {print; count++} } END {print "Total status D: "count}'
ps axu | awk '{if (NR <=7) print; else if ($8 == "D") {print; count++} } END {print "Total status D: "count}'
```

#### QLogic FC HBA LUN Scan Utility

This utility scans for the newly added LUNs. After adding the new LUNs it is not required to unload/load the QLogic FC driver or reboot the system. To see the newly added LUNs, just run the ql-scan-lun.sh utility.

The simplest way to use this utility is to run

      # ./ql-scan-lun.sh

By default the utility would re-scan the QLogic HBAs for new LUNs.

[ql-scan-lun-1.6.tgz](/notas/ql-scan-lun-1.6.tgz)

#### Rescan SCSI BUS Script

Linux allows you to add and remove SCSI devices without rebooting by using the echo “scsi add-single-device H C I L” \> /proc/scsi/scsi command (H = host, C = channel, I = SCSI ID, L = SCSI LUN). The remove-single-device command works similarily. Note, however, that the SCSI bus was NOT designed for hot-plugging, so you might be out of luck … And you have to be sure, that termination is OK. All filesystems on a device have to be unmounted before disconnecting it or powering it down.

[rescan-scsi-bus.sh-1.48.gz](/notas/rescan-scsi-bus.sh-1.48.gz)

[rescan-scsi-bus.sh-1.56.gz](/notas/rescan-scsi-bus.sh-1.56.gz)

<http://www.garloff.de/kurt/linux/>

#### Reescanear tabla de particiones

Esto es útil por ejemplo si agregamos un disco desde VMWare o si estiramos un disco y el SO todavia no lo ve.

Desde una llamada al sistema :

```
echo 1 > /sys/block/sdd/device/rescan
fdisk -l
mkfs.ext3 /dev/sdXY
```

O podemos hacerlo ejecutando la utilidad **partprobe** sin argumentos, desde una shell con root.

#### Copia de seguridad con Rsync

```
rsync -av --progress --rsh ssh root@servidor:/ .
```

```
rsync --progress -av --delete /home/sergio/ /media/disk/sergio-home/
```

- progress: nos mostrará el porcentaje de ficheros copiados
- a: Archive, es una forma rápida de decirle que es una copia recursiva y que queremos que todo sea copiado
- v: Verbose, le indica a rsync que de información en pantalla sobre cada transacción
- z: Compress, comprime los ficheros para utilizar menor ancho de banda, así son copiados más rápidamente
- delete: borrará todos los ficheros en el folder destino, que han sido borrados en el folder origen, lo que los hacer estar sincronizados

**script_rsync.sh**

```bash>
> ${TMPHOSTCHECK}

if [ -s ${TMPHOSTCHECK} ]; then
                PROPERNAME=`cat /etc/hosts | egrep $2\$ | head -n 1 | awk '{print $2}' | cut -f-1 -d.`
                        if [ ${PROPERNAME} != $2 ]; then
                                echo Hostnames do not match - $2 - ${PROPERNAME}
                                echo exiting
                                exit
                        fi
                rm -rfd ${TMPHOSTCHECK}
        else
                echo host does not exist!!!!
                echo exiting
                rm -rfd ${TMPHOSTCHECK}
                exit
fi

if [ -d ${REPOSITORY}/${HOSTDIR} ]; then
                echo ${REPOSITORY}${HOSTDIR} exists
        else
                mkdir ${REPOSITORY}/${HOSTDIR}
fi

for i in "${ARCHIVOS[@]}"
do
    rsync -avz --progress -e 'ssh ' ${USER}@${HOSTNAME}:$i ${REPOSITORY}${HOSTDIR}
done

;;

'help')
echo help screen
;;

*) echo "usage: $0 <backup server|help>"
esac
```

#### Buscar archivos de determinado tamaño

```
find ./ -type f -size +40000k -exec ls -lh {} \; | awk '{ print $9 ": " $5 }
```

#### Formateador de disco para unidades USB

ufiformat is a command-line utility for formatting floppy disks in UFI- compatible USB floppy drives. It allows disks to be formatted in any format supported by the drive, and can also be used to determine what format a disk is currently using.

Homepage: <http://www.geocities.jp/tedi_world/format_usbfdd_e.html>

```
This is formatting disk utility for USB floppy devices.

Usage: ufiformat [OPTION]... [DEVICE]
Format a floppy disk in a USB floppy disk DEVICE.

  -f, --format [SIZE]  specify format capacity SIZE in KB
                       without -f option, the format of the current media will be used.
  -F, --force          do not perform any safety checks
  -i, --inquire        show device information, instead of performing format
                       without DEVICE argument, list USB floppy disk devices
  -v, --verbose        show detailed output
  -q, --quiet          suppress minor output
  -h, --help           show this message
Examples:
        	To format a media keeping its current geometry:  	ufiformat /dev/sda
        	To format a media for 2hd(1.44MB):	ufiformat -f 1440 /dev/sda
        	To list USB floppy devices:	ufiformat -i
        	To show USB floppy device information:	ufiformat -i /dev/sda
To use this program, both /dev/sd* and /dev/sg* need to be accessible.
High-level formatting is usually needed after using this program(ex. mkfs.vfat -I /dev/sda).
```

#### Configurar protección/prioridades en YUM

El propósito del plugin protectbase es proteger ciertos repositorios de las actualizaciones de otros repositorios. Los repositorios que sean protegidos no serán actualizados por ficheros nuevos en repositorios que no estén protegidos. Este plugin es recomendado para todos aquellos que rutinariamente habilitan repositorios de terceros, debido a que estos repositorios que no son de CentOS pueden actualizar ciertos ficheros del sistema y causar potencialmente que su instalación de CentOS se vuelva inestable.

```
yum install yum-protectbase
```

Después que el plugin esté instalado, asegúrese de que esté habilitado. Edite el fichero /etc/yum/pluginconf.d/protectbase.conf y asegúrese de que contenga las líneas siguientes:

```
[main]
enabled = 1
```

Luego usted necesita editar todos los ficheros .repo en /etc/yum.repos.d/ y adicionar:

```
protect=1
```

o

```
protect=0
```

Si tenes RH5 con las suscripciones pagas y todo, hay que editar este archivo

**/etc/yum/pluginconf.d/rhnplugin.conf**

```
[main]
enabled = 1
gpgcheck = 1

# You can specify options per channel, e.g.:
#
[rhel-i386-server-5]
enabled = 1
protect = 0
#
#[some-unsigned-custom-channel]
#gpgcheck = 0
```

Ref.:<http://wiki.centos.org/es/PackageManagement/Yum/ProtectBase>

#### Cambiar la MAC Address

Primero vemos la MAC Address que tenemos asignada fisicamente

```
$ ifconfig -a | grep HWaddr
eth0  Link encap:Ethernet HWaddr 00:11:85:ee:25:61
```

Luego procedemos a cambiarla en modo root

```
# ifconfig eth0 down
# ifconfig eth0 hw ether 00:11:85:ee:25:71
# ifconfig eth0 up
# ifconfig eth0 |grep HWaddr
```

#### Servicios al inicio del Sistema

- Red Hat
  - **chkconfig servicio on** o **chkconfig servicio off**

<!-- -->

- SuSE
  - **insserv -d servicvio**

<!-- -->

- En ambas distribuciones para ver los estados de ejecucion de los servicios
  - **chkconfig –list**
  - Ademas se muestra un apartado con los servicios activos de xinetd

<!-- -->

- Debian
  - **update-rc.d servicio defaults**

#### Locales y Tomcat

A veces cuando usamos aplicaciones que parcialmente utilizan Tomcat, como es el caso de MicroStrategy podemos tener diferencias con los reportes generados en via web y los generados desde el Intelligence Server, si los generados via web por ejemplo tienen incorrecto los separadores de coma y simbolos de moneda es muy probable que sean las locales, podemos fijarnos en modificar ciertas opciones de arranque del Tomcat.

**/etc/init.d/tomcat5** (al principio del archivo)

```bash>
**/etc/profile** (al final del archivo)
<code bash>
export LC_ALL="es_AR.utf8"
```

#### HOW TO Run SAP GUI On LINUX To Connect SAP/R3 Systems

I have created this document for people who need to install SAPGUI into Linux.

1\) Minimum system requirements.\
Linux\
Hardware: CPU: Intel PentiumIII with 800 MHz\
RAM: 256MB\
Software:\
OS: SuSE Linux 9.3 - 10.1, Red Hat Enterprise Linux 3 and 4\
JVM: Sun Java Runtime Evironment 1.4.2 (32-bit) or 5.0 (32-bit) including Java Plugin\
(please see:\
<http://java.sun.com/j2se/1.4.2/install-linux.html> <http://java.sun.com/j2se/1.4.2/docs/>…ocale.doc.html\
<http://java.sun.com/j2se/1.5.0/install-linux.html> <http://java.sun.com/j2se/1.5.0/docs/>…ocale.doc.html for details)\

2\) Download SAPGUI from:

<ftp://ftp.sap.com/pub/sapgui/java/> (anonymous login)

<https://websmp201.sap-ag.de/sapgui-java> (OSS user and pass)

3\) At the /java folder in ftp select:

File: PlatinGUI-Linux-700.jar 37926 KB 11/07/06 13:58:00

(note: a more recent version may be available)

4\) In the downloaded folder, execute

Code:

```
java -jar PlatinGUI-Linux-700.jar
```

5\) Executing SAPGUI

a\) Guilogon:

Code:

```
~/SAPClients/SAPGUI7.00/bin$ ./guilogon
To create servers, touch at new and go to "advanced".
```

– tip “use expert configuration”

Example:

For server 10.1.1.6 with system number 01:

Code:

```
conn=/H/10.1.1.6/S/3201
```

B\) Command line

For server 10.1.1.6 with system number 01:

Code:

```
~/SAPClients/SAPGUI7.00/bin$ ./sapgui /H/10.1.1.6/S/3201
```

If your server using load balancing configuration, use this connection string **conn=/M/(IP_Address_Message_Server)/S/36(system_number)/G/(nama_group)** and use this connection string configuration if yor SAP R/3 server are not using load balancing system, **conn=/H/(IP_Address_server_SAP)/S/32(system_number)**.

<http://sapbasis.wordpress.com/2007/08/22/installation-and-configuration-of-sapgui-for-java-on-linux/>

<http://www.linuxquestions.org/linux/answers/Applications_GUI_Multimedia/HOW_TO_Run_SAP_GUI_On_LINUX_To_Connect_SAP_R3_Systems>

#### SAP SysInfo

Script para colectar informacion de tu sistema SAP

[sapsysinfo.zip](/notas/sapsysinfo.zip)

#### Convertidor del SAPLogon.ini de Windows al connections de SAPGui Java

[sap_gui_for_java_configuration_generator_scripts.zip](/notas/sap_gui_for_java_configuration_generator_scripts.zip)

#### Formatear lista de filesystems montados

```
# mount | column -t
/dev/sda1         on  /                         type  ext4                   (rw,errors=remount-ro,commit=0)
proc              on  /proc                     type  proc                   (rw,noexec,nosuid,nodev)
none              on  /sys                      type  sysfs                  (rw,noexec,nosuid,nodev)
fusectl           on  /sys/fs/fuse/connections  type  fusectl                (rw)
none              on  /sys/kernel/debug         type  debugfs                (rw)
none              on  /sys/kernel/security      type  securityfs             (rw)
none              on  /dev                      type  devtmpfs               (rw,mode=0755)
none              on  /dev/pts                  type  devpts                 (rw,noexec,nosuid,gid=5,mode=0620)
none              on  /dev/shm                  type  tmpfs                  (rw,nosuid,nodev)
none              on  /var/run                  type  tmpfs                  (rw,nosuid,mode=0755)
none              on  /var/lock                 type  tmpfs                  (rw,noexec,nosuid,nodev)
/dev/sda6         on  /home                     type  ext4                   (rw,user_xattr,commit=0)
rpc_pipefs        on  /var/lib/nfs/rpc_pipefs   type  rpc_pipefs             (rw)
nfsd              on  /proc/fs/nfsd             type  nfsd                   (rw)
binfmt_misc       on  /proc/sys/fs/binfmt_misc  type  binfmt_misc            (rw,noexec,nosuid,nodev)
gvfs-fuse-daemon  on  /home/sergio/.gvfs        type  fuse.gvfs-fuse-daemon  (rw,nosuid,nodev,user=sergio)
```

#### Reacer una tabla de particiones desde cero

Reacer una tabla de particiones desde cero, en por ejemplo una LUN o un disco ya usado que deje restos de otro tipo de tabla de partición incompatible.

```
parted /dev/sdc
mklabel msdos
quit
```

#### Agregar Memoria y CPU en caliente en VMware

```bash>
 "${SPARSEMEM_STATE_FILE}"
        fi
done
```

```bash>
 "${CPU_STATE_FILE}"
        fi
    else 
        echo -e "\t${CPU} already configured prior to hot-add"
    fi
done
```

#### Copia rápida de seguridad via red

En el equipo donde vamos a dejar la copia

```
netcat -l -p 7000 > stage.tgz
```

Desde donde realizamos la copia, la ip es la del equipo anterior

```
tar cfzp - /oracle/stage | netcat 192.168.1.55 7000
```

#### Cambiar cadena de texto dentro de archivos

```
find . -name '*.cfg' -type f -print0 | xargs -0 sed -i 's/cadena/reemplazo/g'

s/cadena/reemplazo/g
```

#### IPCRM para determinado usuario

```bash>
) { @a=split(/\s+/); print `ipcrm sem $a[1]`}'

ipcs -s | grep $USERNAME | awk ' { print $2 } ' | xargs ipcrm sem
```

#### Debian

##### TAB Completion Debian Net Install

```
apt-get install bash-completion
exit
login
```

##### Compilar el Kernel a lo Debian

Esto es lo mismo que hacer un *make -j*.

```
export CONCURRENCY_LEVEL=2
```

Despues de hacer el menuconfig le damos el make de esta manera.

```
make-kpkg --initrd --append-to-version=-some-string-here kernel-image kernel-headers kernel-source
```

y listo, vamos a tener dos archivos asi:

```
linux-headers-3.0.6-13-generic_3.0.6-13-generic-10.00.Custom_i386.deb
linux-image-3.0.6-13-generic_3.0.6-13-generic-10.00.Custom_i386.deb  
```

##### Script para modificar control file de un .deb

```bash>
Ref.: http://ubuntuforums.org/showthread.php?t=636724

===== Redes sociales =====

==== Twitter ====


Twidge es una aplicación de línea de comandos para solicitar servicios de microblogging a Twitter (funciona también para Identi.ca). Cuenta con múltiples características, a saber:

    * Es fácil de aprender.
    * Cuenta buena documentación.
    * Es compatible con el API de Twitter.
    * Puede acortar URL con tinyurl.com.
    * Está diseñado para ser amigable con scripts para el shell.


Dicho lo anterior, aprendamos Twidge en 8 sencillos pasos:

1. Instalación. De nueva cuenta, asumo un sistema tipo Debian (también puedes compilarlo para cualquier GNU/Linux).

      $ sudo apt-get install twidge

2. Configuración. Twidge te hará un par de peticiones: usuario y contraseña.

      $ twidge setup

3. Los 20 updates más recientes de tus amigos. Twidge desplegará adecuadamente el nick de tu amigo y estado en un par de columnas.

      $ twidge lsrecent

4. Actualiza tu estado.

      $ twidge update "Preparando post sobre Twidge"

5. Sigue a alguien interesante.

      $ twidge follow cayu

6. Lo contrario…

      $ twidge unfollow BillGates

7. Conoce a tus seguidores.

      $ twidge lsfollowers

8. Acorta URL largas. Twidge lo hace automáticamente.

      $ twidge update "http://software.complete.org/static/twidge/doc/html" 

Ref: http://bitelia.com/2010/02/comando-linux-twidge-un-cliente-para-twitter

===== Ubicar la Latitud y Longitud en Google Maps =====

Una vez seleccionada la dirección o el punto que queremos, en la barra de direcciones del navegador:

<code javascript>
javascript:void(prompt('',gApplication.getMap().getCenter()));
```

## Scripts para importar LOGS de Cisco Call Manager en Mysql o PostgreSQL

[cdr.tgz](/notas/cdr.tgz)

## Script de Nautilus para añadir una marca de agua a una imagen

Script muy bueno

```bash
#!/bin/bash

#******************************************************************************************
#
# Marca de agua
# Versión= 1.0
#
# Script de Nautilus que añade una marca de agua a una imagen 
# La marca de agua es otra imagen

# Script programado y probado satisfactoriamente en Debian Lenny y Gnome 2.22.3
# Esta programado pensado en ejecutarse como un script de Nautilus aunque es posible 
# usarlo en consola pasandole como parámetro el nombre del fichero de la imagen.  

#  DEPENDENCIAS
#
#  Zenity (http://freshmeat.net/projects/zenity)
#  Necesario para generar los cuadros de dialogo del script
#  
#  Imagemagick (http://www.imagemagick.org/)
#  Contiene elprograma composite  necesario para crear el borde 

# INSTALACION:
# 	1. Copiar el script al directorio /home/usuario/.gnome2/nautilus-scripts 
#	2. Dar permisos de ejecución al script
#	3. En este momento al pulsar con el botón derecho del ratón sobre una imagen nos
#	   aparece el menú Scripts y dentro de el aparece el script.

# Copyright (C) 2009 Carlos Fco. Andión López

# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 2 of the License, or
# (at your option) any later version.

# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.

# You should have received a copy of the GNU General Public License along
# with this program; if not, write to the Free Software Foundation, Inc.,
# 51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.

# http://www.gnu.org/copyleft/gpl.html

#******************************************************************************************


# Se comprueba que los programas necesarios para generar el borde estan presentes
for command in composite
do
	if [ ! $(which $command) ]
then
    	zenity --error --text "Falta el programa \"$command\" .\n
		Es necesario instalar el paquete ImageMagick."
        exit 1
    fi
done


#Se indica el fichero con la marca de agua y el porcentaje de visibilidad

	WM_FILE=`zenity --file-selection --title="Seleccione un archivo"`

	case $? in
		0)
			echo "\"$WM_FILE\" seleccionado.";;
		1)
			echo "No ha seleccionado ningún archivo.";;
			
		-1)
			echo "No ha seleccionado ningún archivo.";;
			
	esac


VISIBILITY=`zenity --title='Marca de agua' --text='¿Visibilidad en %?' --entry-text='50' --entry`


#Se inicializa la barra de progreso de zenity
FILES_COUNT="$#"
CURRENT_FILE=1

for arg;
	do
    #Se renombra el archivo para evitar sobreescribir el original y crear uno nuevo con la marca
    IMX_FILE_TARGET='wm_'"$arg"

	composite -dissolve "$VISIBILITY" "$WM_FILE" "$arg" "$IMX_FILE_TARGET"


    #Actualizo la barra de progreso de Zenity
    percentage=$(echo "$CURRENT_FILE * 100 / $FILES_COUNT" | bc)
    CURRENT_FILE=$((CURRENT_FILE + 1))
    echo $percentage

done | zenity --progress --title="Procesando imagenes"  --auto-close
```

Ref: <http://elrincondetolgalen.wordpress.com/2009/11/12/script-de-nautilus-para-anadir-una-marca-de-agua-a-una-imagen/>
