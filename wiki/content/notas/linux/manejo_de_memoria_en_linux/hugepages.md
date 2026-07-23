---
title: "Hugepages"
date: 2016-07-07T16:02:22-03:00
---

Los procesos no trabajan directamente con la memoria física, sino que lo hacen con la memoria virtual. Aplicaciones que realizan una gran cantidad de accesos a memoria pueden obtener mejoras de rendimiento usando páginas grandes gracias a la traducción reducida Lookaside Buffer (TLB). Hugetlbfs es una función de administración de memoria que se ofrece en el kernel de Linux, que es útil para aplicaciones que utilizan un gran espacio de direcciones virtuales.

Para ello se debe realizar una conversión, entre la memoria física y la virtual. Para dicha conversión cada proceso dispone de una tabla de conversión, y como lo que contiene son direcciones de pagina, se llama tabla de paginación. En aplicaciones con mucho consumo de memoria, donde además la información cambia constantemente (por ejemplo, una base de datos), el acceso a la tabla de paginación es constante.

Debido a esto existe la “Translation Lookaside Buffer”, una pequeña cache con parte de la tabla de paginación. El problema es que la TLB es de tamaño fijo, y como normalmente guardamos direcciones de páginas de 4 KBytes; el total de memoria al que nos da acceso la TLB no es muy grande. Con lo cual como consecuente tenemos esta situación :

- Utilización intermitente y muy alta de la CPU

## ¿Cuál es la solución?

Utilizar tamaños mayores de página o Hugepages (el caso más habitual 2 MBytes). Esto supone acceder a una mayor cantidad de memoria con las mismas entradas (PTE) en la tabla de paginación. Reduciendo así el consumo de CPU.

## ¿Cómo se si mi sistema operativo Linux tiene habilitada dicha funcionalidad en el kernel?

Ejecutar el siguiente comando:

```
$ grep -i huge /proc/meminfo 
```

La salida debe ser similar a:

```
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
```

El núcleo compilado con hugepage debe mostrar el número de páginas gigantes configuradas en el sistema. De lo contrario, es necesario recompilar el kernel de Linux con la opción de CONFIG_HUGETLBFS habilitada.

Para mas información leer [Manejo de memoria en Linux](/notas/linux/Manejo de memoria en Linux).

## Los pasos a seguir son

1.  Definir el límite memlock para el usuario del servicio que estamos configurando, con un valor algo menor de la memoria disponible (/etc/sysctl.conf).
2.  Iniciar el servicio que consume la memoria.
3.  Calcular el número de hugepages que queremos utilizar en función de la memoria consumida por nuestro proceso (en el caso de Oracle hay un script en la Nota 401749.1)
4.  Definir el parámetro del kernel vm.nr_hugepages= en el archivo /etc/security/limits.conf
5.  Reiniciar el servidor.
6.  Revisar el valor: \$ grep Huge /proc/meminfo

Detalles sobre su uso, especialmente con Oracle:

- Si las páginas totales son iguales a las libres, nuestro servicio no aprovecha las Hugepages por lo que estamos desperdiciando memoria, mejor desconfigurarlas.
- No son compatibles con la AMM (Automatic Memory Management) en 11g
- Hay un bug con Grid Infraestructure 11g, donde no se utiliza el límite memlock tal y como hemos definido: Bug 9251136 “INSTANCE WILL NOT USE HUGEPAGE IF STARTED BY SRVCTL”
- El aspecto principal de Oracle que se ve afectado por el uso de HugePages es la **SGA**

La característica de hugetlbfs permite a una aplicación a poder utilizar un tamaño de página mucho más grande de lo normal, de modo que una entrada de TLB solo puede asignar un mayor espacio de direcciones. Las entradas en HugeTLB pueden variar en tamaño. Por ejemplo, la arquitectura i386 soporta 4K y 4M (2M en modo PAE), tamaños de página, ia64 arquitectura soporta tamaños de varias páginas 4K, 8K,64K, 256K, 1M, 4M, 16M, 256M y ppc64 apoya 4K y 16M.

Para asignar hugepage, puede definir el número de páginas gigantes mediante la configuración de valor en /proc/sys/vm/nr_hugepages, escriba:

sysctl -w vm.nr_hugepages=40 (Puede hacerlo de manera mas manual, abriendo el archivo /etc/sysctl y agregando vm.nr_hugepages=40)

Hecho lo anterior actualizamos:

bash\$ sysctl -p

Ahora a verificar si se configuraron las paginas:

```
bash# grep -i huge /proc/meminfo 

HugePages_Total:    40
HugePages_Free:     40
HugePages_Rsvd:      0
Hugepagesize:     2048 kB
```

Donde:

- HugePages_Total: 40 - El espacio total otorgado al pool de hugepages.
- HugePages_Free: 40 - El espacio total de paginas libres existentes de la pool total.
- HugePages_Rsvd: 0 - El espacio total de las paginas siendo utilizadas por alguna aplicacion.
- Hugepagesize: 2048 kB -

### Implementación

#### Memoria compartida

