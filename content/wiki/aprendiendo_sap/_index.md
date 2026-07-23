---
title: "Aprendiendo SAP"
date: 2018-08-09T15:43:33-03:00
---

En este apartado voy a ir poniendo mi aprendizaje personal con SAP.

## ¿Que es SAP?

**SAP** son las siglas de *Systems, Applications, Products in Data Processing*. Es un sistema informático basado en módulos integrados, que abarca prácticamente todos los aspectos de la administración empresarial y que se adapta a todas las necesidades de una empresa. Está creado para abarcar todos los sectores del negocio. El lenguaje de programación que usa SAP es el ABAP y es muy parecido al SQL.

## ¿Qué son los módulos SAP?

El sistema SAP está compuesto de una serie de áreas funcionales o módulos, que responden de forma completa, y en tiempo real, a los procesos operativos de las compañías. Aunque pueden ser agrupados en tres grandes áreas (financiera, logística y de recursos humanos), funcionan de un modo integrado, dado que existe una conexión natural entre los diferentes procesos.

En el área financiera, el módulo FI proporciona una visión completa de las funciones contables y financieras, e incluye un amplio sistema de información y de generación de reportes para facilitar la toma de decisiones. El módulo CO (Controlling) se utiliza para representar la estructura de costos y los factores que tienen influencia, lo que genéricamente se conoce como contabilidad interna de las compañías. Otros módulos son IM (Gestión de inversiones) y TR (Tesorería).

En el área logística, el módulo LO brinda las herramientas e informes necesarios para analizar y gestionar el estado de la logística de la compañía y realizar previsiones en la cadena de suministros. El módulo MM (Gestión de materiales) comprende todas las actividades con la adquisición y control de los inventarios, en tanto PM (Mantenimiento) se encarga de los sistemas de control de plantas y PP (Planificación y de la producción) engloba las diferentes tareas y metodologías utilizadas en el proceso mismo de la producción, SD (Ventas y distribución) es el módulo comercial.

El módulo de Recursos Humanos (HR) incluye todos los procesos necesarios para controlar y gestionar de una manera eficaz las necesidades de recursos humanos de las empresas: desde la gestión de candidatos hasta la elaboración del desarrollo del personal, así como el control de tiempos. Sus componentes abarcan el soporte total para la administración de salarios y nóminas, modelos de turnos, planificación de trabajo, gestión de viajes, entre otros.

El módulo de Gestión de Relaciones con los Clientes nos sirve para administrar todos nuestros contactos con nuestros clientes mediante un registro de cualquier actividad que hubiéremos entablado con el mismo. Si el cliente nos llamó para consultarnos, esto queda registrado en la base de datos, si llamamos nosotros al cliente para ofrecerle un producto esto queda registrado, si a raíz de ese llamado el cliente realizó una compra esto también queda registrado.

- SAP ERP (Enterprise Resource Planning)
- SAP CRM (Customer Relationship Management)
- SAP SRM (Supplier Relationship Management)
- SAP PLM (Product Lifecycle Management)
- SAP SCM (Supply Chain Management)

Además de los módulos estrictamente funcionales existe una arquitectura técnica -denominada SAP Netweaver- con sus correspondientes módulos técnicos que se enumeran a continuación:

- People Integration
  - SAP MI (Mobile Infrastructure)
  - SAP EP (Enterprise Portal)
  - SAP KM (Knowledge Management)
- Information Integration
  - SAP BI (Business Intelligence)
  - SAP MDM (Master Data Management)
- Process Integration
  - SAP XI (Exchange Infrastructure)
  - SAP BPM (Business Process Management)
- Application platform
  - ABAP Stack
  - Java Stack
- Composite Application Framework
- Lifecycle Management
- Integración con MS .net
- Integración con IBM Websphere

### ERP

El sistema ERP está compuesto de una serie de módulos funcionales que responden de forma completa a los procesos operativos de las empresas.

- FI (Gestión de Finanzas - Contabilidad Financiera)
- CO (Control de Gestión - Contabilidad Análitica)
- PS (Gestión de proyectos)
- MM (Gestión de Materiales - Compras e Inventario)
- RE (Gestión de bienes Inmuebles)
- AA (Gestión de Activos fijos - Submódulo de FI)
- SL (Gestión de Promociones)
- IM (Gestión de Inversiones)
- SD (Gestión de ventas y distribución)
- QM (Gestión de calidad)
- PP (Gestión de producción)
- PM (Mantenimiento de Plantas industriales)
- HR (Gestión de RR.HH.)

**CA-Cross Application (Aplicación Cruzada, aplicable a todos los módulos de SAP)**

- CA-BPT Business Process Technology (Tecnología de Procesos de Negocios)
- CA-DM Document Management (Administración de documentos)
- CA-CL Classification (Clasificación)
- CA-CAD Integration (Integración)

**FI-Financial Accounting (Contabilidad financiera)**

La contabilidad financiera sigue unos requerimientos legales, ya que es la que se presenta a personas externas a la empresa.

- FI-GL General Ledger (Libro mayor)
- FI-AR Accounts Receivable (Cuentas a cobrar)
- FI-AP Accounts Payable (Cuentas a pagar)
- FI-SL Special Purpose Ledger (Libros mayores especiales)
- FI-AA Asset Accounting (Activos Fijos)
- FI-FM Funds Management (Gestión de fondos/liquidez)
- FI-TM Travel Management (Gastos de viaje)

**CO-Controlling (Contabilidad Analítica)**

El Controlling ayuda en la gestión de la empresa. Es por tanto de uso interno.

- CO-CCA Cost Center Accounting (Control por Centros de coste)
- CO-PCA Profit Center Accounting (Control por Centros de beneficio)
- CO-OM Overhead Cost Controlling (Control de Gastos Generales)
- CO-PC Product Cost Controlling (Control de Coste de Producción)–\> Integración con PP y SD
- CO-ABC Activity-Based Costing (Análisis de Costes basado en Actividades)
- CO-PA Profitability Analysis (Análisis de Rentabilidad)–\> Integración con PP y SD
- CO-PRO Project Control (Control de Proyectos) –\> Integración con PS

**IM-Capital Investment Management (Gestión de Inversión y su Capitalización)**

Permite el control y seguimiento de proyectos de inversión. Gestiona la planificación, presupuesto, contabilizaciones reales y capitalización de la inversión.

- IM-FA Tangible Fixed Assets (Activos Fijos Tangibles)
- IM-FI Financial Investments (Inversiones Financieras)

**EC-Enterprise Controlling (Control de Empresa)**

- EC-EIS Executive Information System (Sistema de Información a la Alta Dirección)
- EC-BP Business Planning (Planificación Empresarial)
- EC-CS Consolidation System (Consolidación de la Empresa)
- EC-PCA Profit Center Accounting (Contabilidad Centros de Beneficio)

**TR-Treasury. (Tesorería)**

- TR-TM Treasury Management (Administración de Tesoreria)
- TR-FM Funds Management (Administración de los Fondos)
- TR-CM Cash Management (Administración de Efectivo)

**PS-Project System (Sistema de Proyectos)**

- PS-BD Basic Data (Datos Básicos)
- PS-OS Operational Structures (Estructuras operacionales)
- PS-PLN Project Planning (Planificación de proyectos)
- PS-APP Approval (Aprobación)
- PS-EXE Project Execution / Integration (Ejecución / Integración de Proyectos)
- PS-IS Information System (Sistema de información)

**LO-Logistics General. Logística General**

