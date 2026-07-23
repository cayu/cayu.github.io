---
title: "Sistema de archivos /proc"
date: 2014-07-22T15:44:20-03:00
---

Dentro del directorio /proc/, se puede encontrar una gran cantidad de información con detalles sobre el hardware del sistema y cualquier proceso que se esté ejecutando actualmente. Además, algunos de los archivos dentro del directorio /proc/ pueden ser alterados por el usuario al kernel cambios en la configuración.

# Archivos en /proc

## /proc/buddyinfo

Este archivo se utiliza principalmente para diagnosticar problemas de fragmentación de memoria. Utilizando el algoritmo buddy, cada columna representa el número de páginas de un cierto orden (de un cierto tamaño) que están disponibles en un momento dado. Por ejemplo, para la zona DMA (acceso directo a memoria), hay 90 de 2^(0\*PAGE_SIZE) pedazos de memoria. De forma similar, hay 6 de 2^(1\*PAGE_SIZE) pedazos, y 2 de 2^(2\*PAGE_SIZE) pedazos de memoria disponibles. La fila DMA hace referencia a los primeros 16 MB en un sistema, la fila HighMem referencia toda la memoria mayor que 4 GB en un sistema, y la fila Normal se refiere a toda la memoria en medio de las anteriores.

## /proc/cmdline

Este archivo muestra los parámetros pasados al kernel en el momento en que éste inicia.

## /proc/cpuinfo

Este archivo virtual identifica el tipo de procesador usado por el sistema y sus carácteristicas.

## /proc/crypto

Este archivo lista todos los códigos de cifrado utilizados por el kernel de Linux, incluyendo detalles adicionales para cada uno.

## /proc/devices

Este archivo muestra los diversos dispositivos de carácteres y de bloque actualmente configurados (no incluye dispositivos cuyos módulos no están cargados).

## /proc/diskstats

Muestra las estadísticas de I/O de dispositivos de bloque. Cada línea contiene los siguientes 14 campos:

1.  major number
2.  minor mumber
3.  nombre del dispositivo
4.  lecturas completadas con éxito
5.  reads merged
6.  sectores leídos
7.  tiempo dedicado a la lectura (ms)
8.  escrituras completadas
9.  writes merged
10. sectores escritos
11. el tiempo dedicado a escribir (ms)
12. I/O en curso
13. el tiempo consumido en I/O (ms)
14. weighted time spent doing I/Os (ms)

Más info <https://www.kernel.org/doc/Documentation/iostats.txt>

## /proc/filesystems

Este archivo muestra una lista de los tipos del sistema de archivos soportados actualmente por el kernel.

## /proc/interrupts

Este archivo graba el número de interrupciones por IRQ en la arquitectura x86.

Dentro del archivo podemos ver su contenido dividido en columnas. La primera columna se refiere al número de IRQ. Cada CPU del sistema tiene su propia columna y su propio número de interrupciones por IRQ. La columna siguiente le indica el tipo de interrupción y la última contiene el nombre del dispositivo que está localizado en ese IRQ.

- **XT-PIC** — Interrupciones del ordenador AT antiguo que se han producido por un largo periodo de

tiempo.

- **IO-APIC-edge** — Señal de voltaje de las transacciones interrumpidas desde abajo hasta arriba,

creando una edge, en la que la interrupción IO-APIC-level, tan sólo se dan a partir de procesadores 586 y superiores.

- **IO-APIC-level** — Genera interrupciones cuando su señal de voltaje se alza hasta que la señal

desciende nuevamente.

## /proc/iomem

Aquí se muestran los mapeos actuales de memoria para cada dispositivo físico.

## /proc/ioports

La salida de /proc/ioports proporciona una lista de las regiones de puertos registrados actualmente utilizados para la comunicación de entrada y salida con un dispositivo.

## /proc/kcore

Este archivo representa la memoria física del sistema y se almacena en el formato de archivos base. A diferencia de la mayoría de archivos **/proc/**, **kcore** muestra un tamaño. Este valor se da en bytes y es igual al tamaño de la memoria física (RAM) utilizada más 4KB. Sus contenidos están diseñados para que los examine un debugger, como por ejemplo gdb, y no es legible humanamente.

## /proc/kmsg

Este archivo se utiliza para mantener mensajes generados por el kernel. Luego, estos mensajes son tomados por otros programas, como por ejemplo **/sbin/klogd** o **/bin/dmesg**.

