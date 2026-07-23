---
title: "PNP4Nagios"
date: 2020-08-11T18:58:51-03:00
---

<img src="/manuales/nagios/pnp4nagios.jpg" class="align-center" alt="pnp4nagios.jpg" />

**Compilación**

./configure –prefix=/usr/local/pnp4nagios –with-nagios-user=nagios –with-nagios-group=nagios

```
*** Configuration summary for pnp4nagios-0.6.26 08-21-2017 ***

  General Options:
  -------------------------         -------------------
  Nagios user/group:                nagios nagios
  Install directory:                /usr/local/pnp4nagios
  HTML Dir:                         /usr/local/pnp4nagios/share
  Config Dir:                       /usr/local/pnp4nagios/etc
  Location of rrdtool binary:       /usr/bin/rrdtool Version 1.7.2
  RRDs Perl Modules:                FOUND (Version 1.7002)
  RRD Files stored in:              /usr/local/pnp4nagios/var/perfdata
  process_perfdata.pl Logfile:      /usr/local/pnp4nagios/var/perfdata.log
  Perfdata files (NPCD) stored in:  /usr/local/pnp4nagios/var/spool

  Web Interface Options:
  -------------------------         -------------------
  HTML URL:                         http://localhost/pnp4nagios
  Apache Config File:               /etc/httpd/conf.d/pnp4nagios.conf


  Review the options above for accuracy.  If they look okay,
  type 'make all' to compile.
```

make all

```
*** PNP4Nagios sample config files installed ***

Please run 'make install-init' if you want to use
BULK Mode with NPCD



cd ./scripts && make install-init
make[1]: Entering directory '/root/pnp4nagios/scripts'
/usr/bin/install -c -m 755 -o root -g root -d /etc/init.d
/usr/bin/install -c -m 755 -o root -g root rc.npcd /etc/init.d/npcd
/usr/bin/install -c -m 755 -o root -g root rc.pnp_gearman_worker /etc/init.d/pnp_gearman_worker
make[1]: Leaving directory '/root/pnp4nagios/scripts'
/usr/bin/perl summary fullinstall


*** Configuration summary for pnp4nagios-0.6.26 08-21-2017 ***

  General Options:
  -------------------------         -------------------
  Nagios user/group:                nagios nagios
  Install directory:                /usr/local/pnp4nagios
  HTML Dir:                         /usr/local/pnp4nagios/share
  Config Dir:                       /usr/local/pnp4nagios/etc
  Location of rrdtool binary:       /usr/bin/rrdtool Version 1.7.2
  RRDs Perl Modules:                FOUND (Version 1.7002)
  RRD Files stored in:              /usr/local/pnp4nagios/var/perfdata
  process_perfdata.pl Logfile:      /usr/local/pnp4nagios/var/perfdata.log
  Perfdata files (NPCD) stored in:  /usr/local/pnp4nagios/var/spool

  Web Interface Options:
  -------------------------         -------------------
  HTML URL:                         http://localhost/pnp4nagios
  Apache Config File:               /etc/httpd/conf.d/pnp4nagios.conf



*** Main program, Scripts and HTML files installed ***

Enjoy.
```

make install-init

```
cd ./scripts && make install-init
make[1]: Entering directory '/root/pnp4nagios/scripts'
/usr/bin/install -c -m 755 -o root -g root -d /etc/init.d
/usr/bin/install -c -m 755 -o root -g root rc.npcd /etc/init.d/npcd
/usr/bin/install -c -m 755 -o root -g root rc.pnp_gearman_worker /etc/init.d/pnp_gearman_worker
make[1]: Leaving directory '/root/pnp4nagios/scripts'
```

Para la ejecución de almacenamiento de gráficas deberemos configurar ciertos comandos que obtengan los resultados de la ejecución de comandos y servicios, para ellos deberemos agregar y/o modificar en la configuración de Nagios ciertos parámetros :

**Modo Síncrono**

*El modo síncrono es la forma más fácil de integrar en nagios el recolector de datos process_perfdata.pl. Cada evento dispara la ejecución de process-service-perfdata.*

```
enable_environment_macros=1
service_perfdata_command=process-service-perfdata
host_perfdata_command=process-host-perfdata
```

```
define command {
       command_name    process-service-perfdata
       command_line    /usr/bin/perl /usr/local/nagios/pnp4nagios/libexec/process_perfdata.pl
}

define command {
       command_name    process-host-perfdata
       command_line    /usr/bin/perl /usr/local/nagios/pnp4nagios/libexec/process_perfdata.pl -d HOSTPERFDATA
}
```

**Modo Masivo**

*En el modo masivo, Nagios escribe los datos a un fichero temporal en un formato predefinido. Este fichero se procesa mediante process_perfdata.pl a intervalos regulares. Nagios controla el arranque y ejecución periódica del recolector de datos. .*

**commands.cfg**

```
define command{
       command_name    process-service-perfdata-file
       command_line    /usr/local/nagios/pnp4nagios/libexec/process_perfdata.pl --bulk=/usr/local/nagios/pnp4nagios/var/service-perfdata
}

define command{
       command_name    process-host-perfdata-file
       command_line    /usr/local/nagios/pnp4nagios/libexec/process_perfdata.pl --bulk=/usr/local/nagios/pnp4nagios/var/host-perfdata
}
```