- LO-LIS Logistic Information System (Sistema de información logística)
- LO-MD Master Data (Datos maestros)
- LO-PR Forecast (Previsiones)
- LO-VC Variant Configuration (Variante de configuración)
- LO-ECH Engineering Change Management (Administración de cambios de ingeniería)

**HR-Human Resources (Recursos Humanos)**

- HR-PD Personal Planning and Development (Planificación y Desarrollo de Personal)
- PD-OM Organizational Management (Administración Organizativa)
- PD-SCM Seminar and Convention Management (Administración de Seminarios y Convenciones)
- PD-PD Personnel Development (Desarrollo de Personal)
- PD-WFP Workforce Planning (Planificación de Personal)
- PD-RPL Room Reservations Planning (Planificación de Reservas de Salas)

**HR-PA Personnel Administration (Administración de Personal)**

- PA-EMP Employee Management (Administración de Personal)
- PA-BEN Benefits (Beneficios)
- PA-COM Compensation Administration (Administración de Compensaciones)
- PA-APP Applicant Management (Administración de candidatos)
- PA-TIM Time Management (Administración de Tiempos)
- PA-INW Incentive Wages (Incentivos Salariales)
- PA-TRV Travel Expenses (Gastos de Viaje)
- PA-PAY Payroll (Nómina)

## SAP Landscape

Ambientes que componen un sistema SAP

- **Desarrollo** donde acceden los consultores y desarrolladores. Este ambiente no posee información del trabajo diario de la organización.
- **Calidad** al que acceden los consultores de producto, consultores funcionales, y usuarios para probar el correcto funcionamiento del programa o funcionalidad configurada en el ambiente de desarrollo pero sin alterar los datos del día a día de la organización, con datos de prueba no críticos.
- **Producción** donde los consultores y desarrolladores no acceden, salvo en casos particulares y solo como visualización, y es en donde la organización posee sus datos operativos y al que acceden todos los usuarios finales del sistema.

<img src="/aprendiendo_sap/sap-landscape.jpg" class="align-center" alt="sap-landscape.jpg" />

## Implementando SAP

## Administrando SAP - Basis

Cuando hablamos de SAP Basis, nos referimos a la capa de base de las aplicaciones SAP, el consultor Basis se encarga de instalar la plataforma SAP y los módulos requeridos.

### Optimización del sistema SAP

#### GNU/Linux

Podemos ver la nota referida a configuración de páginas grandes memoria : [Hugepages](/notas/linux/manejo_de_memoria_en_linux/hugepages/). Si utilizamos VMware, posiblemente necesitemos tambien [cambiar el planificador de disco de la máquina virtual](/notas/linux/cambiar_planificador_de_disco/).

#### Acerca de SuSE LiNUX y SAP

<table>
<thead>
<tr>
<th>1999</th>
<th>First Linux certified for SAP software</th>
</tr>
</thead>
<tbody>
<tr>
<td>2006</td>
<td>First operating system certified for SAP Adaptive Computing Controller (ACC)<br />
Selected operating system for use with SAP BWA<br />
SAP’s Linux/UNIX software development platform</td>
</tr>
<tr>
<td>2007</td>
<td>First virtualization (Xen) validated for SAP<br />
First joint seamless and integrated Linux support through SAP Solution Manager</td>
</tr>
<tr>
<td>2008</td>
<td>Co-development of SUSE Installation Wizard for faster deployment of SAP and SUSE software<br />
Selected operating system for use with SAP Business ByDesign<br />
SAP Pinnacle Award 2008 Winner</td>
</tr>
<tr>
<td>2009</td>
<td>Selected operating system for use with SAP HANA<br />
SAP Pinnacle Award 2009 Finalist</td>
</tr>
<tr>
<td>2010</td>
<td>Four validated high availability best practices for SAP environment<br />
First Linux supported on Amazon Web Services</td>
</tr>
<tr>
<td>2011</td>
<td>Availability of SUSE Linux Enterprise Server for SAP Applications, the most optimized operating system for SAP workload with built-in high availability, faster deployment, page cache management, enterprise support services</td>
</tr>
<tr>
<td>2012</td>
<td>Distribution of ClamSAP (anti-virus connector to ClamAV) with SUSE Linux Enterprise Server for SAP Applications<br />
First Linux distributor to certify High Availability solution for integration with SAP NetWeaver</td>
</tr>
<tr>
<td>2013</td>
<td>SUSE ads new channel program for SAP Business One HANA</td>
</tr>
</tbody>
</table>

In summary, SUSE Linux Enterprise is:

- The leading platform for SAP solutions on Linux
- Selected by thousands of SAP customers
- The only Enterprise Linux optimized for SAP solutions
- SAP’s development platform for UNIX and Linux
- The recommended and supported OS for SAP HANA

##### Novedades de SuSE SP2 para SAP

- 3.0 Linux kernel: SP2 includes scheduler and memory management optimizations, support for transparent huge pages and per-CPU network load balancing. These features improve the performance of compute and I/O intensive workloads. SP2 supports the latest Intel\* Xeon and AMD\* Opteron processors, and exploits new hardware RAS features like CPU and memory offlining.
- Linux Containers: SP2 includes support for Linux Containers - highly efficient and low overhead OS virtualization. Also designed to work well with Xen\*,KVM, ESX and Hyper-V\*, SP2 offers the most extensive virtualization capabilities of any enterprise Linux distribution.

##### Referencias

<table>
<thead>
<tr>
<th>Notas KB SuSE</th>
</tr>
</thead>
<tbody>
<tr>
<td>Virtual SLES11SP1 shows bad IO performance when accessing raw disks<br />
<a href="https://www.suse.com/support/kb/doc.php?id=7009616">https://www.suse.com/support/kb/doc.php?id=7009616</a></td>
</tr>
<tr>
<td>Low Disk Space Conditions on SUSE<br />
<a href="https://www.suse.com/support/kb/doc.php?id=7002723">https://www.suse.com/support/kb/doc.php?id=7002723</a></td>
</tr>
<tr>
<td>CIFS mount does not return the proper file system rights<br />
<a href="https://www.suse.com/support/kb/doc.php?id=7010756">https://www.suse.com/support/kb/doc.php?id=7010756</a></td>
</tr>
<tr>
<td>Notas KB VMware</td>
</tr>
<tr>
<td>Configurar discos para utilizar adaptadores VMware Paravirtual SCSI (PVSCSI) (2033061)<br />
<a href="http://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&amp;externalId=2033061">http://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&amp;externalId=2033061</a></td>
</tr>
<tr>
<td>Setting the number of cores per CPU in a virtual machine (1010184)<br />
<a href="http://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&amp;externalId=1010184">http://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&amp;externalId=1010184</a></td>
</tr>
<tr>
<td>Notas SAP</td>
</tr>
<tr>
<td>1672954 - Oracle 11g: Usage of hugepages on Linux</td>
</tr>
<tr>
<td>1122388 - Linux: VMware vSphere configuration guidelines</td>
</tr>
<tr>
<td>Notas Oracle</td>
</tr>
<tr>
<td><a href="http://docs.oracle.com/cd/E37670_01/E37355/html/ol_config_hugepages.html">http://docs.oracle.com/cd/E37670_01/E37355/html/ol_config_hugepages.html</a></td>
</tr>
<tr>
<td><a href="https://metalink.oracle.com/metalink/plsqlf?p=130:14:2414882707157355558">https://metalink.oracle.com/metalink/plsqlf?p=130:14:2414882707157355558</a>::::p14_database_id,p14_docid,p14_show_header,p14_show_help,p14_black_frame,p14_font:NOT,401749.1,1,0,1,helvetica</td>
</tr>
</tbody>
</table>

