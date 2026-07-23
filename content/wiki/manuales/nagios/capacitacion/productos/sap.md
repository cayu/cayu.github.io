---
title: "SAP"
date: 2015-11-05T10:43:03-03:00
---

Para tener aclarados conceptos, recomiento leer la siguiente sección **[Aprendiendo SAP](/aprendiendo_sap/)**.

## Análisis manual por capas de la aplicación SAP que corresponda

- Análisis de interfaz de usuarios (SAP GUI), versiones ,estabilidad etc.
- Comunicaciones hasta el cliente
- ABAP: Verificar funcionamiento de la aplicación
- Locks, Reportes corriendo y privilegios de usuarios
- Sistemas o servidores RFC externos
- SAP BASIS
- DB (Oracle y SQL)
- OS
- CAPA A BAJO NIVEL (Hipervisor):
  - VMWare o XEN
  - Utilidades de virtualización (VMware tools)
  - Ver método de acceso a datos, tipo de controladores SCSI, Reserva de memoria / oversize, tipo de CPU (Virtual socket y Cirtual CPU), reserva de CPU, prioridad de acceso, configuración de red virtual.
  - CAPA A BAJO NIVEL: Switch de fibra conectado al storage (balanceo round-robin, alta disponibilidad etc).
  - STORAGE (Parity groups; Velocidad de disco de las LUNs y prioridades).

## Análisis automatizado

Para el monitoreo de SAP, por experiencia personal **no es recomendable utilizar sistemas externos**. Siempre es preferible hacer un mínimo de desarrollo en **ABAP** o utilizar **SAP Solution Manager** (Solman). Ya que si realizamos desarrollos a medida por medio de SAP RFC SDK, si llegamos a tener problemas en el camino, enlace, lentitud en la consulta etc, esto nos va a generar una gran cantidad de DUMPS en SAP. Igualmente voy a detallar su implementación en Nagios para quienes no dispongan de una implementación de **Solman** o no puedan hacer un desarrollo ABAP.

## Computing Center Management System (CCMS)

SAP CCMS - Sistema de Gestión Centralizado de computo ó de proceso de datos. Es un conjunto de herramientas y utilidades para los administradores, que permite realizar un monitoreo para administrar y configurar SAP. En los elementos internos de SAP que se pueden chequear estan estos :

- **Procesos de Fondo** – Monitorea el Estatus de los Procesos de Fondo en los sistemas SAP.
- **Memoria** - Sus estadísticas claves y de cambios.
  - **Buffer** – Monitorea la Utilización del Buffer SAP, extended memory etc.
- **Spool** – Monitorea la utilización del sistema Spool.
- **RFC** - Estado general de conexiones RFC.
- **Diálogos** – Monitorea los valores del sistema de Diálogos SAP.
- **Backups** – Estado de Backup full en Incremental, si estos estan integrados a BRtools.
- *Podemos tener una detección automática de problemas mediante el uso y configuración de umbrales de alertas.*

A travéz de **CCMS** podemos tener una integración con herramientas externas a través de BAPI. Para obtener los datos internos de monitoreo SAP.

FIXME Actualmente se esta dejando de utilizar exclusivamente **CCMS**, para utilizarlo integrado desde **Solman**

## NAGIOS

Desde Nagios si utlizar ningún tipo de integración podemos monitorear el estado de puertos TCP y variables internas de Sistema Operativo por medio del protocolo SNMP.

### Procesos

#### GNU/Linux

En el caso de Sistema Operativo GNU/Linux hay que chequear :

- Load average
- Memoria física y SWAP
- Almacenamiento
- Servicio de impresión

Procesos de SAP sobre Linux :

- DISP+WORK
- Message Server

Procesos SAP Java:

- jvm_dispatcher
- jvm_sdm
- jvm_server0
- Respuesta HTTP y su contenido a parsear

#### Windows

En el caso de Sistema Operativo Microsoft Windows hay que chequear :

- Carga de CPU
- Memoria física
- Memoria virtual
- Almacenamiento
- Servicio Windows de instancia SAP

### Conectividad

Puertos TCP a descatar del sistema SAP :