- SHMMAX: Tamaño máximo de un segmento de memoria compartida (bytes)
- SHMMIN: Tamaño mínimo de un segmento de memoria compartida (bytes)
- SHMALL: Cantidad máxima de memoria compartida disponible (bytes ó páfinas)
- SHMSEG: Número máximo de segmentos de memoria compartida por proceso
- SHMMNI: Número máximo de segmentos de memoria compartida en todo el sistema

#### Semáforos

*Un semáforo se puede pensar como un contador que se utiliza para acceder a los recursos compartidos del sistema.*

- SEMMNI: Número máximo de identificadores de semáforos (grupos)
- SEMMNS: Número máximo de semáforos en todo el sistema
- SEMMSL: Número máximo de semáforos por grupo
- SEMMAP: Número de entradas en el mapa de semáforos
- SEMVMX: Máximo valor de un semáforo
- SEMOPM: Número máximo de operaciones con semáforos que pueden realizarse por cada llamada del sistema semop
- SEM: Es igual a “SEMMSL SEMMNS SEMOPM SEMMNI”

Desde la linea de comandos se puede ver la memoria compartida y los semáforos usados

```
srvlxsid:~ # ipcs
------ Shared Memory Segments --------
key        shmid      owner      perms      bytes      nattch     status
0x52205704 32768      orasid     640        20487077888 375
0x00000000 65537      sidadm     777        1024       1
0x00004dbe 13074463   sidadm     777        574868     1
0x00002748 13140000   sidadm     740        534592     15
------ Semaphore Arrays --------
key        semid      owner      perms      nsems
0xc1f65328 98304      orasid     640        1004
0x00004e62 3932277    sidadm     740        1
0x00004e28 3965046    sidadm     740        1
0x00004e68 3997815    sidadm     740        1
------ Message Queues --------
key        msqid      owner      perms      used-bytes   messages
```

```
ipcs -l
---- Límites memoria compartida ----
número máx. segmentos = 4096
tamaño máx. segmento (kbytes) = 9007199254740991
max total shared memory (kbytes) = 4611686018427386880
tamaño mín. segmento (bytes) = 1
------ Límites semáforo --------
número máximo de matrices = 8192
máx. semáforos por matriz = 1250
máx. semáforos sistema = 256000
máx. oper. por llamada semop = 100
valor máx. semáforo = 32767
------ Mensajes: límites -------
máx. colas sistema = 32768
tamaño máx. mensaje (bytes) = 65536
tamaño máx. predeterminado cola (bytes) = 65536
```

- Empezando por la primera sección sobre Límites de la memoria compartida, SHMMAX y SHMALL son los parámetros a los que debe mirarse. SHMMAX es el tamaño máximo de un segmento de memoria compartida en un sistema Linux mientras que SHMALL es la asignación máxima de páginas de memoria compartida en un sistema.
  - Es recomendable establecer el valor SHMMAX de forma que sea igual a la cantidad de memoria física del sistema. Sin embargo, el mínimo necesario en los sistemas x86 es 268435456 (256 MB) y para sistemas de 64 bits es 1073741824 (1 GB).
  - SHMALL está establecido en 8 GB por omisión (8388608 KB = 8 GB). Si la memoria física supera esta cantidad y se debe utilizar para DB2, este parámetro se incrementa al 90% aproximadamente de la memoria física del sistema. Por ejemplo, si el sistema tiene 16 GB de memoria que debe utilizarse principalmente para DB2, SHMALL deberá establecerse en 3774873 (el 90% de 16 GB es 14,4 GB; 14,4 GB se divide entre 4 KB, que es el tamaño de página de base). La salida de ipcs ha convertido SHMALL a kilobytes. El kernel requiere este valor como un número de páginas.Si está actualizando a DB2 Versión 9.7 sin utilizar el valor por omisión de SHMALL, debe aumentar el valor de SHMALL en 4 GB adicionales. Fast Communication Manager (FCM) necesita este incremento de la memoria para los canales y los almacenamientos intermedios adicionales.
- La sección siguiente trata de la cantidad de semáforos disponibles para el sistema operativo. El parámetro del kernel sem está formado por 4 símbolos: SEMMSL, SEMMNS, SEMOPM y SEMMNI. SEMMNS es el resultado de multiplicar SEMMSL por SEMMNI. El gestor de bases de datos necesita que el número de conjuntos (SEMMNI) aumente según sea necesario. Normalmente, SEMMNI debe ser el doble que el número máximo de agentes esperados en el sistema multiplicado por el número de particiones lógicas del sistema servidor de bases de datos más el número de conexiones de aplicación locales del sistema servidor de bases de datos.
- La tercer sección trata de los mensajes del sistema.
  - MSGMNI afecta el número de agentes que pueden iniciarse, MSGMAX afecta el tamaño del mensaje que puede enviarse en una cola y MSGMNB afecta el tamaño de la cola.
  - En MSGMAX debe cambiarse a 64 KB (es decir, 65535 bytes) y MSGMNB debe aumentarse hasta 65535.

```
kernel.shmmax = 9223372036854775807
kernel.sem = 1250 256000 100 8192
kernel.shmall = 1152921504606846720
vm.max_map_count = 1000000
vm.nr_hugepages=12000
```

