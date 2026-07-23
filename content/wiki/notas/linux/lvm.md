---
title: "Logical Volume Management 2 (LVM2)"
date: 2014-06-06T12:13:15-03:00
---

Las herramientas de LVM2 de Linux permiten un manejo más avanzado del los volúmenes de datos (filesystems, discos, etc.) respecto al común de Linux. LVM2 se podría dividir en tres partes, la primera o la más baja serían los Phisical Volumes, que como su nombre lo indica son los volúmenes físicos sobre los cuales luego se crearán los Volume Groups. Los Volume Groups son la capa intermedia que se monta sobre los Phisical Volumes, y permiten combinar las capacidades de los estos en un Volume Group del tamaño de la suma de las capacidad de los Phisical Volumes que lo integran, por ejemplo, si tenemos dos Phisical Volumes de 4Gb cada uno integrando un Volume Group, dicho Volume Group sera de aproximadamente 8Gb. Por último y en la capa superior tenemos a los Logical Volumes que son subdivisiones de los Volume Groups las cuales utilizaremos como si fuesen particiones de disco por ejemplo. Las operaciones básicas que podremos realizar sobre los Logical Voumes son

- Creación de volúmenes
- Eliminación de volúmenes
- Ampliación de volúmenes
- Reducción de volúmenes
- Concatenación de volúmenes
- Re-dimensionado de grupos lógicos
- Re-dimensionado de volúmenes lógicos
- Instantáneas de lectura y escritura)
- RAID 0 de volúmenes lógicos
- Snapshots

## Que es LVM ?

El LVM es un módulo que se le agrega al núcleo Linux y genera una abstracción entre los discos físicos y los dispositivos para accederlos. Con la ayuda de herramientas de administración, el administrador puede acceder a los beneficios de utilizar LVM. Básicamente lo que se logra es tener un nuevo dispositivo que apunta no a un disco o una partición, sino a un grupo de discos y particiones como un todo (manejar muchos espacios de disco como si fuera un único disco).

## Funcionamiento de LVM

Conceptos que se utilizan :

1.  Volumen Físico
    1.  Los Volúmenes Físicos (**PV**) son los discos o particiones de un disco
2.  Grupo de Volumen
    1.  Grupo de Volumen (**GV**) es un área que cuelga de los **PV** y se agrupan los **LV**
3.  Volumen Lógico
    1.  Los Volúmenes Lógicos (**LV**) son dispositivos donde se pueden crear sistemas de archivos

Algunos diagramas útiles que vi en la red :

<img src="/notas/linux/lvm/lvm_componentes.png" class="align-center" alt="lvm_componentes.png" />

<img src="/notas/linux/lvm/lvm_capas.jpg" class="align-center" alt="lvm_capas.jpg" />

## Niveles de un LVM

Un LVM se divide en 5 niveles, estos son:

1.  1.Volúmenes Físicos o Physical Volumes
2.  Volúmenes Lógicos o Logical Volumes
3.  Grupos de volúmenes o Volume Groups
4.  Extensión Lógica o Logical Extents
5.  Extensión Física o Physical Extents

### Volúmenes Físicos (Physical Volumes)

Son los discos duros físicos o particiones de un disco duro

### Volúmenes Lógicos (Logical Volumes)

Es el equivalente a una partición de un disco duro, por lo que puede contener un sistema de archivos como por ejemplo /home.

### Grupos de volúmenes (Volume Groups)

Es la parte que engloba los volúmenes lógicos (Logical Volumes) y nuestros volúmenes físicos (Physical Volumes), es decir, es una especie de contenedor donde se sitúan los volúmenes lógicos (Logical Volumes) y los volúmenes físicos (Physical Volumes).

### Extensión Lógica (Logical Extents)

Cada volumen físico esta divido en pequeños trozo de datos llamados extensión lógica(logical extents).

### Extensión Física (Physical Extents)