| Servicio                     | Puerto TCP     |               |                 |
|------------------------------|----------------|---------------|-----------------|
| SAP Dispatcher               | 3200 - Synr 00 |               |                 |
| SAP Gateway                  | 3300 - Synr 00 |               |                 |
| SAP Internal Message server  | 3900           |               |                 |
| SAP Message Server           | 3600           |               |                 |
| J2EE Engine Dispatcher Ports |                |               |                 |
| Service Name                 | Port Number    | Default Value | Range (min-max) |
| HTTP                         | 5NN00          | 50000         | 50000-59900     |
| HTTP over SSL                | 5NN01          | 50001         | 50001-59901     |
| IIOP                         | 5NN07          | 50007         | 50007-59907     |
| IIOP Initial Context         | 5NN02          | 50002         | 50002-59902     |
| IIOP over SSL                | 5NN03          | 50003         | 50003-59903     |
| P4                           | 5NN04          | 50004         | 50004-59904     |
| P4 over HTTP                 | 5NN05          | 50005         | 50005-59905     |
| P4 over SSL                  | 5NN06          | 50006         | 50006-59906     |
| Telnet                       | 5NN08          | 50008         | 50008-59908     |
| JMS                          | 5NN10          | 50010         | 50010-59910     |

### Procesos del Servidor

| DISP+WORK      | dw.sap \> (número de diálogos) |
|----------------|--------------------------------|
| Message Server | ms.sap \< 0                    |

#### HANA

| hdbdaemon | Se encarga de iniciar los servicios mencionados a continuación en el orden correcto |
|----|----|
| hdbnameserver | Este proceso se encarga de saber la ubicación de las tablas en el index |
| hdbpreprocessor | Se encarga de analizar los datos de texto y la extracción de la información en que se basan las capacidades de búsqueda de texto |
| hdbindexserver | Servidor de índice de datos, responsable de la gestión de la conexión, el proceso de SQL, manipulación de metadatos, almacenamiento fila y columnas |
| hdbstatisticsserver | Contener datos / vistas de administrador del sistema de base de datos de SAP HANA para supervisar el estado del sistema, de sus servicios y el consumo de recursos del sistema |

## NAGIOS + SAP CCMS

Hay diferentes desarrollos de terceros para poder integrar los chequeos de Nagios con los datos obtenidos de SAP CCMS, utilizando la libreria RFC SDK.

**check_sap**

Para que funcionen correctamente algunas cosas de los plugins de Nagios CCMS, hay que realizar algunas minimas modificaciones por ejemplo en : En el plugin “Nagios SAP CCMS” hay que modificar algunas lineas de los archivos agnt_mon.h y sap_moni_ccm.h ya que en estos se establece el path de acceso a los archivos de configuración que por defecto los busca en /etc/sapmon, pero nuestro objetivo es que los busque en /usr/local/nagios/etc/sapmon, de una cierta manera quede mas centralizao u ordenado.

- **agnt_mon.h**

```c>
  * **sap_moni_ccm.h**
<code c>
    #define AGENT_INI_FILE “/usr/local/nagios/etc/sapmon/agent.cfg”
    #define AGENT_LOGIN_FILE “/usr/local/nagios/etc/sapmon/login.cfg”
```

Archivo */usr/local/nagios/etc/sapmon/login.cfg* de ejemplo :