## /proc/loadavg

Este archivo ofrece una vista de la carga promedio del procesador con respecto al tiempo de CPU y de E/S, así como también datos adicionales utilizados por uptime y otros comandos.

Esta dividido en columnas. Las tres primeras columnas indican medida CPU e IO utilización de la última, cinco, y 15 períodos por minuto. la cuarta columna muestra el número de procesos que se están ejecutando actualmente y el número total de procesos. la última columna muestra el ID de proceso usado. Además, el promedio de la carga también se refiere al número de procesos listos para funcionar (es decir, en la cola de ejecución, la espera de una cuota de CPU).

## /proc/meminfo

Este archivo roporciona mucha información importante sobre el uso actual de RAM en el sistema.

La mayoría de la información que está aquí es usada por los comandos **free**, **top** y **ps**.

- **MemTotal** — Cantidad total de RAM física en kilo bytes.
- **MemFree** — Cantidad de RAM física, en kilobytes, sin utilizar por el sistema.
- **Buffers** — Cantidad de RAM física, en kilobytes, usada para los archivos de memoria intermedia.
- **Cached** — Cantidad de RAM física en kilobytes usada como memoria caché.
- **SwapCached** — Cantidad de swap en kilobytes usada como memoria caché.
- **Active** — Cantidad total de memoria intermedia o caché de página, en kilobytes, que está en uso activo. Esta es memoria que recientemente ha sido utilizada y que usualmente no se reclama para otros propósitos.
- **Inactive** — La cantidad total de memoria intermedia o caché de página, en kilobytes, que está libre y disponible. Esta es memoria que no se ha utilizado recientemente y que se puede reclamar para otros propósitos.
- **HighTotal y HighFree** — Cantidad total de memoria libre, que no está mapeada en el espacio del kernel. El valor HighTotal puede variar dependiendo del tipo de kernel utilizado.
- **LowTotal y LowFree** — Cantidad total de memoria libre implantada directamente en el espacio del kernel. El valor LowTotal puede cambiar dependiendo del tipo de kernel utilizado.
- **SwapTotal** — Cantidad total de swap disponible, en kilobytes.
- **SwapFree** — Cantidad total de swap libre, en kilobytes.
- **Dirty** — La cantidad total de memoria, en kilobytes, esperando a ser escrita al disco.
- **Writeback** — Cantidad total de memoria, en kilobytes, que está siendo escrita activamente al disco.
- **Mapped** — La cantidad total de memoria, en kilobytes, que se ha utilizado para asignar dispositivos, archivos o bibliotecas, usando el comando mmap.
- **Slab** — Cantidad total de memoria, en kilobytes, usada por el kernel para hacer caché de estructuras de datos para su propio uso.
- **Committed_AS** — Cantidad total de memoria, en kilobytes, estimadas para completar la carga de trabajo. Este valor representa un escenario del peor caso, y también incluye a la memoria de intercambio o swap.
- **PageTables** — Cantidad total de memoria, en kilobytes, dedicada al nivel más bajo de la tabla de páginas.
- **VMallocTotal** — Cantidad total memoria, en kilobytes, del espacio total de direcciones virtuales asignadas.
- **VMallocUsed** — La cantidad total de memoria en kilobytes, de espacio de direcciones virtuales utilizada.
- **VMallocChunk** — El bloque continuo de memoria más grande, en kilobytes, de espacio de direcciones virtuales disponibles.
- **HugePages_Total** — El número total de paginas gigantes para el sistema. El número se deriva dividiendo Hugepagesize por los megabytes puestos a un lado para las páginas gigantes especificadas en **/proc/sys/vm/hugetlb_pool**. Esta estadística sólo aparece en las arquitecturas x86, Itanium y AMD64.
- **HugePages_Free** — El número total de páginas gigantes disponibles para el sistema. Esta estadística sólo aparece en las arquitecturas x86, Itanium y AMD64.
- **Hugepagesize** — El tamaño para cada unidad de hugepages en kilobytes. Por defecto, el valor es 4096 KB en los kernels de un sólo procesador para las arquitecturas de 32 bits. Para los kernels SMP, hugemem y AMD64, el valor por defecto es 2048 KB. Para las arquitecturas Itanium, el valor por defecto es 262144 KB. Esta estadística solamente aparece en las arquitecturas x86, Itanium y AMD64.