Cada volumen físico(LV) esta divido en pequeños trozos datos que se llaman extensión física(physical extents), que es del mismo tamaño que una extensión lógica(LE).

## Creación de Phisical Volumes

Al crear un Phisical Volume lo que estamos haciendo es inicializarlo para poder utilizarlo luego con LVM2 y poder crear un Volume Group, los Phisical Volume pueden ser creados a partir de discos completos, particiones de discos, meta devices y loop devices, para crear un Phisical Volume utilizaremos

```
# pvcreate [dispositivo]
```

por ejemplo

```
# pvcreate /dev/md2
```

## Creación de Volume Groups

Para crear un Volume Group necesitaremos dos cosas, primero, al menos un Phisical Volume, y un nombre para el Volume Group, la sintaxis del comando para crear Volume Group es

```
# vgcreate [nombre_del_volume_group] [Phisical Volume] 
```

por ejemplo

```
# vgcreate vg-storage1 /dev/vg-storage1
```

## Creación de Logical Volumes

Los Logical Volume son la capa superior de LVM2, en ellos crearemos los filesystems o cualquier cosa para la que necesitemos un block device. La sintaxis mas frecuente del comando es

```
# lvcreate -L [tamaño] -l [nombre] [nombre_del_volume_group]
```

se hizo: para el disco

```
# lvcreate -L 5G -nlv-wiki-root vg-storage1
```

para swap

```
 
#lvcreate -L 512M -nlv-wiki-swap vg-storage1
```

```
# lvcreate -L 5G -nlv-nagios-root vg-storage1
```

para swap

```
# lvcreate -L 512M -nlv-wnagios-swap vg-storage1
```

el comando lvcreate acepta como unidades de tamaño K para kilobyte, M para megabyte, G para gigabyte y T para terabyte, tanto en mayúsculas como en minúsculas.

Salida de los comandos **pvscan**, **vgscan** y **lvscan**.

```
xen01:~# lvscan
  ACTIVE            '/dev/vg-storage1/lv-swap' [2.00 GB] inherit
  ACTIVE            '/dev/vg-storage1/files-nagios' [40.00 GB] inherit
  ACTIVE            '/dev/vg-storage1/lv-nagios-root' [8.00 GB] inherit
  ACTIVE            '/dev/vg-storage1/lv-nagios-swap' [1.00 GB] inherit
  ACTIVE            '/dev/vg-storage1/lv-wiki-root' [5.00 GB] inherit
  ACTIVE            '/dev/vg-storage1/lv-wiki-swap' [512.00 MB] inherit
xen01:~#
```

```
xen01:~# vgscan
  Reading all physical volumes.  This may take a while...
  Found volume group "vg-storage1" using metadata type lvm2
xen01:~#
```

```
xen01:~# pvscan
  PV /dev/md2   VG vg-storage1   lvm2 [283.93 GB / 227.43 GB free]
  Total: 1 [283.93 GB] / in use: 1 [283.93 GB] / in no VG: 0 [0   ]
xen01:~# 
```

Con **vgdisplay** obtenemos informacion del Volume Group

```
xen01:~# vgdisplay
  --- Volume group ---
  VG Name               vg-storage1
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  8
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                6
  Open LV               6
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               283.93 GB
  PE Size               4.00 MB
  Total PE              72686
  Alloc PE / Size       14464 / 56.50 GB
  Free  PE / Size       58222 / 227.43 GB
  VG UUID               gq1nG2-ZqGu-P7sP-M7Yl-vhGT-nLfj-2yZRPp

xen01:~#
```

## Redimensionado de Logical Volumes

Con EXT3

**Kernel Viejo**

```
# lvextend -L+3G /dev/VolGroup00/proxy2-var-log-squid
# umount /dev/VolGroup00/proxy2-var-log-squid
# e2fsck -f /dev/VolGroup00/proxy2-var-log-squid
# resize2fs  /dev/VolGroup00/proxy2-var-log-squid
# mount /dev/VolGroup00/proxy2-var-log-squid
```