| Notas técnicas no oficiales |
|----|
| <http://scn.sap.com/community/oracle/blog/2013/06/17/oracle-myths-and-common-misconceptions-about-transparent-huge-pages-for-oracle-databases-on-linux-uncovered> |

### Monitoreo del sistema SAP

#### Capas de análisis

Aspectos generales a tener en cuenta a la hora de implementar un monitoreo integral de un sistema SAP

##### Hardware

- Estado general físico del Hardware
  - Temperatura, FAN, chassis

##### Sistema Operativo

- Carga de procesamiento
- Estado de almacenamiento
- Servicios
- Procesos
- Logs de sistema

##### Base de datos

- Conexiones
- Espacio asignado
- Estado de Backup y respaldos
- Archivos de logs

##### SAP - Basis

- Central Instace
  - Application servers
- Procesos de diálogo
- Usuarios
- JOBs
- Dumps
- R3Syslog
- Message Server
- Spool

##### SAP - Aplicaciones

- Login
- Sistema de transportes
- Performance
  - Tiempo de respuesta
  - Carga CPU / Memoria
- Message Server

#### Checklist Diario

##### Transacciones generales

- SM50 (Process Overview)
- SM66 (Global process overview)
- SM51 (Application servers status)
- SM12 (Lock entry list)
- ST22 (ABAP Dumps)
- SM21 (System log)
- SM13 (Update Requests overview)
- ST02 (Tune summary)
- ST06 (System Load Analisys)
- ST11 (Display Trace Files)
- DB12 (Backup logs)
- DB13 (DBA Planning calender)
- DB14 (DBA operations log)
- SM37 (Job status overview)
- ST04 (Database alert logs and performance)
- SP01 (Check Spool status)
- DBO1 (Check for Database locks)
- SM58 (Asynchronous RFC Error Log)

##### ABAP Stack Checks

1.  Check process overview(SM50)
2.  Check overall system process overview(SM66)
3.  Check application servers status(SM51)
4.  Check for any pending locks (SM12)
5.  Check for Dumps in the system(ST22)
6.  Check System log for any errors(SM21)
7.  Check for any hanged updates or update status(SM13)
8.  Check for excessive swapping (ST02)
9.  Check for critical job status like backup,updatestats,checkdb etc(DB13)
10. Check for longrunning/failed jobs status(SM37)
11. Check database alertlogs and performance(ST04)
12. Check spool job status (SP01)
13. Check cache status (sxi_cache) for PI System
14. Check SLD functionality(SLDCHECK)
15. Check SXI_MONITOR for PI system
16. Check for Database locks(DB01)

##### Java Stack Checks

1.  Check java portal accessibility using link
2.  Check server0 log for java system for critical errors
3.  Check accessibility of management console
4.  Check server node status
5.  Check default trace for critical java errors
6.  Check java reports for memoryconsumption/swapping Os level checks
7.  Check filesystems usage (shouldb be \<80%)
8.  Check for swap space using topas etc
9.  Check for work directory log files at oslevel for errors

#### Computing Center Management System - CCMS

Algunas cosas que podemos monitorear con el **CCMS** de **SAP** :

- Dialog Metrics
  - Current Dialog Logged On Users
  - Database Request Time
  - Dialog Steps Per Minute
  - Dialog User Response Time
  - Load And Generate Time
  - Queue Time
- SAP Memory Usage
  - Extended Memory Utilization
  - Heap Memory Utilization
  - Page Memory Utilization
  - Roll Memory Utilization
- Work Processes
  - Batch Work Processes
  - Dialog Work Processes
  - Total Work Processes
- Spool

#### Nagios SAP CCMS