## /proc/modules

Este archivo muestra una lista de todos los módulos cargados en el sistema. Su contenido variará dependiendo de la configuración y uso de su sistema.

## /proc/mounts

Este archivo proporciona una lista de todos los montajes en uso por el sistema

## /proc/mtrr

Este archivo se refiere a la actual Memory Type Range Registers (MTRRs), en uso dentro del sistema. Los MTRRs se usan con la familia de procesadores Intel P6 (Pentium II y superior), y controlan el acceso del procesador a los rangos de memoria. Cuando utilice una tarjeta de vídeo en un PCI o un bus AGP, un archivo /proc/mtrr adecuadamente configurado puede incrementar el rendimiento en un 150%.

## /proc/partitions

El archivo contiene información sobre la asignación de bloques de particiones. El mismo se organiza en columnas que son las siguientes :

- **major** — Número principal (major number) del dispositivo con esta partición.
- **minor** — Número menor del dispositivo con esta partición. Separa las particiones en diferentes dispositivos físicos y los relaciona con el número al final del nombre de la partición.
- **\#blocks** — Lista el número de bloques de disco físicos contenidos en una partición particular.
- **name** — Nombre de la partición.

## /proc/pci

El archivo contiene una lista completa de cada dispositivo PCI en su sistema. Podemos acceder a los datos de una forma mas simple con el comando **/sbin/lspci -vb**

## /proc/slabinfo

Este archivo le da información completa sobre el uso de memoria en el nivel slab. Los kernels Linux superiores a la versión 2.2 usan slab pools para manejar memoria por encima del nivel de página. Los objetos utilizados habitualmente, tienen sus propios slab pools. En vez de analizar manualmente el largo archivo **/proc/slabinfo**, el programa **/usr/bin/slabtop** muestra la información del caché slab del kernel en tiempo real. Este programa permite configuraciones personalizadas, incluyendo el ordenamiento por columnas y la actualización de pantallas.

Algunas de las estadísticas usadas más comúnmente en **/proc/slabinfo** que se incluyen en **/usr/bin/slabtop**, abarcan:

- **OBJS** — El número total de objetos (bloques de memoria), incluyendo aquellos en uso (asignados), y algunos adicionales que no estén en uso.
- **ACTIVE** — El número total de objetos (bloques de memoria) utilizados (asignados).
- **USE** — Porcentaje de los objetos totales que están activos. `((ACTIVE/OBJS)(100))`
- **OBJ SIZE** — El tamalo de los objectos.
- **SLABS** — El número total de slabs.
- **OBJ/SLAB** — El número de objectos que caben en un slab.
- **CACHE SIZE** — El tamaño de caché del slab.
- **NAME** — Nombre del slab.

## /proc/stat

Este archivo mantiene un registro de las diferentes estadísticas sobre el sistema desde que fue reiniciado por última vez.

Algunas de las estadísticas más utilizadas son :

- **page** — Número de páginas que el sistema ha cargado o suprimido del disco.
- **swap** — Número de páginas swap que el sistema ha introducido o sacado.
- **intr** — Número de interrupciones que ha experimentado el sistema.
- **btime** — Tiempo de arranque, medido por el número de segundos desde el 1 de enero de 1970, conocido con el nombre de epoch.

## /proc/swaps

Este archivo mide el espacio swap y su uso, además proporciona una instantánea de cada nombre de archivo swap, el tipo de espacio swap, el tamaño total y la cantidad de espacio en uso (en kilobytes). La columna de prioridad es útil cuando se usan múltiples archivos de espacio de intercambio. Cuanto más baja es la prioridad, más probable es que se use el archivo de intercambio.

## /proc/uptime

El archivo contiene información sobre el tiempo que lleva encendido el sistema desde el último reinicio Esta dividido en dos columnas, el primer número le indica el número total de segundos que el sistema ha estado en funcionamiento. El segundo indica cuánto de ese tiempo, también en segundos, la máquina ha estado inactiva.

## /proc/version