**Kernel Nuevo**

```
# lvextend -L+3G /dev/VolGroup00/proxy2-var-log-squid
# resize2fs  /dev/VolGroup00/proxy2-var-log-squid
```

## Notas

### Otros Comandos de LVM

| Comando Linux LVM | Uso |
|----|----|
| Comandos de volúmenes físicos |  |
| pvchange | Cambia los atributos de un volumen físico |
| pvcreate | Crea un volumen físico |
| pvdata | Muestra información de depuración sobre un volumen físico |
| pvdisplay | Muestra información sobre un volumen físico |
| pvmove | Mueve extensiones físicas de un volumen físico a otro |
| pvscan | Localiza y enumera todos los volúmenes físicos |
| Comandos de grupos volumen |  |
| vgcfgbackup | Hace una copia del área del descriptor de grupo volumen |
| vgcfgrestore | Recupera el área del descriptor de grupo volumen al disco |
| vgchange | Cambia los atributos de un grupo volumen |
| vgck | Comprueba la consistencia del área del descriptor de grupo volumen |
| vgcreate | Crea un grupo volumen a partir de volúmenes físicos |
| vgdisplay | Muestra los atributos de un grupo volumen |
| vgexport | Exporta un grupo volumen del sistema |
| vgextend | Añade volúmenes físicos a un grupo volumen |
| vgimport | Importa un grupo volumen al sistema |
| vgmerge | Combina dos grupos volumen |
| vgmknodes | Combina un directorio y los archivos especiales de un grupo volumen |
| vgreduce | Elimina volúmenes físicos de un grupo volumen |
| vgremove | Elimina un grupo volumen |
| vgrename | Renombra un grupo volumen |
| vgscan | Localiza y enumera todos los grupos volumen del sistema |
| vgsplit | Divide un grupo volumen |
| Comandos de volúmenes lógicos |  |
| lvchange | Cambia los atributos de un volumen lógico |
| lvcreate | Crea un volumen lógico |
| lvdisplay | Muestra los atributos de un volumen lógico |
| lvextend | Incrementa el tamaño de un volumen lógico |
| lvreduce | Reduce el tamaño de un volumen lógico |
| lvremove | Elimina un volumen lógico |
| lvrename | Renombra un volumen lógico |
| lvscan | Localiza y enumera todos los volúmenes lógicos, creando /etc/lvmtab y /etc/lvmtab.d/\* |
| Comandos del Administrador de Volúmenes Lógicos (LVM) |  |
| lvmchange | Cambia los atributos del sistema LVM |
| lvmdiskscan | Localiza y enumera todos los discos disponibles, los dispositivos múltiples y las particiones |
| lvmsadc | Recoge información sobre la actividad de LVM |
| lvmsar | Muestra información sobre la actividad de LVM |

### Como crear un LV usando todo el espacio disponible en el VG

```
lvcreate -n NAME -l 100%FREE vg0
```

### Como extender un disco de VMware y agregarlo a un VG On-Line

1.  Extender el disco de VMware
2.  echo 1 \> /sys/block/sdb/device/rescan
3.  Extender el volumén físico completo si el PV es un disco entero ó crear una partición y agregarla al VG
    1.  pvresize /dev/sdb
    2.  vgextend vg1 /dev/sdb2
4.  Realizar el extend del filesystem
    1.  lvextend /dev/mapper/vg1-lv3
    2.  resize2fs /dev/mapper/vg1-lv3

### Ver los dispositivos dm- asociados a un LV

```bash
lvdisplay|awk  '/LV Name/{n=$3} /Block device/{d=$3; sub(".*:","dm-",d); print d,n;}'
```

<http://www.linuxquestions.org/questions/linux-newbie-8/dm0-in-iostat-652771/>