#### /etc/security/limits.conf

memlock : memoria compartida en kilobytes

```
*              soft     nofile    4096
*              hard     nofile    63536
*              soft     memlock   26214400
*              hard     memlock   26214400
@ejemplo - memlock unlimited
```

### Tips

**/etc/security/limits.conf** Debe tener un valor asignado un poco menor a la memoria instalada, por ejemplo si tenemos 64GB of RAM, un valor apropiado puede ser :

```
soft memlock 60397977
hard memlock 60397977
```

Script que calcula automaticamente el valor de hugepages, usar solo como orientación

Oracle recomiendar calcular de esta forma :

**ipcs -m**

<table>
<thead>
<tr>
<th><strong>key</strong></th>
<th><strong>shmid</strong></th>
<th><strong>owner</strong></th>
<th><strong>perms</strong></th>
<th><strong>bytes</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td>0x00004dc4<br />
0x00004dbe<br />
0x52205704<br />
0x00000000<br />
0x0382be84<br />
0x00002749<br />
0x0000271a<br />
0x00002711<br />
0x00002712<br />
0x00002713<br />
0x00002744<br />
0x0000272f<br />
0x00002743<br />
0x0000274e<br />
0x00002759<br />
0x0000274f<br />
0x0000271e<br />
0x00002719<br />
0x00002718<br />
0x00002722<br />
0x00002738<br />
0x00002717<br />
0x00002714<br />
0x00002739<br />
0x00002723<br />
0x00002731<br />
0x00002746<br />
0x00002751<br />
0x00002750<br />
0x00002761<br />
0x00002716<br />
0x0000272e<br />
0x00002732<br />
0x0000274a<br />
0x00002748</td>
<td>229376<br />
262145<br />
88670210<br />
88702979<br />
360452<br />
88735749<br />
92143622<br />
92176391<br />
92209160<br />
92241929<br />
92274698<br />
92307467<br />
92340236<br />
92405773<br />
95813646<br />
99221519<br />
99254288<br />
99287057<br />
99319826<br />
99352595<br />
99385364<br />
99418133<br />
99483670<br />
99516439<br />
99549208<br />
99581977<br />
99614746<br />
99647515<br />
99680284<br />
99713053<br />
101646366<br />
101679135<br />
101711904<br />
101744673<br />
101777442</td>
<td>sapadm<br />
root<br />
orasid<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm<br />
sidadm</td>
<td>760<br />
777<br />
640<br />
740<br />
640<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740<br />
740</td>
<td>40141728<br />
793440<br />
47246737408<br />
1024<br />
4096<br />
1639280<br />
200000000<br />
588<br />
62456056<br />
200592000<br />
179368<br />
6387876<br />
23738964<br />
86484792<br />
60211232<br />
86040<br />
40000000<br />
2147483648<br />
2147483648<br />
6423584<br />
662000000<br />
31072<br />
645640<br />
45454312<br />
1799999904<br />
92159912<br />
287465496<br />
33554456<br />
4194456<br />
19480<br />
4096000000<br />
126790<br />
83394560<br />
2076<br />
535000</td>
</tr>
</tbody>
</table>

De la cantidad de bytes formamos una matriz que es la de segmentos de memoria compartida en bytes y realizamos el siguiente cálculo :
{{% hint info %}}
**\| MATRIZ DE SEGMENTOS DE MEMORIA COMPARTIDA \| / (TAMAÑO DE PAGINA\*1024)+1**
{{% /hint %}}
<http://docs.oracle.com/cd/E37670_01/E37355/html/ol_config_hugepages.html>

[Metalink Note 401749.1, Shell Script to Calculate Values Recommended HugePages / HugeTLB Configuration](https://metalink.oracle.com/metalink/plsql/f?p=130:14:2414882707157355558::::p14_database_id,p14_docid,p14_show_header,p14_show_help,p14_black_frame,p14_font:NOT,401749.1,1,0,1,helvetica)

```bash>
Con Oracle en el Kernel 3.0 es recomendable deshabilitar las páginas transparentes.

Podemos hacerlos on-the-fly
<code>
echo never > /sys/kernel/mm/transparent_hugepage/enabled
```

O al inicio del sistema desde la cmdline de booteo :

```
transparent_hugepage=never
```

### Referencias

- SAP Note
  - 1672954 - Oracle 11g: Usage of hugepages on Linux
  - 1681501 - Configure a SAP JVM to use large pages on Linux
  - 1871318 - Linux: Disable Transparent HugePages for Oracle Database
  - 1672954 - Oracle 11g: Usage of hugepages on Linux
  - 1384753 - Oracle 10g: Using of hugepages on Linux
- Oracle Metalink note 361323.1
- [How to Configure x86 Memory Performance for Large Databases](http://www.oracle.com/technetwork/articles/servers-storage-dev/hugepages-2099009.html)
- [\[Oracle\] Myths and common misconceptions about (transparent) huge pages for Oracle databases (on Linux) uncovered](http://scn.sap.com/community/oracle/blog/2013/06/17/oracle-myths-and-common-misconceptions-about-transparent-huge-pages-for-oracle-databases-on-linux-uncovered)