```ini>
Archivo ///usr/local/nagios/etc/sapmon/agent.cfg// de ejemplo :

**DESCRIPTION** = <Description of the Template>    Arbitrary description field \\
**MONI_SET_NAME** = <Monitor collection>            Monitor collection in RZ20 \\
**MONI_NAME** = <Monitor name>                      Name of the specific monitor \\
**MAX_TREE_DEPTH** = <number>                      Monitor loading depth \\
**PATTERN_0** = <SAPSID>\<Context>\<Monitor object>\<Monitor attribut> \\

<code ini>
[TEMPLATE_00]
DESCRIPTION="Load Average"
MONI_SET_NAME=SAP CCMS Admin Workplace
MONI_NAME="Operating System"
MAX_TREE_DEPTH=0
PATTERN_0="BCE\bcemain_BCE_26\CPU\5minLoadAverage"
PATTERN_0="*\*\CPU\5minLoadAverage"

[TEMPLATE_01]
MONI_SET_NAME=SAP CCMS Admin Workplace
MONI_NAME="Operating System"
MAX_TREE_DEPTH=0
PATTERN_2="BCE\bcemain_BCE_26\CPU\CPU_U*"

[TEMPLATE_02]
VALUE=DIALOG_RESPONSE_TIME

[TEMPLATE_03]
SYSTEM=BCE
APPL-SERVER=bcemain*
VALUE=DIALOG_RESPONSE_TIME

[TEMPLATE_04]
MONI_SET_NAME="SAP CCMS Monitor Templates" 
MONI_NAME="Dialog Overview"
PATTERN_0="BCE\*\Dialog\FrontEndNetTime"


[TEMPLATE_05]
MONI_SET_NAME="SAP CCMS Monitor Templates" 
MONI_NAME="Dialog Overview"
PATTERN_0="BCE\PWD*\Dialog\FrontEndNetTime"

[TEMPLATE_005]
MONI_SET_NAME="SAP CCMS Monitor Templates" 
MONI_NAME="Dialog Overview"
PATTERN_0="P10\*\Dialog\FrontEndNetTime"

[TEMPLATE_06]
MONI_SET_NAME="SAP CCMS Monitor Templates" 
MONI_NAME="Dialog Overview"
PATTERN_0="*"

[TEMPLATE_07]
MONI_SET_NAME="SAP CCMS Monitor Templates"
MONI_NAME="Availability and Performance Overview"
PATTERN_0="*\Availability\*_BCE*\"

# Standard SAP Templates
[TEMPLATE_99]
VALUE=CHECK_SAP_SYSTEMS

[TEMPLATE_105]
MONI_SET_NAME="SAP CCMS Monitor Templates"
MONI_NAME="Dialog Overview"
PATTERN_0="*UsersLoggedIn"

[TEMPLATE_110]
MONI_SET_NAME="SAP CCMS Monitor Templates"
MONI_NAME="Entire System"
PATTERN_0="*EsAct"

[TEMPLATE_200]
MONI_SET_NAME="SAP CCMS Monitor Templates"
MONI_NAME="Database"
PATTERN_0="*Fullest tablespace"

[TEMPLATE_210]
MONI_SET_NAME="SAP CCMS Monitor Templates"
MONI_NAME="Entire System"
PATTERN_0="*DBRequestTime"

[TEMPLATE_300]
MONI_SET_NAME="SAP CCMS Monitor Templates"
MONI_NAME="Operating System"
PATTERN_0="*5minLoadAverage"

[TEMPLATE_999]
MONI_SET_NAME="SAP CCMS Monitor Templates"
MONI_NAME="Entire System"
PATTERN_0="*"

[TEMPLATE_060]
#DESCRIPTION="Free Swap"
MONI_SET_NAME=SAP CCMS Admin Workplace
MONI_NAME="Operating System"
#MAX_TREE_DEPTH=0
PATTERN_0="*\*\Swap_Space\Freespace"

[TEMPLATE_070]
DESCRIPTION=Dialog Response Time
MONI_SET_NAME=SAP CCMS Monitor Templates
MONI_NAME=Dialog Overview
PATTERN_0=P10\*\Dialog\ResponseTime

[TEMPLATE_071]
DESCRIPTION=Dialog Response Time
MONI_SET_NAME=SAP CCMS Monitor Templates
MONI_NAME=Dialog Overview
PATTERN_0=*

[TEMPLATE_09]
DESCRIPTION="DialogResponseTime"
MONI_SET_NAME=SAP CCMS Monitors for Optional Components
MONI_NAME="Logon Load Balancing"
MAX_TREE_DEPTH=0
PATTERN_0="*\*\Dialog\ResponseTime"

[TEMPLATE_007]
DESCRIPTION="TEST"
MONI_SET_NAME=SAP CCMS Monitors for Optional Components
MONI_NAME="Logon Load Balancing"
MAX_TREE_DEPTH=0
PATTERN_0="SAP\Server3_SAP_00\R3Services\Dialog\ResponseTime"


[TEMPLATE_900]
DESCRIPTION=Java
MONI_SET_NAME=SAP J2EE Monitor Templates
MONI_NAME=Heartbeat
PATTERN_0=*

[TEMPLATE_901]
DESCRIPTION=Java
MONI_SET_NAME=SAP J2EE Monitor Templates
MONI_NAME=Applications
PATTERN_0=*

[TEMPLATE_06]
DESCRIPTION=Users-Logged-On
MONI_SET_NAME="SAP CCMS Monitor Templates"
MONI_NAME="Dialog Overview"
MAX_TREE_DEPTH=0
PATTERN_0=SID\hostname*\Di*\Us*


[TEMPLATE_870]
DESCRIPTION=Java
MONI_SET_NAME=Test J2EE Monitor Set
MONI_NAME=J2EE Engine Kernel
PATTERN_0="XQ1\XQ1 64 Serv 649148450 mchp7tpa\System Threads Pool\MaximumThreadPoolSize"

[TEMPLATE_875]
DESCRIPTION=Java
MONI_SET_NAME=Test J2EE Monitor Set
MONI_NAME=J2EE Engine Kernel
PATTERN_0="XQ1\XQ1 64 Disp 649148400 mchp7tpa\General (MessageContext)\AverageMSProcessTime"


[TEMPLATE_666]
DESCRIPTION=SAP Avg. DB Request Time dehq0srm
MONI_SET_NAME=CENTRAL MONITORING SYSTEM (SAP Basis Kerpen)
MONI_NAME=Test Systems SAP
PATTERN_0=SID\hostname\Dialog\DBRequestTime

[TEMPLATE_667]
MONI_SET_NAME = "SAP CCMS Technical Expert Monitors"
MONI_NAME     = "All Contexts on Local Application Server"
PATTERN_0     = "*"

[TEMPLATE_668]
MONI_SET_NAME = "SAP CCMS Technical Expert Monitors"
MONI_NAME     = "All Contexts on Local Application Server"
PATTERN_0     = "*\SYSTEM\Free space"


[TEMPLATE_471]
MONI_SET_NAME = "SAP CCMS Technical Expert Monitors"
MONI_NAME     = "All Contexts on Local Application Server"
PATTERN_0     = "*\PSAPSR3USR\Free space"

# Monitorear Extended memory en application Server
[TEMPLATE_784]
MONI_SET_NAME="SAP CCMS Monitor Templates"
MONI_NAME="Performance Overview"
PATTERN_0="SID\SID3_00\*EsAct"

[TEMPLATE_785]
MONI_SET_NAME="SAP CCMS Monitor Templates"
MONI_NAME="Performance Overview"
PATTERN_0="SID\SID2_00\*EsAct"

[TEMPLATE_786]
MONI_SET_NAME="SAP CCMS Monitor Templates"
MONI_NAME="Performance Overview"
PATTERN_0="SID\SID1_00\*EsAct"

# Monitorear Extended memory en Central Instance
[TEMPLATE_787]
MONI_SET_NAME="SAP CCMS Monitor Templates"
MONI_NAME="Performance Overview"
PATTERN_0="SID\SID_00\*EsAct"

[TEMPLATE_666]
MONI_SET_NAME = "SID - Monitor"
MONI_NAME     = "All Monitoring Contexts"
PATTERN_0     = "*"

[TEMPLATE_670]
MONI_SET_NAME="SID - Monitor"
MONI_NAME="All Monitoring Contexts"
PATTERN_0="SID\*\*Oracle messages*\ORA*"
```