Este archivo muestra las versión del kernel de Linux y gcc en uso.

# Directorios en /proc

## /proc/25845 etc etc

Podemos ver dentro de /proc muchos directorios con un número de nombre, ese numero corresponde a un PID de algún proceso ejecutándose actualmente en el sistema, cuando ese proceso deja de ejecutarse ese directorio deja de existir, dentro de ese directorio podemos ver mucha información sobre dicho proceso, como por ejemplo las variables de entorno con que se ejecuta, grupos de memoria etc.

Cada uno de los directorios de procesos contiene los siguientes archivos:

- **cmdline** — Contiene el comando que se ejecutó cuando se arrancó el proceso.
- **cwd** — Enlace simbólico al directorio actual en funcionamiento para el proceso.
- **environ** — Le da una lista de variables de entorno para el proceso. La variable de entorno viene dada toda en mayúsculas y el valor en minúsculas.
- **exe** — Enlace simbólico al ejecutable de este proceso.
- **fd** — Directorio que contiene todos los descriptores de archivos para un proceso en particular. Vienen dados en enlaces numerados.
- **maps** — Contiene mapas de memoria para los diversos ejecutables y archivos de bibliotecas asociados con este proceso.
- **mem** — Memoria del proceso.
- **root** — Enlace al directorio root del proceso.
- **statv** — Estado del proceso.
- **statm** — Estado de la memoria en uso por el proceso.

Las siete columnas del archivo statm se relacionan a diferentes estadísticas de memoria para el proceso. Dependiendo de como se visualizan, de derecha a izquierda, remiten diferentes aspectos de la memoria utilizada:

- Tamaño total del programa, en kilobytes.
- Tamaño de las porciones de memoria, en kilobytes.
- Número de páginas compartidas.
- Número de páginas que son código.
- Número de páginas de datos/pila.
- Número de páginas de bibliotecas.
- Número de páginas sucias.
* **status** — Proporciona el estado del proceso en una forma mucho más legible que stat o statm. Dentro de este archivo se incluye el nombre y ID del proceso, el estado (tal como S (sleeping) o R (running)), ID del usuario/grupo ejecutando el proceso y detalles sobre el uso de la memoria.

## /proc/sys/vm/

Este directorio proporciona acceso y modificación a los parametros de manejo de memoria virtual del Kernel Linux. El kernel hace un uso extensivo e inteligente de memoria virtual, que se conoce comúnmente como espacio de intercambio.

### block_dump

Configura la depuración de bloques de E/S cuando está activo. Se registran todas las operaciones de lectura/escritura o que impliquen ensuciar bloques hechas a archivos. Esto puede ser muy útil para efectos de diagnóstico del giros del disco para la conservación de la batería de las portátiles. Se pueden recuperar todas las salidas cuando block_dump está activado, mediante dmesg. El valor por defecto es 0.
{{% hint success %}}
Si **block_dump** está activado a la vez que la depuración del kernel, es prudente detener el demonio klogd, pues generará actividad del disco errónea causada por **block_dump**.
{{% /hint %}}
### dirty_background_ratio

Configura la escritura de los datos sucios a este porcentaje total de memoria, a través del demonio pdflush. El valor por defecto es 10.

### dirty_expire_centisecs

Define cuando los datos en memoria que se encuentran marcados como ‘dirty’ son lo suficientemente antiguos como para ser candidatos a escritura. Los datos en memoria por más tiempo que este intervalo, son escritos la próxima vez que se active un demonio pdflush. El valor por defecto es 3000, expresado en cientos de segundos.

### dirty_ratio

Comienza la escritura activa de los datos sucios a este porcentaje total de memoria para el generador de datos sucios, a través de pdflush. El valor por defecto es 40.

### dirty_writeback_centisecs

Define el intervalo entre activaciones del demonio pdflush, que escribe periódicamente los datos en memoria al disco. El valor por defecto es 500, expresado en cientos de segundos.

### laptop_mode

Minimiza el número de veces que un disco duro necesita girar manteniendo los giros por el mayor tiempo posible, y por ende, conservando energía en las baterías de las portatiles. Esto inclementa la eficiencia al combinar todos los procesos futuros de E/S juntos, reduciendo la frecuencia de los giros. El valor por defecto es 0, pero se activa automáticamente en caso de que se use la bateria en la portátil.