**Modo Masivo con NPCD**

*Se vuelca la informacion de perfdata en una cola para luego ser procesada por un proceso en segundo plano, lo cual libera gran carga del CPU.*

**nagios.cfg**

```
process_performance_data=1
#
# service performance data
#
service_perfdata_file=/usr/local/nagios/var/service-perfdata
service_perfdata_file_template=DATATYPE::SERVICEPERFDATA\tTIMET::$TIMET$\tHOSTNAME::$HOSTNAME$\tSERVICEDESC::$SERVICEDESC$\tSERVICEPERFDATA::$SERVICEPERFDATA$\tSERVICECHECKCOMMAND::$SERVICECHECKCOMMAND$\tHOSTSTATE::$HOSTSTATE$\tHOSTSTATETYPE::$HOSTSTATETYPE$\tSERVICESTATE::$SERVICESTATE$\tSERVICESTATETYPE::$SERVICESTATETYPE$\tSERVICEOUTPUT::$SERVICEOUTPUT$
service_perfdata_file_mode=a
service_perfdata_file_processing_interval=15
service_perfdata_file_processing_command=process-service-perfdata-file

#
# host performance data starting with Nagios 3.0
# 
host_perfdata_file=/usr/local/nagios/var/host-perfdata
host_perfdata_file_template=DATATYPE::HOSTPERFDATA\tTIMET::$TIMET$\tHOSTNAME::$HOSTNAME$\tHOSTPERFDATA::$HOSTPERFDATA$\tHOSTCHECKCOMMAND::$HOSTCHECKCOMMAND$\tHOSTSTATE::$HOSTSTATE$\tHOSTSTATETYPE::$HOSTSTATETYPE$\tHOSTOUTPUT::$HOSTOUTPUT$
host_perfdata_file_mode=a
host_perfdata_file_processing_interval=15
host_perfdata_file_processing_command=process-host-perfdata-file
```

**commands.cfg**

```
define command{
        command_name    process-service-perfdata-file
        command_line    /bin/mv /usr/local/nagios/var/service-perfdata /usr/local/nagios/var/spool/perfdata/service-perfdata.$TIMET$
 }

define command{
        command_name    process-host-perfdata-file
        command_line    /bin/mv /usr/local/nagios/var/host-perfdata /usr/local/nagios/var/spool/perfdata/host-perfdata.$TIMET$
 }
```

Y luego ejecutar :

```
/etc/init.d/npcd start
```

#### Datos de configuración

- **service_perfdata_file**
  - Ruta al archivo temporal que debe contener los datos de rendimiento.
- **service_perfdata_file_template**
  - Formato del archivo temporal. Los datos se definen utilizando Nagios macros.
- **service_perfdata_file_mode**
  - Opción “a” especifica que los datos se insertan como anexo.
- **service_perfdata_file_processing_interval**
  - El intervalo de procesamiento es de 15 segundos
- **service_perfdata_file_processing_command**
  - El comando que habrá de ejecutarse durante dicho intervalo.

Luego se deja ejecutando en segundo plano el demonio npcd para procesar la cola de mensajes.
{{% hint success %}}
En modo masivo con NPCD se puede deshabilitar la opcion *enable_environment_macros* para ahorrar carga de CPU, ya que en este modo esta ya no es requerida.

Si en algún momento se llega a colgar el demonio NPCD o si por algo falla el procesamiento de los archivos de perfdata, reiniciamos NPCD y va a reprocesar los archivos en el spool de perfdata para volver a incluir la información faltante en RRD.
{{% /hint %}}
**Interfaz web**

Luego en el template que vayamos a utilizar deberemos agregar estas directivas :

**Para los hosts**

```
action_url /pnp4nagios/index.php/graph?host=$HOSTNAME$
```

**Para los servicios**

```
action_url /pnp4nagios/index.php/graph?host=$HOSTNAME$&srv=$SERVICEDESC$
```

<img src="/manuales/nagios/nagios_pnp.png" class="align-center" alt="nagios_pnp.png" />

[nagios_pnp.dia.gz](/manuales/nagios/nagios_pnp.dia.gz)

#### Extrancción de datos de los RRD de PNP4Nagios

Podemos extraer los datos almacenados en los datos RRD, para luego procesarlos y mostralo en la herramienta de reportes que desarrollemos o por ejemplo, en ves de utilizar Highcharts podemos utilizar la libreria D3js o alguna otra.

<https://servidor_nagios/pnp4nagios/xport/csv?host=servidor1&srv=Load%20Linux>

