---
title: "Manual de manejo de respaldos con Tivoli"
date: 2015-05-04T16:53:21-03:00
---

<img src="/manuales/tivoli_tsm/manual_de_manejo_de_backups_con_tivoli/tsm.png" class="align-right" /> Tivoli Storage Manager (TSM) o más recientemente llamado IBM Tivoli Storage Manager (ITSM) es un software centralizado y basado en políticas que permite la administración de los recursos de almacenamiento.

Este programa es parte de la serie IBM TotalStorage. Previamente se conocía como ADSTAR Distributed Storage Manager (ADSM).

# Implementación tipica de TSM

![esquema_tsm.jpg](/manuales/tivoli_tsm/manual_de_manejo_de_backups_con_tivoli/esquema_tsm.jpg)

# Instalación del Servidor Tivoli TSM

## Desde paquetes

Para instalar sobre Debian, deberemos convertir los paquetes **RPM** a **DEB** por medio de la herramienta **alien**.

```
tivsm-license_5.4.0-1_i386.deb
tivsm-server_5.4.0-1_i386.deb
tivsm-tsmscsi_5.4.0-1_i386.deb
```

y los archivo del cliente de Tivoli TSM para poder administrarlo desde la consola

```
tivsm-api_5.4.2-1_i386.deb
tivsm-ba_5.4.2-1_i386.deb
tivsm-msg.es-es_5.4.2-1_i386.deb
```

## Creación de los archivos de la base de datos

Generación de los archivos :

Un tamaño recomendable para la base de datos es de 5GB y para el log de 2GB

```
dd if=/dev/zero of=/opt/tivoli/tsm_data/dbvol.dsm bs=1024 count=5000000
dd if=/dev/zero of=/opt/tivoli/tsm_data/logvol.dsm bs=1024 count=2000000
```

Debemos formatearlos a TSM

```
./dsmserv format 1 /opt/tivoli/tsm_data/logvol.dsm 1 /opt/tivoli/tsm_data/dbvol.dsm

Tivoli Storage Manager for Linux/i386
Version 5, Release 4, Level 0.0

Licensed Materials - Property of IBM

(C) Copyright IBM Corporation 1990, 2007.
All rights reserved.
U.S. Government Users Restricted Rights - Use, duplication or disclosure
restricted by GSA ADP Schedule Contract with IBM Corporation.

ANR7800I DSMSERV generated at 16:19:56 on Dec  7 2006.
ANR7801I Subsystem process ID is 1916.
ANR0900I Processing options file dsmserv.opt.
ANR4726I The ICC support module has been loaded.
ANR0300I Recovery log format started; assigned capacity 1952 megabytes.
ANR0301I Recovery log format in progress; 4 megabytes of 1952.
ANR0301I Recovery log format in progress; 8 megabytes of 1952.
ANR0301I Recovery log format in progress; 12 megabytes of 1952.
ANR0301I Recovery log format in progress; 16 megabytes of 1952.
ANR0301I Recovery log format in progress; 20 megabytes of 1952.
ANR0301I Recovery log format in progress; 24 megabytes of 1952.
ANR0301I Recovery log format in progress; 28 megabytes of 1952.
......
```

Si al ejecutar el comando **dsmserv** obtenemos un mensaje como este :

```
ANR0000E Unable to open language en_US for message formatting.
ANR0000E Unable to open message text file for message formatting.
```

Debemos copiar el archivo **dsmen_US.txt** a **dsmen_us.txt**.

Debemos configurarlos en **/opt/tivoli/tsm/server/bin/dsmserv.dsk**

```
/opt/tivoli/tsm_data/logvol.dsm
/opt/tivoli/tsm_data/dbvol.dsm
```

### Backup de la base

Podemos ir tomando backups diarios de la base de datos, para ello necesitamos generar una clase de dispositivo.

Ejemplo si es sobre un servidor TSM sobre Windows o Linux :

```
DEFINE DEVCLASS FILE DEVTYPE=FILE FORMAT=DRIVE DIRECTORY=/opt/TSMDATA/file
DEFINE DEVCLASS FILE DEVTYPE=FILE FORMAT=DRIVE DIRECTORY=E:\TSMDATA\file
```

Ejemplo para una base pequeña : **backup db t=f dev=file wait=yes**

```
ANR0984I Process 4 for DATABASE BACKUP started in the FOREGROUND at 11:59:24.
ANR2280I Full database backup started as process 4.
ANR4554I Backed up 67712 of 135502 database pages.
ANR4554I Backed up 135424 of 135502 database pages.
ANR4550I Full database backup (process 4) complete, 135502 pages copied.
ANR0985I Process 4 for DATABASE BACKUP running in the FOREGROUND completed with completion state SUCCESS at 11:59:
```

Tomamos registros de otros historicos que nos interesan :

```
backup volhist file=E:\TSMDATA\file\volhist.out
backup devconfig file=E:\TSMDATA\file\devconfig.out
```

## Configuración del servicio de backup

Debemos configurar el nombre del servidor :

```
Set SERVername SERVERTSM
```

Ver las opciones tomadas del archivo dsmserv.opt

```
tsm: SERVER1>q opt    

Server Option         Option Setting           Server Option         Option Setting      
-----------------     --------------------     -----------------     --------------------
CommTimeOut           60                       IdleTimeOut           15                  
BufPoolSize           32768                    LogPoolSize           512                 
DateFormat            1 (mm/dd/yyyy)           TimeFormat            1 (hh:mm:ss)        
NumberFormat          1 (1,000.00)             MessageFormat         1                   
Language              en_US                    Alias Halt            HALT                
MaxSessions           25                       ExpInterval           24                  
ExpQuiet              No                       EventServer           Yes                 
ReportRetrieve        No                       DISPLAYLFINFO         No                  
MirrorRead DB         Normal                   MirrorRead LOG        Normal              
MirrorWrite DB        Sequential               MirrorWrite LOG       Parallel            
TxnGroupMax           256                      MoveBatchSize         1000                
MoveSizeThresh        2048                     RestoreInterval       1,440               
DisableScheds         No                       NOBUFPREfetch         No                  
AuditStorage          Yes                      REQSYSauthoutfile     Yes                 
SELFTUNEBUFpools-     No                       DBPAGEShadow          No                  
 ize                                                                                     
DBPAGESHADOWFile      dbpgshdw.bdt             MsgStackTrace         On                  
QueryAuth             None                     LogWarnFullPerCe-     90                  
                                                nt                                       
ThroughPutDataTh-     0                        ThroughPutTimeTh-     0                   
 reshold                                        reshold                                  
NOPREEMPT             ( No )                   Resource Timeout      60                  
TEC UTF8 Events       No                       AdminOnClientPort     Yes                 
NORETRIEVEDATE        No                       IMPORTMERGEUsed       Yes                 
DNSLOOKUP             Yes                      NDMPControlPort       10,000              
NDMPPortRange         0,0                      SHREDding             Automatic           
TCPPort               1500                     TcpAdminport          1500                
HTTPPort              1580                     TCPWindowsize         64512               
TCPBufsize            16384                    TCPNoDelay            Yes                 
CommMethod            TCPIP                    CommMethod            HTTP                
MsgInterval           1                        ShmPort               1510                
FileExit                                       FileTextExit                              
UserExit                                       AcsAccessId                               
AcsTimeoutX           1                        AcsLockDrive          No                  
AcsQuickInit          No                       SNMPSubagentPort      1521                
SNMPSubagentHost      127.0.0.1                SNMPHeartBeatInt      5                   
TECHost                                        TECPort               0                   
UNIQUETECevents       No                       UNIQUETDPTECeven-     No                  
                                                ts                                       
SHAREDLIBIDLE         No                       3494Shared            No                  
CheckTrailerOnFr-     On                       SANdiscovery          Off                 
 ee                                                                                      
```