- Este valor es controlado automáticamente por el demonio acpid una vez que se le notifica al usuario que la energía de la batería está activada. No se necesitan modificaciones o interacciones por parte del usuario si la portátil soporta la especificación de ACPI (Advanced Configuration and Power Interface).

Para más información, consulte la siguiente documentación : Documentation/laptop-mode.txt

### lower_zone_protection

Determina qué tan agresivo es el kernel en defender las zonas de ubicación de memoria baja. Esto es efectivo cuando se utiliza con máquinas configuradas con el espacio de memoria highmem activado. El valor por defecto es 0, sin protección. Todos los demás valores enteros están en megabytes, y por lo tanto, la memoria lowmem está protegida de ser asignada por los usuarios.

Para más información, consulte la siguiente documentación : Documentation/filesystems/proc.txt

### max_map_count

Configura el número máximo de áreas de mapa de memoria que puede tener un proceso. En la mayoría de los casos, el valor por defecto de 65536 es apropiado.

### min_free_kbytes

Obliga a que Linux VM (el gestor de memoria virtual) mantenga un número mínimo de kilobytes libres. El VM utiliza este número para computarizar un valor pages_min para cada zona lowmem en el sistema. El valor por defecto es en relación al número total de memoria en la máquina.

### nr_hugepages

Lista el número actual de páginas hugetlb configuradas en el kernel.

Para más información, consulte la siguiente documentación : Documentation/vm/hugetlbpage.txt

### nr_pdflush_threads

Indica el número de demonios pdflush que se están ejecutando actualmente. Este archivo es de sólo lectura, y no debería ser cambiado por el usuario. Bajo grandes cargas de E/S, el kernel incrementa el valor por defecto de dos.

### overcommit_memory

Configura las condiciones bajo las cuales una petición de gran memoria es aceptada o rechazada. Están disponibles los siguientes tres modos:

- **0** — El kernel lleva a cabo un manejo de memoria heurístico, estimando la cantidad de memoria disponible y suspendiendo las peticiones que son obviamente inválidas. Desafortunadamente, puesto que la memoria es asignada usando heurísticas en vez de un algoritmo preciso, esta configuración puede algunas veces ocasionar la sobrecarga de la memoria disponible en el sistema. Esta es la configuración por defecto.
- **1** — El kernel no lleva a cabo ningún manejo de asignaciones extra de memoria. Con esta configuración, el potencial de sobrecarga de memoria se incrementa, pero también el rendimiento para las tareas intensivas de memoria (tales como aquellas ejecutadas por software científico).
- **2** — El kernel suspende las peticiones de memoria que consumen todo el swap más el porcentaje de memoria física RAM especificado en /proc/sys/vm/overcommit_ratio. Esta configuración es mejor para aquellos que deseen menos riesgos de comprometer en exceso la memoria.
{{% hint success %}}
Esta configuración solamente es recomendada para los sistemas con áreas swap más grandes que la memoria física.
{{% /hint %}}
### overcommit_ratio

Especifica el porcentaje de memoria física RAM considerada cuando /proc/sys/vm/overcommit_memory es configurado a 2. El valor por defecto es 50. page-cluster — Establece el número de páginas leídas en un solo intento. El valor por defecto de 3 establecido en 16 páginas, es apropiado para la mayoría de los sistemas.

### swappiness

Establece cuánto swap debería hacer una máquina. Mientras más alto es el número, ocurrirá mayor swapping. O sea el swappines, que es nivel de prioridad swap/ram, permite establecer un balance entre el uso de la memoria swap y la memoria ram. El Swappiness puede tomar valores desde el 0 hasta el 100. El valor por dfault es 60, si se estable un valor mas bajo se prioriza la ram sobre la swap y se puede evitar la paginacion mientras que si se establece a 100 el intercambio se realizará constantemente.

Para cambiar el swappiness ejecutamos

```
echo 0 > /proc/sys/vm/swappiness
```

O sino en /etc/sysctl.conf :

```
vm.swappiness = 10
```

## Referencias

- Documentación del Filesystem Proc Oficial del Kernel Linux
  - <https://www.kernel.org/doc/Documentation/filesystems/proc.txt>