timestamp;load_1_min_MIN;load_1_min_MAX;load_1_min_AVERAGE;load_5_min_MIN;load_5_min_MAX;load_5_min_AVERAGE;load_15_min_MIN;load_15_min_MAX;load_15_min_AVERAGE 1425027060;6.0332333333;6.0332333333;6.0332333333;5.899;5.899;5.899;5.7517666667;5.7517666667;5.7517666667 1425027120;6.0332333333;6.0332333333;6.0332333333;5.899;5.899;5.899;5.7517666667;5.7517666667;5.7517666667 1425027180;5.9871333333;5.9871333333;5.9871333333;6.2204;6.2204;6.2204;5.9390333333;5.9390333333;5.9390333333 1425027240;5.9871333333;5.9871333333;5.9871333333;6.2204;6.2204;6.2204;5.9390333333;5.9390333333;5.9390333333 …..

<https://servidor_nagios/pnp4nagios/xport/xml?host=servidor2&srv=Load%20Linux>

```xml>
load_1_min_MIN</entry>
      <entry>load_1_min_MAX</entry>
      <entry>load_1_min_AVERAGE</entry>
      <entry>load_5_min_MIN</entry>
      <entry>load_5_min_MAX</entry>
      <entry>load_5_min_AVERAGE</entry>
      <entry>load_15_min_MIN</entry>
      <entry>load_15_min_MAX</entry>
      <entry>load_15_min_AVERAGE</entry>
    </legend>
  </meta>
  <data>
    <row><t>1425027180</t><v>5.9871333333e+00</v><v>5.9871333333e+00</v><v>5.9871333333e+00</v><v>6.2204000000e+00</v><v>6.2204000000e+00</v><v>6.2204000000e+00</v><v>5.9390333333e+00</v><v>5.9390333333e+00</v><v>5.9390333333e+00</v></row>
    <row><t>1425027240</t><v>5.9871333333e+00</v><v>5.9871333333e+00</v><v>5.9871333333e+00</v><v>6.2204000000e+00</v><v>6.2204000000e+00</v><v>6.2204000000e+00</v><v>5.9390333333e+00</v><v>5.9390333333e+00</v><v>5.9390333333e+00</v></row>
    <row><t>1425027300</t><v>5.9871333333e+00</v><v>5.9871333333e+00</v><v>5.9871333333e+00</v><v>6.2204000000e+00</v><v>6.2204000000e+00</v><v>6.2204000000e+00</v><v>5.9390333333e+00</v><v>5.9390333333e+00</v><v>5.9390333333e+00</v></row>
    <row><t>1425027360</t><v>5.9871333333e+00</v><v>5.9871333333e+00</v><v>5.9871333333e+00</v><v>6.2204000000e+00</v><v>6.2204000000e+00</v><v>6.2204000000e+00</v><v>5.9390333333e+00</v><v>5.9390333333e+00</v><v>5.9390333333e+00</v></row>
    <row><t>1425027420</t><v>5.9871333333e+00</v><v>5.9871333333e+00</v><v>5.9871333333e+00</v><v>6.2204000000e+00</v><v>6.2204000000e+00</v><v>6.2204000000e+00</v><v>5.9390333333e+00</v><v>5.9390333333e+00</v><v>5.9390333333e+00</v></row>
....
```

<https://servidor_nagios/pnp4nagios/xport/json?host=servidor3&srv=Load%20Linux>

```
{"meta":{"start":"1425027180","step":"60","end":"1425041580","rows":"241","columns":"9","legend":{"entry":["load_1_min_MIN","load_1_min_MAX","load_1_min_AVERAGE","load_5_min_MIN","load_5_min_MAX","load_5_min_AVERAGE","load_15_min_MIN","load_15_min_MAX","load_15_min_AVERAGE"]}},"data":{"row":[{"t":"1425027180","v":["5.9871333333e+00","5.9871333333e+00","5.9871333333e+00","6.2204000000e+00","6.2204000000e+00","6.2204000000e+00","5.9390333333e+00","5.9390333333e+00","5.9390333333e+00"]},{"t":"1425027240","v":["5.9871333333e+00","5.9871333333e+00","5.9871333333e+00","6.2204000000e+00","6.2204000000e+00","6.2204000000e+00","5.9390333333e+00","5.9390333333e+00","5.9390333333e+00"]},{"t":"1425027300","v":["5.9871333333e+00","5.9871333333e+00","5.9871333333e+00","6.2204000000e+00","6.2204000000e+00","6.2204000000e+00","5.9390333333e+00","5.9390333333e+00","5.9390333333e+00"]},{"t":"1425027360","v":["5.9871333333e+00","5.9871333333e+00","5.9871333333e+00","6.2204000000e+00","6.2204000000e+00","6.2204000000e+00","5.9390333333e+00","5.9390333333e+00","5.9390333333e+00"]},{"t":"1425027420","v":["5.9871333333e+00","5.9871333333e+00","5.9871333333e+00","6.2204000000e+00","6.2204000000e+00","6.2204000000e+00","5.9390333333e+00","5.9390333333e+00","5.9390333333e+00"]},{"t":"1425027480","v":
....
```

#### Extrancción de datos de las imágenes de los RRD de PNP4Nagios

Si por ejemplo necesitamos embeber las imágenes de pnp4nagios dentro de por ejemplo un reporte en PDF que realizamos con algun script en este caso en PHP, podemos ver el siguiente ejemplo :

```php>

```