Debemos crear un usuario administrador para poder conectarnos al servicio por red, para esto debemo arrancar manualmente el servidor y registrar el usuario por ejemplo admin con el passwor ‘admin’ :

Registramos el usuario

- **register admin admin admin**

Le otorgamos permisos de administración al mismo

- **GRANT AUTHORITY admin classes=system**

```
root@localhost:/opt/tivoli/tsm/server/bin# ./dsmserv

Tivoli Storage Manager for Linux/i386
Version 5, Release 4, Level 0.0

Licensed Materials - Property of IBM

(C) Copyright IBM Corporation 1990, 2007.
All rights reserved.
U.S. Government Users Restricted Rights - Use, duplication or disclosure
restricted by GSA ADP Schedule Contract with IBM Corporation.

ANR7800I DSMSERV generated at 16:19:56 on Dec  7 2006.
ANR7801I Subsystem process ID is 2436.
ANR0900I Processing options file dsmserv.opt.
ANR4726I The ICC support module has been loaded.
ANR0990I Server restart-recovery in progress.
ANR0200I Recovery log assigned capacity is 1952 megabytes.
ANR0201I Database assigned capacity is 4880 megabytes.
ANR0306I Recovery log volume mount in progress.
ANR0353I Recovery log analysis pass in progress.
ANR0354I Recovery log redo pass in progress.
ANR0355I Recovery log undo pass in progress.
ANR0352I Transaction recovery complete.
ANR1635I The server machine GUID, fd.fa.36.ba.7c.3d.11.e2.87.5c.00.18.fe.75.50.26, has initialized.
ANR2100I Activity log process has started.
ANR4726I The NAS-NDMP support module has been loaded.
ANR1794W TSM SAN discovery is disabled by options.
ANR0984I Process 1 for EXPIRATION started in the BACKGROUND at 18:17:40.
ANR0811I Inventory client file expiration started as process 1.
ANR2803I License manager started.
ANR8200I TCP/IP driver ready for connection with clients on port 1500.
ANR8190I HTTP driver ready for connection with clients on port 1580.
ANR2560I Schedule manager started.
ANR4747W The web administrative interface is no longer supported. Begin using the Integrated Solutions Console instead.
ANR0993I Server initialization complete.
ANR0916I TIVOLI STORAGE MANAGER distributed by Tivoli is now ready for use.
ANR0985I Process 1 for EXPIRATION running in the BACKGROUND completed with completion state SUCCESS at 18:17:40.
ANR2841W Server is NOT IN COMPLIANCE with license terms.
TSM:SERVER1> 
register admin admin admin
ANR2017I Administrator SERVER_CONSOLE issued command: REGISTER ADMIN admin ?***? 
ANR2068I Administrator ADMIN registered.
TSM:SERVER1> 
GRANT AUTHORITY admin classes=system
ANR2017I Administrator SERVER_CONSOLE issued command: GRANT AUTHORITY admin classes=system 
ANR2076I System privilege granted to administrator ADMIN.
TSM:SERVER1> 
QUERY ADMIN
ANR2017I Administrator SERVER_CONSOLE issued command: QUERY ADMIN 

Administrator        Days Since       Days Since      Locked?       Privilege Classes      
Name                Last Access     Password Set                    
--------------     ------------     ------------     ----------     -----------------------
ADMIN                        <1               <1         No         System                 
ADMIN_CENTER                 <1              (?)        Yes                                
SERVER_CONSOLE                                           No         System                 
```

Configuración de Storage Pools

```
UPDATE STGPOOL ARCHIVEPOOL NEXTSTGPOOL=SPACEMGPOOL
UPDATE STGPOOL BACKUPPOOL NEXTSTGPOOL=SPACEMGPOOL

tsm: SERVER1>q stg

Storage         Device          Estimated       Pct       Pct     High     Low     Next Stora-
Pool Name       Class Name       Capacity      Util      Migr      Mig     Mig     ge Pool    
                                                                   Pct     Pct     
-----------     ----------     ----------     -----     -----     ----     ---     -----------
ARCHIVEPOOL     DISK                0.0 M       0.0       0.0       90      70     SPACEMGPOOL
BACKUPPOOL      DISK                0.0 M       0.0       0.0       90      70     SPACEMGPOOL
SPACEMGPOOL     DISK                0.0 M       0.0       0.0       90      70                
```

Debemos definir un volumen para el pool de disco, por ejemplo de 50GB

```
DEFINE VOLUME SPACEMGPOOL /opt/tivoli/tsm_data/disk.dsm f=50000
```

## Configurando dispositivo de Tape

<img src="/manuales/tivoli_tsm/manual_de_manejo_de_backups_con_tivoli/tsm_lto.gif" class="align-right" alt="tsm_lto.gif" />

Debemos instalar y compilar el paquete lin_tape “Linux – IBM LinTape driver”

Obtendremos dos paquetes RPM

- lin_tape-1.61.0-1.src.rpm \<= Módulo del Kernel
- lin_taped-1.61.0-rhel5.i386.rpm \<= Servicio

Debemos compilar el codigo fuente del módulo del Kernel y luego instalar el servicio.

Con lo cual obtendremos estos dispositivos especiales en **/dev**:

```
IBMtape
IBMtape0
IBMtape0n
```

**/proc/scsi/IBM**\*

```
lin_tape version: 1.76.0
lin_tape major number: 250
Attached Changer Devices:
Number  model       SN                HBA             SCSI            FO Path   
lin_tape version: 1.76.0
lin_tape major number: 250
Attached Tape Devices:
Number  model       SN                HBA             SCSI            FO Path   
0       ULT3580-TD3 1210342411        MPT SPI Host    2:0:6:0         NA      
```

Luego de esto debemos ejecutar **/opt/tivoli/tsm/devices/bin/autoconf**

Definiendo la libreria :

```
DEFINE LIBRARY TAPE_LIBR LIBTYPE=MANUAL
```

Definiendo el drive :

```
DEFINE DRIVE TAPE_LIBR TAPE_DRIVE ONLINE=YES
```

Definiendo la clase de dispositivo :

```
DEFINE DEVCLASS TAPE_DEVICE_CLASS DEVTYPE=LTO FORMAT=DRIVE LIBRARY=TAPE_LIBR MOUNTLIMIT=1 MOUNTRETENTION=60 PREFIX=ADSM MOUNTWAIT=60
```

Definiendo el path :

```
DEFINE PATH TSERVER1 TAPE_DRIVE SRCTYPE=SERVER DESTTYPE=DRIVE LIBRARY=TAPE_LIBR DEVICE=/dev/IBMtape0 ONLINE=YES
```

Definimos un pool de cintas :

```
DEFINE STGPOOL BACKUP TAPE_DEVICE_CLASS maxscratch=999 
```

Definimos como poolde migracion del pool de disco al pool de cintas :

```
UPDATE STGPOOL SPACEMGPOOL nextstgpool=BACKUP
```

```
ACTIVATE POLICYSET STANDARD STANDARD
```

## Configurando dispositivo de VTL - Data Domain

Para poder utilizar esta configuración deberemos contar con la version de Tivoli TSM 5.5.7 o superior y dar de alta la configuación en el Data Domain

Ejemplo de definición para una emulación VTL tipo IBM 3584 con cuatros drives LTO4

