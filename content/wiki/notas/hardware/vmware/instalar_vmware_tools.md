---
title: "Instalar VMware Tools"
date: 2015-04-28T11:39:59-03:00
---

Desde el vcenter seleccionamos la máquina a instalar, botón derecho-\>Guest-\>install/Upgrade vmware tools. Desde la consola de la máquina virtual montamos el cdrom, copiamos las VMWare Tools en un directorio temporal, las descomprimimos y ejecutamos el script de instalación:

```
# mount /dev/cdrom /media
# cp -a /media/cdrom/VMwareTools*.gz /tmp/.
# cd /tmp
# sudo tar -xzvf VMwareTools*.gz
# cd vmware-tools-distrib/
# ./vmware-install.pl
```

Al ejecutar el script nos hara una serie de preguntas, responder a todas pulsando ‘enter’ y así dejar todas las opciones por defecto.

Al final de la instalación se nos propone instalar el controlador de red de VMware (muy recomendado):

```
# /etc/init.d/networking stop
# rmmod pcnet32
# rmmod vmxnet
# depmod -a
# modprobe vmxnet
# /etc/init.d/networking start
```

#### mediante rpm

Para instalar las vmware tools desde el paquete rpm

```
 rpm -ivh VMwareTools-4.0.0-xxxxxx.i386.rpm
```

Una vez instalado el paquete ejecutar desde la consola

```
 # vmware-config-tools.pl 
```

Por último para instalar el driver de red

```
 /etc/init.d/network stop
rmmod pcnet32
rmmod vmxnet
modprobe vmxnet
/etc/init.d/network start
```

# Instalación VMWare Tools desde repositorio en Centos 5

Lo primero será habilitar un repositorio de vmware para poder instalar las herramientas.

```
 # rpm --import http://packages.vmware.com/tools/VMWARE-PACKAGING-GPG-KEY.pub 
```

En /etc/yum.repos.d crear un archivo llamado vmware-tools.repo

```
 # vim /etc/yum.repos.d/vmware-tools.repo 
```

y añadir dentro lo siguiente.

```
[vmware-tools]
name=VMware Tools for Red Hat Enterprise Linux $releasever – $basearch
baseurl=http://packages.vmware.com/tools/esx/<version esx>/rhel5/<arch>
enabled=1
gpgcheck=1

  * <version esx> = Sustituimos por la versión esx que tengamos instalada. (3.5u2, 3.5u3, 3.5u4, 4.0 ó 4.0u1).
  * <arch> = Sustituimos por la arquitectura de nuestro procesador. Para 32-bits sería i686 y para 64-bits x86_64.
```

Instalar con yum

```
# yum install vmware-tools
```

# Configuración de drivers al inicio del sistema

El disco RAM inicial o initrd, por sus siglas en inglés es un sistema de archivos temporal usado por el núcleo Linux durante el inicio del sistema. Es usado típicamente para hacer los arreglos necesarios antes de que el sistema de archivos raíz pueda ser montado.

En SuSE la configuración de lo que vamos a cargar en dicha imagen debemos configurarlo en el archivo **/etc/sysconfig/kernel**

Por ejemplo en un servidor que utiliza controladores SCSI Virtuales de VMware con la propiedad Paravirtual la configuración es asi :

```bash>
La configuración es igual a cualquier otra, con la salvedad que se agrega el controlador //vmw_pvscsi//


Es recomendable si tenemos SCSI Paravirtualizado cargar el controlador al final

<code>
INITRD_MODULES="vmxnet3  vmxnet thermal mptspi ata_piix ata_generic piix ide_pci_generic processor fan jbd ext3 edd vmw_pvscsi "
```

En otro servidor que no utiliza controlador SCSI Paravirtualizado la configuración es la siguiente :

```bash>
Luego correr mkinitrd


====== Otras referencias ======

===== SuSE 11 SP2 =====

En esta versión de SuSE podemos tener problemas al instalar las VMWare tools

Hay que agregar al archivo /etc/sysconfig/kernel
<code>
INITRD_MODULES=""
```

[VMware Tools fails to install on SLES 11 with the error: Unable to configure the initrd modules file at /etc/sysconfig/kernel. Execution aborted. (2035987)](http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2035987)