Para poder ver algunas cosas de monitoreo de SAP desde Nagios por medio de CCMS podemos referirnos a los [tips de Nagios](/manuales/nagios/#tip-s-varios).

### Permitir uso del usuario SAP\*

Uso permitido del usuario SAP\* para tareas de configuración: en todas las instalaciones Sap existe un usuario SAP\* (mientras no este creado como tal a propósito), que tiene la contraseña por defecto PASS y todas las autorizaciones disponibles (SAP_ALL). Esto es un agujero de seguridad grave que Sap soluciono con el parámetro login/no_automatic_user_sapstar. Por defecto, el parámetro vale 1 e impide utilizar este usuario. Para poder activarlo, habrá que poner el valor 0. Lo lógico es activarlo para tareas de configuración, y una vez terminado el proceso, volver a desactivarlo. Este usuario es HARDCODED.

### Ordenes de Transporte

Las Órdenes de Transporte se utilizan para implementar modificaciones del ambiente de desarrollo DEV a los sistemas de calidad QAS y productivos PRD. También podemos implementar modificaciones externas generadas en otros landscapes de sistemas SAP.

Para acceder al sistema de transportes de SAP, deberemos entrar al sistema SAP que necesitamos y ejecutar la transacción **STMS**.

#### Importar una Orden de Transporte desde un sistema externo

Se puede dar el caso de que nos envíen ordenes de transporte de un desarrollo externo para importarlas en nuestro sistema de desarrollo DEV.

La convención standard de SAP para estos archivos es R\<6 digitos\>.\<sistema fuente\> y K\<6 digitos\>.\<sistema fuente\> (El sistema fuente es el nombre del sistema del cual proviene el transporte)

- **K** Type Transport = Cofile – Normalmente 1 – 3 KB
- **R** Type Transport = Datafile – Normalmente mas grande que el Colfile

K902506.SAP es un Cofile – no contiene muchos datos, son los atributos del archivo data. Clases de objeto, comandos o tipo de información.

R902506.SAP es el Datafile – Contiene los datos del Transporte – Son los cambios que van a ser aplicados al sistema.

Deberemos ingresar al sistema de desarrollo en el cual queremos importar los archivos dentro del directorio /usr/sap/tran o D:\usr\sap\trans (según el sistema operativo)

- Deberemos copiar el archivo Cofile K
  - /usr/sap/trans/cofiles/
  - D:\usr\sap\trans\cofile
- Deberemos copiar el archivo Data R
  - /usr/sap/trans/data/
  - D:\usr\sap\trans\data

Dentro del la transacción STMS deberemos ingresar a la opción (dependiendo del idioma)

- Extras –\> Other requests
  - Detalles –\> Otros

Agregar el transporte ej SAPK902506 Donde DEV es la extension del archivo y el resto es el nombre del archivo.

#### SE10 - Transport Organizer

Mediante estra transacción podemos transportar una orden de un ambiente a otro y ver su histórico

### Componentes del sistema SAP

#### SAP Gateway

**SAP NetWeaver Gateway** es una tecnología que proporciona una forma sencilla de conectar dispositivos, entornos y plataformas de software de SAP en base a los estándares del mercado. Permite el desarrollo de soluciones en cualquier interfaz de usuario llevando el entorno SAP a otros entornos como los sociales y de de colaboración, dispositivos móviles y tabletas y aplicaciones de Internet. Ofrece conectividad a aplicaciones SAP utilizando cualquier lenguaje de programación o modelo, sin necesidad de conocimientos de SAP mediante el aprovechamiento de los servicios REST y protocolos OData / ATOM.

##### Notas principales que afectan el servicio

Nota de seguridad de conexiones

- 1444282
- Subnotas que están afectadas por la aplicación de la nota anterior :
  - 74141
  - 384971

## Base de datos HANA

Apartado : [SAP HANA](/aprendiendo_sap/sap_hana/)

## Base de datos Oracle

- [Apartado de notas de Oracle](/notas/bases_de_datos/oracle/)

### Manejo de Backup y Restores

Hay un apartado acerca de la configuración de backups online de SAP en el [manual de administración de backups con Tivoli TSM](/manuales/tivoli_tsm/manual_de_manejo_de_backups_con_tivoli/#configuracion-de-nodo-tdp-para-backup-online-de-sap), donde se indica la configuración necesaria a implementar.

#### Backup de datos

```
brbackup -u / -c force -t online -m all -p initSID.sap -a -c force -p initSID.sap -sd
```

#### Restore de datos

Examples of BRRESTORE Runs Locate the document in its SAP Library structure

- brrestore -b last -m all
  - Restore all tablespaces without the control file and online redo log files from the last successful backup.

<!-- -->

- brrestore -b bcnmhluz.aft -m full
  - Restore all the files from backup bcnmhluz.aft, including the control file and the online redo log files. Restore the mirror copies of the control file and the online redo log files.

<!-- -->

- brrestore -m /usr/sap/C11/SYS/profile
  - Restore the SAP profiles.

<!-- -->

- brrestore -m /oracle/C11/sapdata1=/oracle/C11/sapdata5
  - Restore all the database data files that were originally stored in the subdirectories of /oracle/C11/sapdata1 in directory /oracle/C11/sapdata5.

<!-- -->

- brrestore -b last -m 1-10,01-04,0
  - Restore all the database data files with Oracle file IDs from 1 through 10, the four online redo log files, and the control file starting from the last successful backup. Restore the mirror copies of the control file and the online redo log files.

<!-- -->

- brrestore -m 0
  - Restore the control file. Restore the mirror copies of the control file.

<!-- -->

- brrestore -b last -m /oracle/C11/sapdata2/ddicd_5/ddicd.data5
  - Restore a database data file starting from the last successful backup.

<!-- -->

- brrestore -a 200-220
  - Restore the archived redo log files with the log sequence numbers from 200 through 220 into the archiving directory.

<!-- -->

- brrestore -a 40-70=/oracle/C11/sapbackup 71-90=/oracle/C11/sapreorg
  - Restore the archived redo log files with the log sequence numbers from 40 through 70 in directory sapbackup and those with the log sequence numbers from 71 through 90 in directory /oracle/C11/sapreorg.

<!-- -->

- brrestore -a 40-69,70-100=/oracle/C11/sapbackup
  - Restore the archived redo log files with the log sequence numbers from 40 through 69 in the archiving directory, and those with the log sequence numbers from 70 through 100 in directory sapbackup.

<!-- -->

- brrestore -n det_log
  - Restore a detail log to the local working directory.

### Chequeando consistecia

- brconnect -u / -c -f check

### Estadisticas

- brconnect -u / -c -f stats -t system_stats
- brconnect -u / -c -f stats -t oradict_stats

Update the Oracle database statistics with BRCONNECT as follows:

- brconnect -u / -c -f stats -t all -f collect -p 4

### Administrando Oracle

##### Archivos de LOGS

Hay un script [script filtrador de Logs](/manuales/filtrador_de_logs/) para parsear los resultados del alert.log de Oracle y enviarlos por email.

**alert.log**

```
$ORACLE_BASE/admin/$ORACLE_SID/bdump/alert_$ORACLE_SID.log
```

```
$ORACLE_BASE/admin/$ORACLE_SID/background/alert_$ORACLE_SID.log
```

```
$ORACLE_BASE/saptrace/background/alert_$ORACLE_SID.log
```

**listener.log**

```
$ORACLE_BASE/network/log/listener.log
```

##### Modificación de parámetros Oracle

```sql>
<code sql>
SQL> SHOW parameters log_archive_max_processes;
log_archive_max_processes integer 4
 
SQL> ALTER system SET log_archive_max_processes=1;
System altered.
 
SQL> ALTER system SET log_archive_max_processes=4;
System altered.
```

```sql>
 show parameter db_writer_processes
NAME                                 TYPE        VALUE
------------------------------------ ----------- -------
db_writer_processes                  integer     1

SQL> alter system set db_writer_processes=2 scope=spfile sid='*';

System altered.
```

si nos da un error como este :

```sql>
 ALTER system SET db_writer_processes=2 scope=spfile sid='*';
ALTER system SET db_writer_processes=2 scope=spfile sid='*'
*
ERROR at line 1:
ORA-32001: write to SPFILE requested but no SPFILE specified at startup
```

```sql>
 show parameter spfile 

NAME				     TYPE	 VALUE
------------------------------------ ----------- ------------------------------
spfile				     string

SQL> create spfile from pfile;

File created.

SQL> 

SQL> shutdown immediate

SQL> startup

SQL> show parameter spfile 

NAME				     TYPE	 VALUE
------------------------------------ ----------- ------------------------------
spfile				     string	 /oracle/SID/102_64/dbs/spfileSID.ora

SQL> ALTER system SET db_writer_processes=2 scope=spfile sid='*';
```

```sql>
 show parameter block_size;

NAME				     TYPE	 VALUE
------------------------------------ ----------- ------------------------------
db_block_size			     integer	 8192
SQL> 
```

##### Indices de la base Oracle de SAP exportados en CSV

```sql
set serveroutput on size unlimited 
declare 
texto            varchar2(2000); 
indice_anterior  varchar2(30) := 'XXXXXXXXXX'; 
begin 
DBMS_OUTPUT.ENABLE (buffer_size => NULL); 
 for c1 in (select table_name,index_name,column_name from dba_ind_columns where index_owner = 'SAPSR3' and index_name 
             not like '%~0' order by table_name,index_name,column_position) loop 
      if c1.index_name <> indice_anterior then 
          if indice_anterior = 'XXXXXXXXXX' then 
              indice_anterior := c1.index_name; 
              texto := c1.table_name||';'||c1.index_name||';'||c1.column_name||';'; 
          else 
              dbms_output.put_line(texto); 
              texto := c1.table_name||';'||c1.index_name||';'||c1.column_name||';'; 
              indice_anterior := c1.index_name; 
          end if; 
      else 
          texto := texto||c1.column_name||';'; 
      end if; 
   end loop; 
 dbms_output.put_line(texto); 
end; 
/ 
```

##### Status de las cuentas de usuario

```sql>
== Desbloquear/Bloquear cuenta de usuario ==
<code sql>
alter user SAPSR3 account lock;
```

```sql>
<code sql>
select count(*) from SAPSR3.SNAP  where ROWNUM <= 5;
```

## Administrando SAP - Funcional

El área funcional de SAP es la que se encarga de parametrizar el sistema SAP instalado de acuerdo a lo que se requiera. Igualmente vamos a separar en otro apartado lo que se refiere a [desarrollo y programación en ABAP](/aprendiendo_sap/desarrollo y programación en ABAP)

### Apertura y cierre de mandante

### Administración de JOBS

**TBTCO** Tabla de resumen del estado de jobs

**TBTCP** Resumen step de un job de fondo

#### Creación de JOBS de Fondo

Un JOB o proceso de fondo en SAP se compone de estas propiedades principales :

- Nombre
- Usuario de ejecución
- Programa
- Variante de ejecución
- Fecha y hora
- Valores de periodicidad

Para comenzar a la creación de nuestro proceso de fondo, deremos utilizar la **Trx. SM36**.

## Utilizando SAP

#### Como conectar el SAPgui desde Linux

- Requerimientos del sistema
  - Linux
  - Hardware: CPU: Intel PentiumIII with 800 MHz
  - RAM: 256MB
  - Software:
  - OS: SuSE Linux 9.3 - 10.1, Red Hat Enterprise Linux 3 and 4
  - JVM: Sun Java Runtime Evironment 1.4.2 (32-bit) or 5.0 (32-bit) including Java Plugin

<!-- -->

- Descargar SAPGUI de:
  - <ftp://ftp.sap.com/pub/sapgui/java/> (anonymous login)
  - <https://websmp201.sap-ag.de/sapgui-java> (OSS user and pass)

<!-- -->

- En el directorio /java desde el ftp seleccionar:
  - PlatinGUI-Linux-700.jar 37926 KB 11/07/06 13:58:00 (a la versión mas nueva disponible)
  - En el directorio de descarga ejecutar

```
java -jar PlatinGUI-Linux-700.jar
```

- Ejecutando SAPgui
  - Guilogon:

```
~/SAPClients/SAPGUI7.00/bin$ ./guilogon
To create servers, touch at new and go to "advanced".
```

- “expert configuration”

Ejemplo:

Para el servidor 10.1.1.6 con el system number 01:

```
conn=/H/10.1.1.6/S/3201
```

Command line

Para el servidor 10.1.1.6 conel system number 01:

```
~/SAPClients/SAPGUI7.00/bin$ ./sapgui /H/10.1.1.6/S/3201
```

##### saplogon.ini en Windows

El archivo se guarda en **C:\Users\USUARIO\AppData\Roaming\SAP\Common**

##### saplogon.ini en Linux

**/home/usuario/.SAPGUI/connections**

Ejemplo sin logon balance

```
SID:conn=/M/190.1.1.19/S/3600/G/Empresa&expert=true
```

Ejemplo con logon balance

```
SID [Empresa]:conn=/M/190.1.1.19/S/3600/G/Empresa&expert=true
```

#### Script para obtener datos del sistema

SAP Note 618104 - “sapsysinfo” - Compiling system information on Linux

## Actualización de servidor SAP

### Actualización del Kernel de SAP

Para realizar esta tarea sea el sistema operativo que fuere, debemos tener todo el sistema SAP R3 bajo y en lo posible para evitar complicaciones la base de datos también.

#### Instrucciones para actualizar el Kernel de SAP en Windows

#### Instrucciones para actualizar el Kernel de SAP en Linux

Ejemplo del Kernel de SAP 701 al Kernel de SAP 720_REL

Primero deberemor limpiar el directorio exe donde estan los binarios principales de SAP

```
rm -rf /sapmnt/<SID>/exe
cd /sapmnt/<SID>/exe
```

Luego dentro de ese directorio deberemos descomprimir los paquetes SAR de SAP

```
SAPCAR -xvf SAPEXEDB_100-10011223.SAR 
SAPCAR -xvf SAPEXE_100-10011224.SAR 
SAPCAR -xvf DBATL720O10_33-10007263.SAR
```

Luego de eso deberemos ejecutar la orden [sapcpe](http://help.sap.com/saphelp_nw04/helpdata/en/4f/37783021fc11d288cd0000e83539c3/content.htm) para que SAP tome las configuraciones del Kernel nuevo :

Asignamos permisos de sap

```
chown -R <SID>adm:sapsys /sapmnt/<SID>/exe
```

```
sapcpe pf=/usr/sap/<SID>/SYS/profile/<SID>_DVEBMGS00_<hostname>
sapcpe pf=/usr/sap/<SID>/SYS/profile/START_DVEBMGS00_<hostname>
```

Y luego como usuario **root** ejecutamos el script [saproot.sh](http://help.sap.com/saphelp_nw2004s/helpdata/en/43/3753c4b87b6025e10000000a422035/content.htm) :

```
cd /sapmnt/<SID>/exe
./oraroot.sh <SID>
./saproot.sh <SID>
```

#### Post Actualización

Para verificar errores o ver mas info de debug deberemos ejecutar :

```
R3trans -d
disp+work -V
```

Luego de la actualización del Kernel recordemos que las transacciones en SAP tardaran bastante en ejecutar ya que deberan ser compiladas para una primera ejecución.

### Administración del motor de Java de PORTAL

#### Panel de administración de procesos

```
/usr/sap/<SID>/<INSTANCIA>/j2ee/os_libs/# jcmon pf=/usr/sap/<SID>/SYS/profile/<SID>_<INSTANCIA>_<hostname>
```

#### Aplicar un deploy

Dentro de /usr/sap/\<SID\>/\<INSTANCIA\>/SDM/program/inBox, colocar los archivos (usualmente \*.ear). Luego ejecutar /usr/sap/\<SID\>/\<INSTANCIA\>/SDM/program/RemoteGui.sh.

Seleccionar : Deployment-\>Carpeta SCAs/SDAs for Deployment-\>(botón derecho)-\>Add SCA/SDA(Server Side File Browser)-\>Elegir paquete-\>Proceder Next-\>Start deploy-\>Confirm

#### Reset de contraseña SDM

Si perdemos la contraseña la podemos resetear de la siguiente forma

Debemos bajar el servicio SDM y ejecutar

/usr/sap/\<SID\>/\<INSTANCIA\>/SDM/program/inBox/sdm.sh changepassword “sdmhome=/usr/sap/\<SID\>/\<INSTANCIA\>/SDM/program” “newpassword=Password123”

### Actualización del Sistema Operativo de SAP

En nuestro caso, si por ejemplo usamos SuSE Linux como Sistema Operativo para la implementación de SAP, [podemos seguir este simple procedimiento para su actualización](/notas/linux/actualizar_suse_linux/).

#### Notas SAP

- **171356** - SAP Software on Linux: General information
- **1984787** - SUSE LINUX Enterprise Server 12: Installation notes

### Export / Import de Tablas de SAP

Ejemplo Destinos RFC

R3trans -w exp_rfc.log rfc_export

**rfc_export**

```
Export
File=rfc_dump
Select * from rfcattrib
Select * from rfcdes
Select * from rfcdoc
```

R3trans -w imp_rfc.log rfc_import

**rfc_import**

```
import
file = 'rfc_dump'
client = 200
```

## Renombrar un sistema SAP

En algunas ocasiones, debemos modificar un sistema SAP, ya sea por una separación o modificación del negocio que lo utiliza etc.

### Relevamiento

Antes de migrar el sistema, deberemos verificar

- Conexiones RFC
- Conexiones a base de datos remotas
  - DBCO
  - /sapmnt/\<SID\>/profile/oracle/tnsnames.ora

### Modificaciones

#### Identificador de Base de Datos Oracle

Algunas formas de recuperación requieren que la base de datos se inicie y acceda como montada. Para poner la base de datos en modo de montaje, deberemos inicia de la siguiente manera :

```
SQL> startup mount
```

Si ya se ha iniciado la instancia de la base de datos con la opción de inicio nomount, se puede cambiar el nomount para montar la base utilizando el comando alter database :

```
SQL> alter database mount;
```

DBNEWID es una utilidad de la base de datos para poder cambiar el identificador interno de dicha base de datos (DBID) y el nombre de base de datos (DBNAME) para una base de datos operativa.

```
# nid target=/ setname=yes dbname=<NEWDBID>

DBNEWID: Release 12.1.0.2.0 - Production on Tue May 29 14:50:57 2018

Copyright (c) 1982, 2014, Oracle and/or its affiliates.  All rights reserved.

Connected to database <OLDDBID> (DBID=497546318)

Connected to server version 12.1.0

Control Files in database:
    /oracle/<OLDDBID>/origlogA/cntrl/cntrl<OLDDBID>.dbf
    /oracle/<OLDDBID>/origlogB/cntrl/cntrl<OLDDBID>.dbf
    /oracle/<OLDDBID>/sapdata1/cntrl/cntrl<OLDDBID>.dbf

Change database name of database <OLBDBID> to <NEWDBID>? (Y/[N]) => Y

Proceeding with operation
Changing database name from <OLDDBID> to <NEWDBID>
    Control File /oracle/<OLDDBID>/origlogA/cntrl/cntrl<OLDDBID>.dbf - modified
    Control File /oracle/<OLDDBID>/origlogB/cntrl/cntrl<OLDDBID>.dbf - modified
    Control File /oracle/<OLDDBID>/sapdata1/cntrl/cntrl<OLDDBID>.dbf - modified
    Datafile /oracle/<OLDDBID>/sapdata1/system_1/system.data - wrote new name
    Datafile /oracle/<OLDDBID>/sapdata7/undo_1/undo.data - wrote new name
    Datafile /oracle/<OLDDBID>/sapdata1/sysaux_1/sysaux.data - wrote new name
...
```

```
# orapwd file=$ORACLE_HOME/dbs/orapw<SID> password=<PASSWORD>
```

```sql>
 STARTUP NOMOUNT
	
SQL> CREATE SPFILE='/oracle/<NEWSID>/12102/dbs/spfile<NEWSID>.ora' FROM 	PFILE='/oracle/<NEWSID>/12102/dbs/init<NEWSID>.ora';

File created.

SQL> ALTER SYSTEM SET DB_NAME=<NEWSID> SCOPE=spfile;

System altered.

SQL> startup open

SQL> alter database rename global_name to <NEWSID>.WORLD;

SQL> update global_name set global_name = '<NEWSID>';

SQL> commit ;

SQL> STARTUP NOMOUNT

SQL> ALTER SYSTEM SET DB_NAME=<NEWSID> SCOPE=spfile;

System altered.

SQL> alter system set instance_name=<NEWSID> scope=spfile;

SQL> startup

SQL> create pfile from spfile;

File created.
```

##### Iniciando la Base de Datos

```
SQL> startup
ORA-32004: obsolete or deprecated parameter(s) specified for RDBMS instance
ORACLE instance started.

Total System Global Area 6.8719E+10 bytes
Fixed Size		    5304248 bytes
Variable Size		 2.8857E+10 bytes
Database Buffers	 3.9728E+10 bytes
Redo Buffers		  128913408 bytes
ORA-00205: error in identifying control file, check alert log for more info
```

alert\_\<NEWSID\>.log

```
ALTER DATABASE MOUNT
Tue May 29 15:27:57 2018
ORA-00210: cannot open the specified control file
ORA-00202: control file: '/oracle/<NEWSID>/sapdata1/cntrl/cntrl<NEWSID>.dbf'
ORA-27037: unable to obtain file status
Linux-x86_64 Error: 2: No such file or directory
Additional information: 3
ORA-00210: cannot open the specified control file
ORA-00202: control file: '/oracle/<NEWSID>/origlogB/cntrl/cntrl<NEWSID>.dbf'
ORA-27037: unable to obtain file status
Linux-x86_64 Error: 2: No such file or directory
Additional information: 3
ORA-00210: cannot open the specified control file
ORA-00202: control file: '/oracle/<NEWSID>/origlogA/cntrl/cntrl<NEWSID>.dbf'
ORA-27037: unable to obtain file status
Linux-x86_64 Error: 2: No such file or directory
Additional information: 3
```

Renombramos los control file de cntrl\<OLDSID\>.dbf hacia cntrl\<NEWSID\>.dbf y luego iniciamos la base de datos

#### Conectividad interna

Deberemos modificar el nombre del host de nuestro servidor, para que se adapte al **SID** que necesitamos y también deberemos ajustar su resolución local en el archivo */etc/hosts*.

También deberemos agregar el servicio en el archivos */etc/services*

```
sapms<SID>	3600/tcp	# SAP System Message Server Port
```

#### Perfiles de usuarios de sistema

En el HOME del usuario administrador de SAP y del HOME del usuario administrador de la base de datos (en nuestro caso Oracle), usualmente dicho usuario tiene la siguiente nomenclatura :

- Administrador SAP : \<SID\>adm
- Administrador Oracle : ora\<SID\>

Estos archivos suelen encontrarse con el mismo nombre en los directorios HOME de dichos usuarios

Archivos con variables de entorno de la shell del usuario :

- **.cshrc**

Archivos con variables de entorno para la ejecución de la base de datos :

- **.dbenv.csh**
- **.dbenv.sh**
- **.dbenv\_\<HOSTNAME\>.csh**
- **.dbenv\_\<HOSTNAME\>.sh**

Archivos con variables de entorno del motor JAVA de SAP :

- **.j2eeenv.csh**
- **.j2eeenv.sh**
- **.j2eeenv\_\<HOSTNAME\>.csh**
- **.j2eeenv\_\<HOSTNAME\>.sh**

Archivos con variables de entorno para la ejecución de SAP :

- **.sapenv.csh**
- **.sapenv.sh**
- **.sapenv\_\<HOSTNAME\>.csh**
- **.sapenv\_\<HOSTNAME\>.sh**
- **.sapsrc.csh**
- **.sapsrc.sh**
- **.sapsrc\_\<HOSTNAME\>.csh**
- **.sapsrc\_\<HOSTNAME\>.sh**

Perfiles de SAP : /sapmnt/\<SID\>/profile

- \<SID\>\_DVEBMGS00\_\<HOSTNAME\>
- DEFAULT.PFL
- START_DVEBMGS00\_\<HOSTNAME\>

Perfiles de Oracle para SAP : /sapmnt/\<SID\>/profile/oracle

- sqlnet.ora
- tnsnames.ora

Configuración del Listener :

- /oracle/\<SID\>/12102/network/admin

Variables de configuración de Oracle para SAP :

- /oracle/\<SID\>/12102/dbs

Podemos hacer un cambio brusco y rápido de los valores de configuración del viejo \<SID\> hacia el nuevo \<SID\> :

```
# sed -i -e 's/<SIDVIEJO>/<SIDNUEVO>/g' *
# sed -i -e 's/<sidviejo>/<sidnuevo>/g' *
```

#### Iniciando SAP

Posteriormente a modificar la configuración de los prefiles de Oracle, iniciamos el Listener

```
# lsnrctl start

LSNRCTL for Linux: Version 12.1.0.2.0 - Production on 29-MAY-2018 15:31:47

Copyright (c) 1991, 2014, Oracle.  All rights reserved.

Starting /oracle/<NEWSID>/12102/bin/tnslsnr: please wait...

TNSLSNR for Linux: Version 12.1.0.2.0 - Production
System parameter file is /oracle/<NEWSID>/12102/network/admin/listener.ora
Log messages written to /oracle/<NEWSID>/saptrace/diag/tnslsnr/<NEWHOSTNAME>/listener/alert/log.xml
Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=<NEWSID>.WORLD)))
Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=<NEWSID>)))
Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=<NEWFQDN>)(PORT=1527)))

Connecting to (ADDRESS=(PROTOCOL=IPC)(KEY=<NEWSID>.WORLD))
STATUS of the LISTENER
------------------------
Alias                     LISTENER
Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
Start Date                29-MAY-2018 15:31:50
Uptime                    0 days 0 hr. 0 min. 0 sec
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Listener Parameter File   /oracle/<NEWSID>/12102/network/admin/listener.ora
Listener Log File         /oracle/<NEWSID>/saptrace/diag/tnslsnr/<NEWHOSTNAME>/listener/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=<NEWSID>.WORLD)))
  (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=<NEWSID>)))
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=<NEWFQDN>)(PORT=1527)))
Services Summary...
Service "<NEWSID>" has 1 instance(s).
  Instance "<NEWSID>", status UNKNOWN, has 1 handler(s) for this service...
The command completed successfully
```

Revisar /usr/sap y mover el directorio de \<OLDSID\> hacia \<NEWSID\>

Modificar los enlaces hacia los ejecutables de SAP en el directorio /usr/sap/\<NEWSID\>/DVEBMGS00/work

- co.sap\<OLDSID\>\_DVEBMGS00 -\> /usr/sap/\<OLDSID\>/DVEBMGS00/exe/rslgcoll
- dw.sap\<OLDSID\>\_DVEBMGS00 -\> /usr/sap/\<OLDSID\>/DVEBMGS00/exe/disp+work
- ig.sap\<OLDSID\>\_DVEBMGS00 -\> /usr/sap/\<OLDSID\>/DVEBMGS00/exe/igswd_mt
- ms.sap\<OLDSID\>\_DVEBMGS00 -\> /usr/sap/\<OLDSID\>/DVEBMGS00/exe/msg_server
- se.sap\<OLDSID\>\_DVEBMGS00 -\> /usr/sap/\<OLDSID\>/DVEBMGS00/exe/rslgsend

De la siguiente forma :

```
ln -s /usr/sap/<NEWSID>/DVEBMGS00/exe/rslgcoll 		co.sap<NEWSID>_DVEBMGS00 
ln -s /usr/sap/<NEWSID>/DVEBMGS00/exe/disp+work 	dw.sap<NEWSID>_DVEBMGS00
ln -s /usr/sap/<NEWSID>/DVEBMGS00/exe/igswd_mt 		ig.sap<NEWSID>_DVEBMGS00
ln -s /usr/sap/<NEWSID>/DVEBMGS00/exe/msg_server 	ms.sap<NEWSID>_DVEBMGS00
ln -s /usr/sap/<NEWSID>/DVEBMGS00/exe/rslgsend 		se.sap<NEWSID>_DVEBMGS00
```

Modificar los enlaces global y profile /usr/sap/\<NEWSID\>/SYS :

- global -\> /sapmnt/\<OLDSID\>/global
- profile -\> /sapmnt/\<OLDSID\>/profile

Como :

```
ln -s /sapmnt/<NEWSID>/global
ln -s /sapmnt/<NEWSID>/profile
```

Modificar los enlaces /usr/sap/\<NEWSID\>/SYS/exe :

- dbg -\> /sapmnt/\<OLDSID\>/exe
- nuc -\> /sapmnt/\<OLDSID\>/exe/nuc
- run -\> /usr/sap/\<OLDSID\>/SYS/exe/dbg
- uc -\> /sapmnt/\<OLDSID\>/exe/uc

Como :

```
ln -s /sapmnt/<NEWSID>/exe dbg
ln -s /sapmnt/<NEWSID>/exe/nuc
ln -s /usr/sap/<NEWSID>/SYS/exe/dbg run
ln -s /sapmnt/<NEWSID>/exe/uc
```

Revisar la ruta de SSFS (Secure Storage in File System) :

```
RSEC_SSFS_DATAPATH=/usr/sap/<NEWSID>/SYS/global/security/rsecssfs/data
RSEC_SSFS_KEYPATH=/usr/sap/<NEWSID>/SYS/global/security/rsecssfs/key
```

Renombrar SSFS\_\<OLDSID\>.DAT como SSFS\_\<NEWSID\>.DAT

Reset de usuario SAP\* :

```sql>
 select UFLAG, BNAME, MANDT from SAPSR3.USR02 where BNAME='SAP*';

SQL> delete from SAPSR3.USR02 where BNAME='SAP*' and MANDT=000;
```

SAP Profile \<NEWSID\>\_DVEBMGS00\_\<NEWHOSTNAME\> :

```
login/no_automatic_user_sapstar=0
```

```
# startsap
Checking <NEWSID> Database
Database is running
-------------------------------------------
Starting Startup Agent sapstartsrv
OK
Instance Service on host <NEWHOSTNAME> started
-------------------------------------------
starting SAP Instance DVEBMGS00
Startup-Log is written to /home/<NEWSID>adm/startsap_DVEBMGS00.log
-------------------------------------------
/usr/sap/<NEWSID>/DVEBMGS00/exe/sapcontrol -prot NI_HTTP -nr 00 -function Start
Instance on host <NEWHOSTNAME> started
```

Luego dentro de SAP, deberemos cambiar el nombre lógico por medio de la **Trx. BDLS** o del programa **RBDLS2LS**. Luego rearmamos el landscape de SAP con los equipos de DEV y QA. Para después rearmar sistema de transporte.

## Clonación de un sistema SAP

## Problemas comunes

#### Analizando problemas de ejecución poco descriptivos

Muchas veces en SAP nos topamos con problemas poco descriptivos, para analizarlos deberemos analizar principalmente estos achivos de log dentro del directorio work :

Windows -\> **\<Drive\>:\usr\sap\\Sys-ID\>\DVEBMGS\<Sys-Nr\>\Work**

Linux -\> **/usr/sap/Sys-ID/DVEBMGS\<Sys-Nr\>/work**

- **dev_disp** Dispatcher developer trace
- **dev_ms** Message Server developer trace
- **dev_w0** Work process 0 developer trace. Contains information about the **latest ABAP work process**. But it can happen at time that the other work processes may fail or create errors. If that is the case , one has to identify the work process by its number and open the log file and examine the issue
- **dev_rfc0** RFC 0 developer trace
- **dev_rd** Gateway trace file
- **dev_icm** Internet Communication Manager developer trace
- **STDERR1** Information about the start process of the database system.
- **STDERR2** Information about the start process of the message server.
- **STDERR3** Information about the start process of the dispatcher.

Y para analizar mas a fondo, si tenemos SAP corriendo sobre GNU/Linux podemos hacer uso de la herramienta [strace](/notas/strace/).

Referencias útiles :

- <https://wiki.scn.sap.com/wiki/pages/viewpage.action?pageId=326533815>
- <http://itsiti.com/abap-system-logs-traces>

#### Unknown webmethod: WaitforServiceStarted

```
sapcpe pf=/usr/sap/<SID>/SYS/profile/<SID>_DVEBMGS00_<hostname>
sapcpe pf=/usr/sap/<SID>/SYS/profile/START_DVEBMGS00_<hostname>
```

#### Note 1069225 - NO HW ID RECEIVED BY MSSG SERVER

**Symptom**

After you upgrade to 7.00, you can no longer log on to the ABAP system. The system issues an error message on the logon screen to tell you this. Reason and Prerequisites

There is a conflict with the port numbers. These parameters are valuated as follows:

```
rslg/collect_daemon/listen_port   39xx
rslg/collect_daemon/talk_port     40xx
rdisp/msserv_internal             39xx
```

**Solution**

Delete these from the default profile to ensure that the system uses the default values 14xx and 15xx.

If these ports are already used by other programs, choose unused port numbers.

\- See more at: <http://www.stechno.net/sap-notes.html?view=sapnote&id=1069225#sthash.0nPTQ8aF.dpuf>

#### Registrar programa RFC

```
nohup rfcexec -a PROG_TRANSPOR -g server_host -x sapgw00 &
```

<http://wiki.scn.sap.com/wiki/display/ABAPConn/CPIC+Return+Code+679+-+Transaction+Program+not+Registered>

#### Convertir un certificado PFX a PSE

```
sapgenpse import_p12 -p  Certificado.pse Certificado.pfx
```

#### WebDynPro - 503 Service Unavailable

<http://hostsap:50000/webdynpro/dispatcher/sap.com/bi~plan~blablabla1/Modeler>

Si tenemos un error como este :

```
503   Service Unavailable

  SAP WebAS Engine is starting...

If this state does not change within a few minutes,
please contact your system administrator.
Check the recommendations in SAP Notes: 943498, 764417

Message: Dispatcher running but no server connected!

  Details: 	

  No details available
```

Lo primero que debemos revisar son los archivos de Log en **/usr/sap/\<SID\>/\<InstNo\>/j2ee/cluster/server0/log**

Si vemos dentro del trace un error como este :

```
"Start of UME Service Failed". Technical details: User is locked. Please notify the person responsible
```

Deberemos revisar desde ABAP que los usuarios

- SAPJSF
- J2EE_ADMIN
- J2EE_GUEST

Esten correctos. Si queremos podemos resetearle la contraseña al usuario **SAPJSF** Ademas deberemos ejecutar la configtool **/usr/sap/\<SID\>/\<InstNo\>/j2ee/configtool** y actualizar alli la contraseña del usuario del servicio UME

- Global server config -\> services -\> com.sap.security.core.ume.service
  - ume.r3.connection.master.client (100) : aca va el numero de mandante
  - ume.r3.connection.master.user (SAPJSF) : aca va el usuario que utilizamos para conectarnos a SAP

Luego de eso por medio de **jcmon pf=/usr/sap/\<SID\>/\<InstNo\>/\<SID\>\_\<InstNo\>\_\<hostname\>**, podremos arrancar el proceso server0

Si nos da un error como el de abajo, deberemos referinos a la **SAP Note 971249 - JMSServerSecurityException thrown accessing JMS resources**

```
 {0}#1#javax.jms.JMSSecurityException: You do not have permissions: action create_queue and instance ALL.
	at com.sap.jms.protocol.notification.ServerExceptionResponse.getException(ServerExceptionResponse.java:231)
	at com.sap.jms.client.session.Session.checkReceivedPacket(Session.java:2598)
	at com.sap.jms.client.session.Session.createQueue(Session.java:2425)
	at com.sap.ip.collaboration.core.api.rtmf.core.RTMFMessaging$JMSPolling.startRunning(RTMFMessaging.java:1244)
	at com.sap.ip.collaboration.core.api.rtmf.core.RTMFMessaging$JMSPolling.run(RTMFMessaging.java:1182)
	at java.lang.Thread.run(Thread.java:770)
```

FIXME Como TIP de como analizar podemos leer la **SAP Note 764417 - Information for troubleshooting of the SAP J2EE Engine 6.40**

#### Acceso a un Webservice HTTPS con un certificado autofirmado

Ejemplo del caso un proovedor puede ser privado o una agencia Gubernamental modificó un certificado SSL en su servicio web que necesitamos para ejecutar nuestros procesos de negocio.

Modificaron su certificado SSL (el cual todavia era válido ) por otro certificado autofirmado el cual por lo tanto no es válido y ninguna librería de SSL por lógica - NO LO VÁLIDA.

**ANTERIOR CERTIFICADO**

- Titular : c
- Autoridad certificante :
  - CN=GeoTrust SSL CA, O=“GeoTrust, Inc.”, C=US
- Válidez :
  - Válido de 15.08.2011 01:48:59 A 16.08.2014 23:02:18

**CERTIFICADO DEL DIA DE HOY**

- Titular : CN=SARAZAZAZA
- Autoridad certificante :
  - CN=SARAZAZAZA
- Válidez :
  - Válido de 14.02.2011 00:00:00 A 01.01.2020 23:59:59

Deberemos hacer uso de esta opcion **icm/HTTPS/verify_client** con un valor **0** dentro del profile de SAP por medio de la **Trx. RZ11**

- icm/HTTPS/verify_client = 0
  - 0: Do not use certificates
  - 1: Allow certificates (default)
  - 2: Require certificates

<https://help.sap.com/saphelp_nw70/helpdata/en/0d/88153a1a5b4c2de10000000a114084/content.htm>

## Agregando funcionalidades y oscuridades

### Oscuridad : Librerias extra para SAP

#### SAP Note 1644499 - Database connectivity from Linux to SQL Server

Se copia esta librería [dbmssslib.so.gz](/aprendiendo_sap/dbmssslib.so.gz) a **/usr/sap/\<SID\>/DVEBMGS00/exe/**

Luego de esto se ejeucuta :

```
# odbcinst -u -d -n "SQL Server Native Client 11.0"
# odbcinst -i -d -f template.ini
```

Luego de esta ejecución deberia quedar un contenido como este en **/etc/odbcinst.ini**

```ini>
{{:aprendiendo_sap:template.ini.gz|}}

=== Instalacion de Cliente SQL Server ===

El ODBC de Microsoft se puede instalar en :

**/opt/microsoft/msodbcsql/**

Microsoft® ODBC Driver 11 for SQL Server® - SUSE Linux Community Technology Preview - http://www.microsoft.com/en-us/download/details.aspx?id=34687
<code>
./build_dm.sh --prefix=/opt/microsoft/msodbcsql/

./install.sh install
```

Y el paquete unixODBC esta instalado en :

**/usr/local/unixodbc/**

<ftp://ftp.unixodbc.org/pub/unixODBC/>

```
ln -s /usr/local/unixodbc/bin/* /usr/local/bin
```

#### Nota

Como el cliente de SAP depende de una version mas vieja de unixODBC que la que esta soportada por el ODBC de Microsoft, debemos instalar los paquetes de la version anterior, ejemplo en SuSE :

```
unixODBC-2.2.12-198.17.x86_64.rpm
unixODBC-32bit-2.2.12-198.17.x86_64.rpm
unixODBC-devel-2.2.12-198.17.x86_64.rpm
unixODBC-devel-32bit-2.2.12-198.17.x86_64.rpm
```

```
/usr/bin/odbcinst -u -d -n "SQL Server Native Client 11.0"
/usr/bin/odbcinst -i -d -f template.ini
```

Para testear la conexion podemos hacerlo desde la transacción **DB02**.

:!: En caso de que tengamos problemas deberemos ver los archivos de los dev_w\* que se encuentran dentro de /usr/sap/\<SID\>/DVEBMGS00/work

#### Convert saplogon.ini file to connections file for SAPGUI Java

[sapguiinitosapcconverter.jar.gz](/aprendiendo_sap/sapguiinitosapcconverter.jar.gz)

java -jar \[complete path to SapGuiIniToSapcConverter.jar\] \[path to source saplogon.ini file\] \[path to target connections file\]

/home/scayuqueo/.SAPGUI/connections

Ref.: <http://scn.sap.com/people/mike.schernbeck2/blog/2011/01/03/convert-saplogonini-file-to-connections-file-for-sapgui-java>

## Referencias

- [An Introduction to Linux Shell Scripting for DBAs](/manuales/an_introduction_to_linux_shell_scripting_for_dbas/)
- [HOW TO Run SAP GUI On LINUX To Connect SAP R3 Systems](http://www.linuxquestions.org/linux/answers/Applications_GUI_Multimedia/HOW_TO_Run_SAP_GUI_On_LINUX_To_Connect_SAP_R3_Systems)
- [Convert saplogon.ini file to connections file for SAPGUI Java](http://scn.sap.com/people/mike.schernbeck2/blog/2011/01/03/convert-saplogonini-file-to-connections-file-for-sapgui-java)
- <http://www.orafaq.com/wiki/Alert_log>
- [Troubleshooting BI on Linux with strace](http://wiki.scn.sap.com/wiki/display/BOBJ/Troubleshooting+BI+on+Linux+with+strace)