```
DEFINE LIBRARY lb0.0.0.6 libtype=scsi
DEFINE PATH  TSM1 lb0.0.0.6 srctype=server desttype=library device=lb0.0.0.6
DEFINE DRIVE lb0.0.0.6 mt0.1.0.6 element=257
DEFINE PATH  TSM1 mt0.1.0.6 srctype=server desttype=drive device=mt0.1.0.6 library=lb0.0.0.6
DEFINE DRIVE lb0.0.0.6 mt0.2.0.6 element=258
DEFINE PATH  TSM1 mt0.2.0.6 srctype=server desttype=drive device=mt0.2.0.6 library=lb0.0.0.6
DEFINE DRIVE lb0.0.0.6 mt0.3.0.6 element=259
DEFINE PATH  TSM1 mt0.3.0.6 srctype=server desttype=drive device=mt0.3.0.6 library=lb0.0.0.6
DEFINE DRIVE lb0.0.0.6 mt0.4.0.6 element=260
DEFINE PATH  TSM1 mt0.4.0.6 srctype=server desttype=drive device=mt0.4.0.6 library=lb0.0.0.6
DEFINE DEVCLASS LTOCLASS2 devtype=LTO library=lb0.0.0.6 format=ULTRIUM4
DEFINE STGPOOL LTOPOOL2 LTOCLASS2 maxscratch=500 dataformat=NATIVE
```

Luego podemos etiquetar las cintas presentes en la VTL :

```
LABEL LIBVolume LB0.0.0.3 SEARCH=yes LABELSource=b checkin=priv overwrite=no
```

## Configuraciones extras

./dsmserv runfile /opt/tivoli/tsm/server/bin/scripts.smp

# Registro de un nodo cliente

```
register node nodo contraseña
```

## Tipos de Backups

Esquema de como se organizan los datos de Backup del nodo

![tsm_esquema_nodos.gif](/manuales/tivoli_tsm/manual_de_manejo_de_backups_con_tivoli/tsm_esquema_nodos.gif)

### Backups Programados

#### Compresión del lado del cliente

EL uso de la compresión en el cliente de Tivoli Storage Manager puede ahorrar espacio de almacenamiento para las copias de seguridad. La compresión ocurre en el cliente antes de ser enviado el respaldo al servidor de TSM con lo cual también ahorra ancho de banda en la red. El algoritmo de compresión hace uso adicional de CPU y puede causar que la copia de seguridad tarde un poco más.

![tsm_compress.gif](/manuales/tivoli_tsm/manual_de_manejo_de_backups_con_tivoli/tsm_compress.gif)

#### Clientes de Tivoli Storage Manager en sistemas UNIX

##### Problemas comúnes con el cliente Tivoli TSM

El cliente Java arroja este error :

```
ANS2600S Browser trying to establish connection to client: received
socket exception: java.net.ConnectException: Connection refused
```

