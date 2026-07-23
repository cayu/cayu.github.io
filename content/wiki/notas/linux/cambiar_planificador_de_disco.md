---
title: "Cambiar planificador de acceso a disco"
date: 2015-01-16T11:41:13-03:00
---

*Planificación de E/S (entrada/salida) es el término utilizado para describir el método mediante el cual los sistemas operativos deciden una cosa el orden por el cual se van a enviar las peticiones de lectura y escritura al disco.*

Este planificador lo que hace a groso modo es reorganizar las operaciones de E/S que le van llegando de una manera optima para que los movimientos mecanicos del disco sean los menos posibles. En determinadas ocasiones en las que no tiene mucho sentido este tipo de comportamiento, como podria ser con discos SSD que no tienen parte mecanica o cuando el acceso al disco se esta gestionando desde otra capa como es el caso de VMware.

Pues bien para esto esta el planificador de tipo noop, que es el planificador mas sencillo, es de tipo FIFO y todo lo que entra sale al disco sin ningun tipo de orden que no sea el de prioridad de entrada.

## Planificadores disponibles desde Linux 2.6

- Completely Fair Queuing (cfq): CFQ es la predeterminada en muchas distribuciones de Linux. CFQ coloca solicitudes sincrónicas presentados por los procesos en un número de colas por proceso y luego asigna intervalos de tiempo para cada una de las colas para acceder al disco. La longitud de la porción de tiempo y el número de peticiones de una cola se le permite presentar depende de la prioridad de E/S del proceso dado. Peticiones asíncronas para todos los procesos se procesan por lotes juntos en un menor número de colas, una para cada ámbito prioritario.

<!-- -->

- NOOP (noop): NOOP es el programador más sencilla de E/S para el kernel de Linux basada en el concepto de cola FIFO. Las inserciones NOOP planificador todas las solicitudes de E/S en una cola FIFO simple y solicitud implementos fusión. El planificador asume **E** / optimización del rendimiento **E** se tratará en otra capa de la jerarquía de E/S.

<!-- -->

- Anticipatory (anticipatory): Es un algoritmo para programar el disco duro E/S. Su objetivo es aumentar la eficiencia de la utilización del disco de “Previsión” operaciones de lectura sincrónica.

<!-- -->

- Deadline (deadline): El objetivo de Deadline es garantizar un tiempo de servicio de inicio de la petición. Para ello, se establece un plazo de todas las operaciones de E/S para evitar colisión de las solicitudes.

## Configuración

Para conocer el planificador de disco activo :

```
# cat /sys/block/sdc/queue/scheduler
noop anticipatory deadline [cfq]
# cat /sys/block/sda/queue/scheduler
noop anticipatory deadline [cfq] 
```

Para cambiarlo :

```
echo "noop" > /sys/block/sda/queue/scheduler
```

Para aplicarlo en masa :

```
/sys/block # for i in `ls`; do echo noop > "$i/queue/scheduler"; done
/sys/block # for i in `ls`; do cat "$i/queue/scheduler"; done
```

Para ponerlo en grub :

```
elevator=noop
```

```
lnlxccq:/proc/sys # cat /boot/grub/menu.lst

# Modified by YaST2. Last modification on Fri Oct 29 16:37:16 ART 2010 default 0 timeout 8 ##YaST - generic_mbr gfxmenu (hd0,0)/message ##YaST - activate

###Don't change this comment - YaST2 identifier: Original name: linux### title SUSE Linux Enterprise Server 11 SP1 - 2.6.32.12-0.7
    root (hd0,0)
    kernel /vmlinuz-2.6.32.12-0.7-default root=/dev/sda2 resume=/dev/sda3 splash=silent crashkernel=256M-:128M showopts vga=0x314 elevator=noop
    initrd /initrd-2.6.32.12-0.7-default

###Don't change this comment - YaST2 identifier: Original name: failsafe### title Failsafe -- SUSE Linux Enterprise Server 11 SP1 - 2.6.32.12-0.7
    root (hd0,0)
    kernel /vmlinuz-2.6.32.12-0.7-default root=/dev/sda2 showopts ide=nodma apm=off noresume edd=off powersaved=off nohz=off highres=off processor.max_cstate=1 nomodeset x11failsafe vga=0x314
    initrd /initrd-2.6.32.12-0.7-default

###Don't change this comment - YaST2 identifier: Original name: floppy### title Floppy
    rootnoverify (fd0)
    chainloader +1
```

## Referencias

- <https://www.kernel.org/doc/Documentation/block/switching-sched.txt>