Si queremos agregar en CCMS el monitoreo de X jobs debemos seguir esta guia <http://help.sap.com/saphelp_nw04/helpdata/en/1c/48803d48de0610e10000000a114084/content.htm>

```ini>
Ejemplo de como podemos definir el comando en Nagios :
<code>
define command {
        command_name                    check_sap
        command_line                    $USER1$/ccms/check_sap $ARG1$ $_HOSTSAPID$
        register                        1
}       

define command {
        command_name                    check_sap_np
        command_line                    $USER1$/ccms/check_sap_np.sh $ARG1$ $_HOSTSAPID$
        register                        1
}       
```

Por ejemplo si la salida del chequeo en SAP nos arroja *pipes* **\|**, para que no tengamos problemas con un falso perfdata, podemos apelar a este script :

**check_sap_np.sh**

```bash>
Para poder hacer correctamente el chequeo deberemos defini la variable **$_HOSTSAPID$** dentro de la configuración del host :
<code>
_SAPID                     	PRD
```

**check_sap** version netways

Esta versión es mas actualizada que la anterior y tiene mejor mantenimiento. Deberemos tener el paquete nwrfcsdk, para proceder a su compilación

```
check_sap  -f   --host 10.1.1.200 --sysnr {numero_sistema} --client {instancia} --dest {SID} --user {usuario} --pass {contraseña}   'SID - Monitor' 'All Monitoring Contexts' 'System Configuration/Concurrent Users' --trim 4
```

#### Referencias

<https://www.netways.org/projects/check-sap/wiki>