Debemos ejecutar **/opt/tivoli/tsm/client/ba/bin/dsmagent** y ver si nos arroja errores de libreria. Si es así probablemente deberemos agregar a nuestro **LD_LIBRARY_PATH** la siguiente ruta **/opt/tivoli/tsm/client/api/bin64/**.

#### Clientes de Tivoli Storage Manager en sistemas Windows

<http://publib.boulder.ibm.com/tividd/td/ITSMERP/SC33-6340-04/es_ES/HTML/fbrg33m069.htm>

<http://www.ajpdsoft.com/modules.php?name=News&file=print&sid=257>

### Backups TDP

#### BACKINT

<img src="/manuales/tivoli_tsm/manual_de_manejo_de_backups_con_tivoli/backint.gif" class="align-right" />

Las herramientas de administración de bases de datos de SAP ofrecen todas las funciones necesarias para administrar una base de datos. Las herramientas SAP DBA BRTOOLS para Oracle (BRBACKUP, BRARCHIVE, BRRESTORE) ofrecen funciones de copia de seguridad en línea o fuera de línea, parcial o completa de espacios de tabla (mediante BRBACKUP), copia de seguridad de archivos de registro redolog archivados (mediante BRARCHIVE) y funciones de restauración/recuperación guiadas por el sistema (mediante BRRESTORE y BRRECOVER).

Las herramientas de mySAP.com (programas de utilidad de SAPDBA) utilizan tablas de la base de datos Oracle y datos del sistema para registrar el estado de las copias de seguridad de los archivos de datos y de las copias de seguridad de los archivos de registro. Esta información permitirá a mySAP.com restaurar automáticamente los archivos de datos correctos y sus archivos específicos de registro de transacciones de bases de datos (archivos de registro redolog), si es necesario. Los archivos de datos residen en la base de datos de Oracle (Instancia de Oracle). Data Protection for mySAP.com se ejecuta como un proceso independiente de la base de datos. Recibe los datos a través de la interfaz de Backint y los guarda en Tivoli Storage Manager.

Una ejecución de copia de seguridad se realizará del siguiente modo (consulte los números incluidos en un círculo):

1.  El programa de utilidad de SAPDBA BRBACKUP informa a Oracle sobre los datos de los que hay que hacer copia de seguridad y coloca la base de datos en el estado de copia de seguridad adecuado (‘en línea’ o ‘fuera de línea’).
2.  BRBACKUP llama a DP for mySAP.com a través de la interfaz de BACKINT con una lista de todos los archivos de los que hay que hacer copia de seguridad.
3.  DP for mySAP.com (a través de la interfaz de BACKINT) hace copia de seguridad de todos los archivos solicitados de la base de datos e informa a BRBACKUP. BRBACKUP aumenta sus datos para incluir los archivos de los que se ha hecho copia de seguridad (corriente de datos a repositorio de archivos).
4.  BACKINT guarda los datos en TSM.
5.  SAPDBA actualiza el repositorio de archivos, que alberga información sobre el estado de los archivos.

##### Instalación de BACKINT

**Instalación de Data Protection for mySAP.com en UNIX (AIX, Solaris, HP-UX, Linux)**

Data Protection for mySAP.com para sistemas UNIX se suministra como un solo archivo ejecutable denominado

install_tdpr3_ora\_\<sistema operativo\>\_\<xx\>bit.bin donde \<xx\> es 32 ó 64 \<sistema operativo\> es aix43 para AIX 4.3 o aix51 para AIX 5.X o solaris o hpux o linux

Cuando invoque el archivo, se le guiará por el procedimiento de configuración de Data Protection for mySAP.com. Para asegurarse de que el procedimiento de configuración se lleva a cabo correctamente, lea detenidamente la siguiente descripción y siga las directrices de los paneles que se muestran durante la configuración.

Para instalar Data Protection for mySAP.com en un sistema UNIX, siga los pasos siguientes:

Lea el Aviso de release (RELNOTE) y el archivo README que se suministra con Data Protection for mySAP.com.

Conéctese como usuario root en la máquina servidor de bases de datos de mySAP.com.

El programa de instalación le guía por la instalación mediante una X-Window gráfica. Asegúrese de haber establecido la variable DISPLAY correctamente.

Invoque el archivo de instalación correspondiente a Data Protection for mySAP.com que se ajuste a su sistema operativo y a sus bases de datos Oracle.

Compruebe el resumen al final del diálogo de instalación. En el resumen, entre otra información, encontrará la ruta de instalación de Data Protection for mySAP.com. Los resultados de la instalación también quedan registrados en el registro de la instalación denominado log.txt situado en la ruta de instalación.

Durante la instalación, se realizan automáticamente las siguientes modificaciones en el sistema:

- se crea una entrada en /etc/inittab para iniciar el prole del proceso de fondo
- se crea una entrada en /etc/services necesaria para la comunicación interna.

Después de la instalación, dispone de los siguientes archivos en el sistema:

En la ruta de instalación de Data Protection for mySAP.com:

- backint
- prole
- createinfo
- backfm
- backintv3.cat
- initSID.bki
- libtdp_r3.\<ext\>
- archive.ksh
- backup.ksh
- crontab.sample
- dsm.opt
- dsm.sys
- gensortfile.sh
- inclexcl.list
- README
- RELNOTE
- TIPHINTS

se crea una carpeta denominada \_uninst, que contiene varios archivos.

Los archivos de configuración de DP for mySAP.com bajo los directorios de SAP (generalmente, /oracle/SID/dbs) son los siguientes:

- init\<SID\>.utl, donde ‘SID’ se sustituye por el SID de la base de datos Oracle proporcionado durante la instalación
- init\<SID\>.bki, donde ‘SID’ se sustituye por el SID de la base de datos Oracle proporcionado durante la instalación
- agent.lic (Sólo después de la instalación desde CD-ROM, no está contenido en el paquete de la web).

La configuración de Data Protection for mySAP.com se realiza con el siguiente valor básico de rendimiento:

Multiplexing 1

Parallel Sessions 1

Para ajustar los valores a sus requisitos, en el soporte de instalación se proporciona un perfil de ejemplo, initSID.utl. El procedimiento de instalación para UNIX copia el archivo y cambia su nombre por \$ORACLE_HOME/dbs/init\$ORACLE_SID.utl, donde \$ORACLE_HOME es el directorio inicial de Oracle y \$ORACLE_SID es el ID del sistema Oracle, por ejemplo /oracle/\<SID\>/dbs/init\<SID\>.utl.

Se aplican a la sintaxis las reglas siguientes:

- Cada línea se analiza por separado.
- Las palabras clave pueden empezar en cualquier columna de la línea.
- Las palabras clave no deben ir precedidas de ninguna cadena, a excepción de espacios en blanco.
- Si una palabra clave se encuentra varias veces, se utiliza la última.
- El proceso de archivos finaliza cuando se encuentra la palabra clave END o se llega al final del archivo.
- El símbolo de comentario es \#. Una exploración de la línea actual se detiene cuando se encuentra el símbolo de comentario. No se permite ningún comentario entre la palabra clave y los valores. Por ejemplo:

<!-- -->

          #BRARCHIVEMGTCLASS   MLOG1                    <--  correcto
          BRARCHIVEMGTCLASS    MLOG1  #                 <--  correcto
          BRARCHIVEMGTCLASS  # MLOG1                    <--  INCORRECTO

En cualquier caso se requieren unas cuantas palabras clave, pero la mayoría son opcionales. Cada una de las palabras clave opcionales tiene preestablecido un valor predeterminado.

**La contraseña**

Data Protection for mySAP.com se debe instalar una vez finalizada la instalación de TSM. TSM proporciona dos métodos de contraseña diferentes para proteger los datos. Data Protection for mySAP.com debe utilizar el mismo método que se haya especificado en TSM. Cuando se instala Data Protection for mySAP.com, se da por supuesto que el método de contraseña, seleccionado durante la instalación de TSM, es manejo manual de contraseñas (indicador PASSWORDACCESS). Los parámetros predeterminados para Data Protection for mySAP.com se establecen según este supuesto. Si para TSM se establece un método de contraseña diferente, consulte el apartado Métodos de manejo de contraseñas para ajustar los parámetros de Data Protection for mySAP.com.

Para poder trabajar con Data Protection for mySAP.com, tiene que proporcionar a Data Protection for mySAP.com la contraseña correspondiente al nodo de TSM. Este es un paso manual en el shell:

Conéctese como usuario de Oracle. Ejecutar el siguiente comando :

```
backint -p <ruta completa a archivo UTL>/init<SID>.utl -f contraseña
```

Introdusca la contraseña cuando se le solicite.

##### Problemas comúnes con BACKINT

Los mas errores frecuentes son:

Mensaje de error:

- BKI2001E: Error de socket al conectar con PROLE en \<Dirección-IP\>:\<PUERTO\>: Conexión rechazada.

<img src="/manuales/tivoli_tsm/manual_de_manejo_de_backups_con_tivoli/backint_problema.gif" class="align-right" /> Compruebe que el daemon background se está ejecutando, en UNIX.

Para comprobar el daemon, ejecute el comando:

- ps -ef \| grep prole

Compruebe la entrada contenida en /etc/services.

Compare el número de puerto indicado en el mensaje de error con el número de puerto que aparece en /etc/services. Vea también /etc/inittab. Si se ha establecido otro puerto mediante la opción -p\<PUERTO\>, compruébelo también.

Si todo esto no le sirve de ayuda, inicie el prole desde otro shell, prole -p \<PUERTO\>, y vuelva a intentar iniciar backint.

Mensaje de error:

- BKI5001E: Error de Tivoli Storage Manager: No se ha encontrado el servidor en el archivo de configuración.

El Servidor de TSM indicado en init\<SID\>.utl no coincide con el especificado en el archivo dsm.sys utilizado.

Mensaje de error:

- BKI5001E: Error de Tivoli Storage Manager: ANS1353E (RC53) Sesión rechazada: Se ha entrado un ID desconocido o incorrecto

Se puede producir este mensaje si el nodo de la stanza de servidor en el archivo UTL no es válido en el servidor. Vea también la referencia de la API (es decir, busque el mensaje en tivoli.com)

COLGADO: Si backint se cuelga después de entrar la contraseña, es posible que la dirección IP del servidor indicada en dsm.sys sea incorrecta.

# Configuracion de Clientes

# Cliente Standard

## GNU/Linux
{{% hint success %}}
It seems to be a better solution, I haven’t tested yet <http://adsm.org/files/TSM/Clients/Linux/Debian/>
{{% /hint %}}
{{% hint danger %}}
It doesn’t work as expected, I’ll spend more time on it, but actually I don’t have time
{{% /hint %}}
The TSM Client is proposed only as a RPM. With `alien` you can convert the `rpm` to a `deb`. Download the `tar` file : <ftp://ftp.software.ibm.com/storage/tivoli-storage-management/maintenance/client/v5r5/Linux/> from IBM website then untar the file and alien all the `rpm` :

```
test# tar xf 5.5.0.0-TIV-TSMBAC-LinuxX86.tar
test# alien *.rpm
```

Now you’ve got all `rpm` in `deb` format, you can install those packages with `dpkg` :

```
test# dpkg --force-all -i *.deb
```

You would notice there’s `api64` and `api` files, do not install the `api64` file on 32 bits system.

Now all your files are installed on `/opt/tivoli`. You have to add library in your ld cache first, those library are stored on `/opt/tivoli/tsm/client/api/bin`. You have to create a file in `/etc/ld.so.conf.d/`, for example `tivoli.conf` and add the path in it :

```
test# echo "/opt/tivoli/tsm/client/api/bin" > /etc/ld.so.conf.d/tivoli.conf
```

And then run `ldconfig` :

```
test# ldconfig
```

Finally just create a symbolic link in `/opt/tivoli/tsm/client/ba/bin` to `/opt/tivoli/tsm/client/lang/en_US` :

```
test# cd /opt/tivoli/tsm/client/ba/bin
test# ln -s ../../lang/en_US en_US
```

Now your client is ready to use. The rest is well documented in the IBM documentation.

### Init script

Now you need an init script. So this is mine (based on `/etc/init.d/skeleton`) :

```
#! /bin/sh
### BEGIN INIT INFO
# Provides:          dsmcad 
# Required-Start:    $all
# Required-Stop:     
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Run the TSM Client acceptator
# Description:       Run the TSM Client acceptator provided by the IBM rpm.
### END INIT INFO

# Author: Etienne Bagnoud <tchetch@i-james.com> 
#

PATH=/sbin:/usr/sbin:/bin:/usr/bin:/opt/tivoli/tsm/client/ba/bin
DESC="TSM Client acceptator"
NAME=dsmcad
DAEMON=/opt/tivoli/tsm/client/ba/bin/$NAME
SCRIPTNAME=/etc/init.d/$NAME

# Exit if the package is not installed
[ -x "$DAEMON" ] || exit 0

. /lib/init/vars.sh
. /lib/lsb/init-functions

do_start()
{
        start-stop-daemon --start --quiet --exec $DAEMON --test > /dev/null \
                || return 1
        start-stop-daemon --start --quiet --exec $DAEMON -- \
                || return 2
}

do_stop()
{
        # TODO It seems that start-stop-daemon crash when stopping dsmcad
        start-stop-daemon --stop --quiet --retry=20/TERM/5/KILL --name $NAME
        RETVAL="$?"

        return "$RETVAL"
}

case "$1" in
  start)
        [ "$VERBOSE" != no ] && log_daemon_msg "Starting $DESC" "$NAME"
        do_start
        case "$?" in
                0|1) [ "$VERBOSE" != no ] && log_end_msg 0 ;;
                2) [ "$VERBOSE" != no ] && log_end_msg 1 ;;
        esac
        ;;
  stop)
        [ "$VERBOSE" != no ] && log_daemon_msg "Stopping $DESC" "$NAME"
        do_stop
        case "$?" in
                0|1) [ "$VERBOSE" != no ] && log_end_msg 0 ;;
                2) [ "$VERBOSE" != no ] && log_end_msg 1 ;;
        esac
        ;;
  *)
        echo "Usage: $SCRIPTNAME {start|stop}" >&2
        exit 3
        ;;
esac

:
```

## Windows

Ejemplo para el nodo SRVWINDOWS con el password client

```
dsmcutil inst /name:"TSM Client Scheduler" /node:SRVWINDOWS /password:client /autostart:yes
```

Ref.: <http://publib.boulder.ibm.com/infocenter/tivihelp/v1r1/index.jsp?topic=%2Fcom.ibm.itsmfdt.doc%2Fans60000542.htm>

```
C:\Tivoli\TSM\baclient>dsmcutil inst /name:"TSM Client Scheduler" /node:SRVWINDOWS
 /password:client

TSM Windows NT Client Service Configuration Utility
Command Line Interface - Version 5, Release 5, Level 2.0
(C) Copyright IBM Corporation, 1990, 2009, All Rights Reserved.
Last Updated Mar 22 2009
TSM Api Verison 5.5.2

Command: Install TSM Client Service
Machine: SRVWINDOWS(Local Machine)



Installing TSM Client Service:

       Machine          : SRVWINDOWS
       Service Name     : TSM Client Scheduler
       Client Directory : C:\Tivoli\TSM\baclient
       Automatic Start  : no
       Logon Account    : LocalSystem
The service was successfully installed.


Creating Registry Keys ...

Updated registry value 'ImagePath' .
Updated registry value 'EventMessageFile' .
Updated registry value 'TypesSupported' .
Updated registry value 'TSM Client Scheduler' .
Updated registry value 'ADSMClientKey' .
Updated registry value 'OptionsFile' .
Updated registry value 'ClientNodeName' .
Updated registry value 'EventLogging' .

Generating registry password ...
Authenticating TSM password for node SRVWINDOWS ...

Connecting to TSM Server via client options file 'C:\Tivoli\TSM\baclient\dsm.opt
' ...

Password authentication successful.

The registry password for TSM node SRVWINDOWS has been updated.


Starting the 'TSM Client Scheduler' service ...

The service was successfully started.
```

Ejemplo de como instalar el servicio TDP para Domino

```
dsmcutil install /name:"TDP for Lotus Domino Schedule" /node:SRVLOTUS01 /password:client /optfile:"c:\tivoli\tsm\domino\dsm.opt" /clientdir:"c:\tivoli\tsm\baclient" /schedlog:"c:\tivoli\tsm\domino\domsch.log"
```

# Cliente TDP

# Configuración de nodo TDP para Backup Online de SAP

<img src="/manuales/tivoli_tsm/manual_de_manejo_de_backups_con_tivoli/esquema.gif" class="align-center" alt="esquema.gif" />

1 - La base debe estar en modo ARCHIVE LOG

2 – Instalación

Debemos correr el ejecutable del instalador

Elegir SID

Elegir Oracle_Home/dbs

Configurar primero el nodo en TSM server

Configurar en /opt/tivoli/tsm/client/api/bin los archivos dsm.sys y dsm.opt

```
# cat dsm.opt
************************************************************************
* IBM Tivoli Storage Manager                                           *
*                                                                      *
* Sample Client User Options file for AIX and SunOS                    *
************************************************************************

SErvername       TSERVER1
Quiet
```

```
# cat dsm.sys
************************************************************************
* IBM Tivoli Storage Manager                                           *
*                                                                      *
* Sample Client System Options file for AIX and SunOS                  *
************************************************************************


SErvername  TSERVER1
  COMMmethod         TCPip
  TCPPort            1500
  TCPServeraddress   10.1.1.90

ERRORLOGNAME /tmp/dsmerror.log
```

Crear links simbólicos en /opt/tivoli/tsm/client/api/bin64 apuntando a los creados anteriormente.

Configuración TDP :

```
/oracle/<SID>/102_64/dbs # vi init<SID>.utl
/oracle/<SID>/102_64/dbs # vi init<SID>.sap
```

**ini\<SID\>.utl**

```
#**************************************************************************
# Statement for servers and paths.
# Multiple servers may be defined.
#**************************************************************************
SERVER         TSMSERVER                  # Servername
  SESSIONS            2                  # Maximum number of sessions 
                                         # to server_a
  PASSWORDREQUIRED    YES                # Use a password
  ADSMNODE            SERVER_TDP               # Tivoli Storage Manager Nodename
  BRBACKUPMGTCLASS    BACKUP_ONLINE                # Mgmt-Classes
  BRARCHIVEMGTCLASS   LOGS        # Mgmt-Classes
# TCP_ADDRESS         192.168.1.1        # IP address of network interface 
                                         # on server_a 
                                         # Overrides IP address of dsm.sys
# USE_AT              0 1 2 3 4 5 6      # Days when server_a is used for 
                                         # backup
#**************************************************************************
# USE_AT : 0=Su 1=Mo 2=Tu 3=We 4=Th 5=Fr 6=Sa
# The valid range of USE_AT is from 0 to 6.
# Default: all days
#**************************************************************************
```

Podemos por ejemplo espejar el backup a dos pools

```
  BRARCHIVEMGTCLASS   LOGS LOGS2        # Mgmt-Classes
```

Si hay problemas en que el backup no arranca verificar:

```
# ps -ef | grep prole
root      1123 26374  0 10:38 pts/1    00:00:00 grep prole
root     32757     1  0 10:17 ?        00:00:00 /opt/tivoli/tsm/tdp_r3/ora64/prole -p tdpr3ora64
```

Luego hay que inicializar el nodo por única vez con la password

Ejecutar

```
orasid # backint -p /oracle/<SID>/102_64/dbs/init<SID>.utl -f password
```

Importante: verificar que el archivo /tmp/dsmerror.log tenga permisos 777

Luego hay que poner en modo archive la base de datos y probar un los backup online desde SAP por medio de la **Trx. DB13**.

# Problemas comunes

**Reinstalar la licencia de TSM**

```
register license file=*.lic 
```

**ANS1316E The server does not have enough recovery log space**

```
dsmserv reset logm
```

# Problemas no comunes

## Restore de una base TSM

1.  Instalar el servidor TSM y todos sus parches hasta llegar a la versión que tenía el dañado.
2.  Copiar del servidor TSM viejo en la carpeta F:\tsmdata\dbbackup los archivos volhist.out, devconf.out y el último \*.dbb a la carpeta c:\tivoli\tsm\server1.
3.  Ejecutar c:\tivoli\tsm\server\dsmserv loadformat 1 f:\tsmdata\server1\log1.dsm 1000 1 f:\tsmdata\server1\db1.dsm
4.  DSMSERV RESTORE DB DEV=FILE VOL=f:\tsmdata\dbbackup\63423647.dbb COMMIT=Yes
5.  Ejecutar dsmserv auditdb fix=yes
6.  Conectarse a la consola y verificar que se vea la unidad de tape con query path y query dr.
7.  Recrear los storage pool de disco.
8.  Verificar el path de la device class FILE para que apunte a F:\TSMDATA\DBBACKUP.
9.  Ejecutar un backup de la base desde la consola de TSM **backup db t=f dev=file wait=yes**.

## Disaster Recovery for SAP R/3 on Oracle

## Procedimiento para recuperar una base de datos correspondiente a una instancia SAP luego de un desastre

Este procedimiento está basado en la nota de SAP número 96848, cualquier duda referirse a la misma.

Requisitos:

Contar con un full backup online u offline (en este procedimiento el ejemplo es con full backup online realizado con brbackup) La base de datos debe estar en modo archive (lo más lógico es que esté) En caso de haberse perdido todo debe instalarse previamente TSM y luego recuperarse de un backup realizado con TSM para los binarios de SAP y Oracle más todos los archivos de configuración. Para utilizar el brrestore debe existir el archivo initSID.sap, ej. initDE1.sap

Pasos del recovery:

1.  Ubicar el archivo de log del último backup online bueno, el nombre figura en el archivo /oracle/SID/sapbackup/backSID.log, ahífigura la fecha y hora de realización del backup, y la primer columna denota el nombre buscado, ej: bdnrsjbq anf.
2.  Primero habría que asegurarse que ese backup se realizócorrectamente, entonces editando ese archivo, vi bdnrsjbq anf, buscar que el código de retorno sea cero, ej BKI0024I: Return code is: 0, lo mismo para el brarchive al final del mismo.
3.  Loguearse con el usuario ora\<SID\> y realizar el full restore con el comando: **brrestore -b bdnrsjbq.anf -m full**, siendo bdnrsjbq.anf el archivo que contiene el detalle del backup online.
4.  Recuperar todos los archive logs necesarios, esto se realiza con el comando **brrestore -a log_seq1-loq_seqN**, donde estos dos parámetros son los números de secuencia de los archive logs ya salvados por el brarchive que figuran en el archivo /oracle/SID/saparch/archSID.log.
5.  Luego de recuperados los archivos realizar los siguientes pasos conectandose a oracle con **sqlplus “/ as sysdba”**.

<!-- -->

          SQL> set autorecovery off
          SQL> startup mount
          SQL> recover database using backup controlfile until cancel;    --> acá va a ir sugiriendo los archive logs hasta que uno tipee cancel.
          SQL> alter database open resetlogs;             --> esto pone a cero los redo logs online y los recrea.

# Consultas SQL de Tivoli

## Introducción

La sintaxis básica de SQL es

```sql>
La norma de operador de SQL, '=', mayúsculas y minúsculas al comparar cadenas. Se puede utilizar SELECT DISTINCT, para eliminar los registros duplicados, y obtener datos de más de una tabla utilizando el operador de combinación. También se pueden combinar cuando las declaraciones están utilizando el operador AND.

NOTA: El SQL de Tivoli TSM **Es un lenguaje de consulta de sólo lectura**.

TSM SQL query command does not support the full SQL language syntax. The following operations will not work:
Los comandos de consulta SQL de Tivoli TSM no son compatibles con toda la sintaxis de lenguaje SQL. Las siguientes operaciones no van a funcionar:

  * **UNION**
  * **INTERSECT**
  * **EXCEPT**
  * //Las subconsultas que devuelven varios valores//
  * //No se puede utilizar un punto y coma como terminador de comando//

Se pueden combinar dos tablas por consulta, como por ejemplo:
<code sql>
SELECT nodes.domain_name,summary.activity FROM nodes, summary
```

Se pueden también estableces alias para la consulta :

```sql>
==== Como son las tablas de TSM ====
Para conocer las tablas que existen en TSM y lo que contienen, ejecute las siguientes consultas :

<code sql>
select * from syscat.tables
select * from syscat.columns
select * from syscat.enumtypes
```

| Tabla | Campo | Descripcion |
|----|----|----|
| ADSM | ACTLOG | Server activity log |
| ADSM | ADMINS | Server administrators |
| ADSM | ADMIN_SCHEDULES | Administrative command schedules |
| ADSM | ARCHIVES | Client archive files |
| ADSM | AR_COPYGROUPS | Management class archive copy groups |
| ADSM | ASSOCIATIONS | Client schedule associations |
| ADSM | AUDITOCC | Server audit occupancy results |
| ADSM | BACKUPS | Client backup files |
| ADSM | BACKUPSETS | Backup Set |
| ADSM | BU_COPYGROUPS | Management class backup copy groups |
| ADSM | CLIENTOPTS | Client Options |
| ADSM | CLIENT_SCHEDULES | Client schedules |
| ADSM | CLOPTSETS | Client Option Sets |
| ADSM | COLLOCGROUP | Collocation groups |
| ADSM | CONTENTS | Storage pool volume contents |
| ADSM | DATAMOVERS | Data Movers |
| ADSM | DB | Server database information |
| ADSM | DBBACKUPTRIGGER | Database backup trigger information |
| ADSM | DBSPACETRIGGER | Database space trigger information |
| ADSM | DBVOLUMES | Database volumes |
| ADSM | DEVCLASSES | Device Classes |
| ADSM | DOMAINS | Policy domains |
| ADSM | DRIVES | Drives |
| ADSM | DRMCSTGPOOLS | Copy storage pools managed by the disaster recovery manager |
| ADSM | DRMEDIA | Physical volumes managed by move drmedia |
| ADSM | DRMMACHINE | Disaster recovery manager machine information |
| ADSM | DRMMACHINECHARS | Disaster recovery manager machine characteristics |
| ADSM | DRMMACHINENODE | Disaster recovery manager machine node associations |
| ADSM | DRMMACHINERECINST | Disaster recovery manager machine recovery instructions |
| ADSM | DRMMACHINERECMEDIA | Disaster recovery manager machine recovery media associations |
| ADSM | DRMPSTGPOOLS | Primary storage pools managed by the disaster recovery manager |
| ADSM | DRMRECOVERYMEDIA | Disaster recovery manager recovery media |
| ADSM | DRMSRPF | Recovery plan files in source server |
| ADSM | DRMSTANZA | Recovery plan file stanza names |
| ADSM | DRMSTATUS | Disaster recovery manager status information |
| ADSM | DRMTRPF | Recovery plan files in target server |
| ADSM | EVENTS | Scheduled Event Results |
| ADSM | FILESPACES | Client file spaces |
| ADSM | GROUP_MEMBER | Group Members |
| ADSM | LIBRARIES | Libraries |
| ADSM | LIBVOLUMES | Library volumes |
| ADSM | LICENSES | Server feature licenses |
| ADSM | LICENSE_DETAILS | License usage details |
| ADSM | LOG | Server recovery log information |
| ADSM | LOGSPACETRIGGER | Recovery Log space trigger information |
| ADSM | LOGVOLUMES | Recovery log volumes |
| ADSM | MEDIA | Physical volumes managed by move media |
| ADSM | MGMTCLASSES | Management classes |
| ADSM | NODEGROUP | Node Groups |
| ADSM | NODES | Client nodes |
| ADSM | OCCUPANCY | Client storage occupancy |
| ADSM | OPTIONS | Server Options |
| ADSM | PATHS | Paths |
| ADSM | POLICYSETS | Policy sets |
| ADSM | PROCESSES | Client schedule associations |
| ADSM | PROFILES | Profiles |
| ADSM | PROF_ASSOCIATIONS | Profile associations |
| ADSM | RECLAIM_ANALYSIS | Server reclamation analysis table |
| ADSM | RESTORES | Client restore operations |
| ADSM | SAN | SAN attached devices |
| ADSM | SCRIPTS | Server Command Scripts |
| ADSM | SCRIPT_NAMES | Server Command Script Names |
| ADSM | SERVERS | Remote server nodes |
| ADSM | SERVER_GROUP | Server Groups |
| ADSM | SESSIONS | Active client sessions |
| ADSM | SHREDSTATUS | Shred Status |
| ADSM | SPACEMGFILES | Client space-managed files |
| ADSM | STATUS | Server status |
| ADSM | STGPOOLS | Storage pools |
| ADSM | STGSPACETRIGGER | Storage Pool space trigger information |
| ADSM | SUBSCRIPTIONS | Subscriptions |
| ADSM | SUMMARY | Server Activity Summary Table |
| ADSM | VFSMAPPINGS | Virtual Filespace Mappings |
| ADSM | VOLHISTORY | Volume history information |
| ADSM | VOLUMES | Storage pool volumes |
| ADSM | VOLUMEUSAGE | SEQUENTIAL volume usage by client node |
| SYSCAT | COLUMNS | SQL Table Column Catalog |
| SYSCAT | ENUMTYPES | SQL Enumerated Types Catalog |
| SYSCAT | TABLES | SQL Table Catalog |

La tabla **SUMMARY** contiene una gran cantidad de entradas útiles para las estadísticas generales. Un par de los campos son **SUMMARY.ACTIVITY** Y **SUMMARY.SUCCESFUL**. El campo de la actividad contiene en la actualidad, ‘TAPE MOUNT’, ‘FULL_DBBACKUP’, ‘INCR_DBBACKUP’, ‘EXPIRATION’, ‘RECLAMATION’, ‘MIGRATION’, ‘MOVE DATA’, ‘STGPOOL BACKUP’, ‘BACKUP’, ‘RESTORE’, ‘ARCHIVE’ y ‘RETRIEVE’. El campo successful puede ser ‘YES’ o ‘NO’. Sin embargo, no se puede confiar en la tabla de resumen para informar sobre el éxito de los eventos de cliente como de backup y restauración, ya que sólo informa sobre los progresos realizados hasta el momento.

El campo DATE en la tabla EVENTS no admite expresiones como ‘scheduled_start \>= current_timestamp-24 hours’. Si emite esta consulta a las 14:00 devolverá todos los eventos que se inició después de la medianoche de hoy, pero no las que existen entre las 14:00 y la medianoche de ayer. Usted puede obtener los resultados correctos mediante la combinación de la marca de tiempo en relación con una marca de tiempo constante, por ejemplo, si ayer fue el 25 de marzo, entonces este va a funcionar.

### Formateo de consultas SQL

Si se introduce consultas SQL desde la línea de comandos los resultados se obtendrán en formato tabular. Se puede canalizar el comando a un archivo y obtener los resultados en formato delimitado por comas para importar a una hoja de cálculo Excel o similar.

Ejemplo de como exportar los resultados a un archivo CSV con campos delimitados por comas :

```
dsmadmc -id=adminid -password=adminpassword -commadelimited 'select etc '  > filename
```

Ejemplo de como direccionar la salida a archivos :

```sql>
 summary.txt
select * from summary where date>current_timestamp - 24 hours > summary.out
```

### Uso de columnas indexadas para acelerar consultas

En TSM las consultas SQL se pueden ejecutar durante mucho tiempo, y utilizar una gran cantidad de recursos. Esto es generalmente debido a que están buscando en la base de datos completa para obtener los datos que desee. Puede reducir la cantidad de datos de la búsqueda mediante la selección de los datos específicos de una columna indexada utilizando una sentencia WHERE.

Para saber qué columnas están indexadas, utilice la siguiente consulta :

```sql>
Obtendremos un resultado como este :
<code>
 TABSCHEMA: ADSM
      TABNAME: MEDIA
      COLNAME: LRD
        COLNO: 9
 INDEX_KEYSEQ: 
  INDEX_ORDER: 
     TYPENAME: TIMESTAMP
       LENGTH: 0
        SCALE: 0
        NULLS: TRUE
      REMARKS: Last Reference Date

    TABSCHEMA: ADSM
      TABNAME: MGMTCLASSES
      COLNAME: DOMAIN_NAME
        COLNO: 1
 INDEX_KEYSEQ: 1
  INDEX_ORDER: A
     TYPENAME: VARCHAR
       LENGTH: 30
        SCALE: 0
        NULLS: FALSE
      REMARKS: Policy Domain Name
```

Esto indica que la columna DOMAIN_NAME de la tabla de MGMTCLASSES está indexada, pero la columna LRD en la tabla MEDIA no esta indexada. Así que si usted ejecuta una consulta, como :

```sql>
La consulta se ejecutará mas rápido.

==== Formato de datos SQL ====
El formato timestamp o marca de tiempo para consultas SQL de TSM es el siguiente:

<code>
  'yyyy-mm-dd hh:mm:ss.nnnnnn'

  yyyy = año
  mm = mes
  dd = dia
  hh = hora
  mm = minutos
  ss = segundos 
  nnnnnn = fracción de segundo
```

‘ss’ y ‘nnnnnn’ son parámetros opcionales. Cuando se hace referencia a una marca de tiempo, por ejemplo para seleccionar los registros que se iniciaron después de las 12:00 el 21 de julio se especificaría **start_time \<= ‘2005-07-21 12:00:00’**.\
Se puede dividir el sello de tiempo usando una función de fecha o una función de tiempo. Por ejemplo, para seleccionar los registros que se iniciaron el 21 de julio usar **date(start_time) \>= ‘2005-07-21’**.\
Si sólo desea ver los registros que comenzaron después de las 21:00, deberá añadir **time(start_time) \<= ‘12:00:00’**.

### Como combinar dos tablas en una consulta

Es posible combinar dos tablas de TSM en una consulta, pero tenga en cuenta que la base de datos de Tivoli Storage Manager no es realmente relacional, por lo que el join toma algo de tiempo y utiliza altos recursos. Puede ser más rápido que hacer dos consultas, copiar los resultados a continuación y combinar los datos con un programa externo. Para realizar el join :

- Ambas tablas deben tener una columna que contenga los mismos datos
- Se debe asignar un alias a cada table en el FROM
- Seleccione la columna común en ambas tablas usando el nombre de aliasname.table
- El JOIN de datos se debe realizar con un WHERE alias1.column2=alias2.column2

Por ejemplo

```sql>
Esto combina la columna de porcentaje de volumen utilizado a partir de la tabla de volúmenes de la columna con nombre de nodo en la tabla volumeusage, combinado con la columna de volumen de cada uno. Tener en cuenta que esta es una consulta que hace uso intensivo de CPU.

===== Consultas SQL =====

==== Consultas de Administración ====
=== Ver información del servidor ===
<code sql>
SELECT server_name, platform, - 
VARCHAR(version)||'.'||VARCHAR(release)||'.'||VARCHAR(level)||'-'||VARCHAR(sublevel), -
server_hla, server_lla, server_url, logmode, crossdefine, licensecompliance FROM status
```

#### Ver información general de los nodos

```sql>
<code sql>
select NODE_NAME as Nodo, PLATFORM_NAME as Plat, CLIENT_OS_LEVEL as Os, -
(concat(concat(concat(trim(cast(CLIENT_VERSION as character(4))),'-'), -
concat(trim(cast(CLIENT_RELEASE as character(4))),'-')), -
cast(cast(CLIENT_LEVEL as decimal(4,0)) as character(2)))) as TSM, -
tcp_address, tcp_name  as Host, -
CURRENT_DATE-date(LASTACC_TIME) as Ult_Acceso from nodes order by ULT_ACCESO
```

#### Ver detalles de Copygroups de Archive

```sql>
=== Ver detalles de Copygroups de Backup ===
<code sql>
SELECT DOMAIN_NAME,SET_NAME,CLASS_NAME,VEREXISTS,VERDELETED,RETEXTRA,RETONLY,DESTINATION FROM bu_copygroups             
```

#### Ver detalles de Management Classes

```sql>
=== Ver nodo bloqueados ===
<code sql>
SELECT nodes,locked FROM nodes WHERE locked='YES'
```

#### Ver slots de la libreria asociados a cada cinta

```sql>
=== Ver información de Backup de la base de datos ===
<code sql>
SELECT * FROM volhistory WHERE ( type='BACKUPFULL' OR type='BACKUPINCR' ) -
AND date_time>=current_timestamp-24 hours
```

#### Cantidad de volúmenes Scratch permitidos por storage pool

```sql>
=== Porcentaje de uso por storage pool ===
<code sql>
SELECT stgpool_name,pct_utilized FROM stgpools
```

#### Uso por storage pool en MB y en cantidad de archivos

```sql>
=== Estado de los volúmenes dentro de la libreria ===
<code sql>
SELECT volumes.volume_name, volumes.stgpool_name, volumes.pct_utilized, volumes.pct_reclaim, volumes.status, -
volumes.access FROM volumes, libvolumes WHERE volumes.volume_name=libvolumes.volume_name ORDER BY stgpool_name
```

```sql>
=== Cantidad de volúmenes por storage pool dentro de la libreria ===
<code sql>
SELECT volumes.stgpool_name, count(*) FROM volumes, libvolumes WHERE -
volumes.volume_name=libvolumes.volume_name GROUP BY stgpool_name
```

#### Número de volúmenes por libreria

```sql>
=== Número de volúmenes con errores dentro de la libreria ===
<code sql>
SELECT volumes.volume_name, volumes.stgpool_name, volumes.pct_utilized, volumes.status, -
volumes.write_errors, volumes.read_errors FROM volumes, libvolumes WHERE -
volumes.volume_name=libvolumes.volume_name AND ( volumes.write_errors>0 OR volumes.read_errors>0 )
```

### Consultas rápidas rutinarias

#### Listar todas las cintas utilizadas por un nodo

```sql>
=== Listar todos los nodos que utilizan cierta cinta ===
<code sql>
SELECT DISTINCT node_name,volume_name,stgpool_name FROM volumeusage WHERE volume_name='xxx'
```

#### Cintas usadas hoy

```sql>
=current_timestamp - 24 hours)
```

#### La última fecha de escritura para todas las cintas

```sql>
=== Litas las cintas con error y tipo de error ===
<code sql>
SELECT volume_name, read_errors,write_errors FROM volumes WHERE (read_errors >= 1 OR write_errors >= 1)
```

#### Cantidad de MB respaldados las últimas 24 horas ordenado por nodo

```sql>
= current_timestamp - 24 hours) -
GROUP BY ENTITY order by "MB xfer"
```

#### Cantidad de MB total respaldados las últimas 24 horas

```sql>
=current_timestamp - 24 hours)
```

#### El número de cintas escritas las ultimas 24 horas

```sql>
=current_timestamp - 24 hours)
```

#### Nodos que en el día anterior realizaron restauraciones

```sql>
=current_timestamp - 24 hours)
```

#### Cintas en uso y máximo de scratch permitidos

```sql>
=== Listas de cintas en estado filling y cuanto esta lleno el storage pool ===
<code sql>
SELECT a.stgpool_name AS "STORAGE POOL", -
CAST(a.EST_CAPACITY_MB AS DECIMAL(12,0)) AS "SIZE MB", -
CAST(a.PCT_UTILIZED AS DECIMAL(2,0)) AS "PCT", -
count(*) AS Filling, CAST(AVG(b.PCT_UTILIZED) AS DECIMAL(2,0)) AS "PCT Full" -
FROM stgpools a, volumes b -
WHERE ((a.stgpool_name = b.stgpool_name AND upper(status)='FILLING') -
OR (a.stgpool_name = b.stgpool_name AND devclass_name='DISK')) -
GROUP BY a.stgpool_name, a.EST_CAPACITY_MB, a.PCT_UTILIZED
```

#### Lista paginas expiradas de la Base de datos

```sql>
=== Conocer resultados de eventos ===
<code sql>
SELECT SCHEDULED_START, events.node_name, events.STATUS, nodes.email_address FROM events, nodes WHERE events.node_name = nodes.node_name
```

### Operaciones de conteo de elementos

#### Consultar copias de seguridad de los pooles

```sql>
=== Consultar cantidad de filespaces por nodo ===
<code sql>
SELECT node_name, count(*) AS "Number of Filespaces" FROM filespaces GROUP BY node_name ORDER BY 2
```

#### Consultar cantidad de plataforma por nodo

```sql>
=== Consultar cantidad de usuarios administradores de algo ===
<code sql>
SELECT count(*) AS "Number of Administrators" FROM admins
```

#### Consultar cantidad de archivos de tipo Backup por nodo

```sql>
=== Consultar cantidad de archivos de tipo Archive por nodo ===
<code sql>
SELECT node_name,sum(num_files) AS "Number of Archive Files" FROM occupancy WHERE type='Arch' GROUP BY node_name
```

#### Consultar numero de asociaciones por Schedule

```sql>
=== Consultar numero de backupsets ===
<code sql>
SELECT count(*) AS "Number of Backupsets" FROM backupsets
```

#### Consultar numero de opciones de cliente

```sql>
=== Consultar numero de Collocation Groups ===
<code sql>
SELECT count(*) AS "Number of Collocation Groups" FROM collocgroup
```

#### Consultar numero de Archive CopyGroups

```sql>
=== Consultar numero de Backup CopyGroups ===
<code sql>
SELECT count(*) AS "Number of Backup CopyGroups" FROM bu_copygroups
```

#### Consultar numero de Data Movers

```sql>
=== Consultar numero de Device Classes ===
<code sql>
SELECT count(*) AS "Number of Device Classes" FROM devclasses
```

#### Consultar numero de Dominios

```sql>
=== Consultar numero de Drives ===
<code sql>
SELECT count(*) AS "Number of Drives" FROM drives
```

#### Consultar numero de Librerias

```sql>
=== Consultar numero de Cintas en la libreria ===
<code sql>
SELECT count(*) AS "Number of Library Volumes" FROM libvolumes
```

#### Consultar numero de Cintas totales

```sql>
=== Consultar numero de Management Classes ===
<code sql>
SELECT count(*) AS "Number of Management Classes" FROM mgmtclasses
```

#### Consultar grupos de nodos

```sql>
=== Consultar rutas de disposivitos ===
<code sql>
SELECT count(*) AS "Number of Device Paths" FROM paths
```

#### Consultar set de Politicas

```sql>
=== Consultar set de Schedules de clientes ===
<code sql>
SELECT count(*) AS "Number of Client Schedules" FROM client_schedules
```

#### Consultar numero de tareas programadas

```sql>
=== Consultar numero de scripts en el servidor ===
<code sql>
SELECT count(*) AS "Number of Server Scripts" FROM scripts
```

#### Consultar numero de servidores definidos

```sql>
=== Consultar numero de grupos de servidores definidos ===
<code sql>
SELECT count(*) AS "Number of Servers Groups Defined" FROM server_group
```

#### Consultar numero de storage pools definidos

```sql>
=== Cuantos clientes hay registrados por dominio ===
<code sql>
SELECT domain_name,num_nodes FROM domains
```
