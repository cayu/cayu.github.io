---
title: "MicroStrategy"
date: 2015-09-15T09:08:20-03:00
---

MicroStrategy es una compañía que ofrece software de inteligencia de negocio y de informes para empresas. El software de MicroStrategy permite crear informes y análisis de datos almacenados en una Base de datos relacional y otras fuentes.

## Monitoreo

Para plantear el monitoreo de la solución debemos conocer el landscape de integral de MicroStrategy con sus ambientes, DES, QAS, PRD.

| Configuración usual de servicios |  |  |  |
|----|----|----|----|
| Intelligence Server IS | Web Server MSW | Narrowcast NW | Base de datos |
| Equipo Linux donde se configuran los accesos a datos por ODBC | Equipo Linux con Tomcat | Equipo Windows con herramienta de administración del IS | Oracle |

### Aspectos a chequear

| Capa de Sistema Operativo |  |
|----|----|
| Estado de Host |  |
| Carga de sistema o de CPU |  |
| Consumo de memoria |  |
| Espacio en discos rígidos |  |
| Base de datos |  |
| Puertos de conexión | Ej 1527 |
| [oracle](/manuales/nagios/capacitacion/productos/oracle/) | Ver este apartado si es Oracle |
| Capa de Aplicación |  |
| **Servicios Linux** |  |
| Tomcat | Nivel de proceo y nivel interno, ej consulta al website, respuesta 200 |
| Intelligence Server | Nivel de proceso y nivel interno, ej consulta interna de estado |
| **Servicios Windows** |  |
| MicroStrategy Distribution Manager |  |
| MicroStrategy Execution Engine |  |
| MicroStrategy Listener |  |
| MicroStrategy Logging Client |  |
| MicroStrategy Logging Consumer |  |
| MicroStrategy Logging Server |  |
| MicroStrategy SMTP Service |  |

#### Scripts

##### check_microstrategy

```bash
#!/bin/sh
SALIDA_SSH=`ssh $1 -l monitoreo "sudo /msis/var/opt/MicroStrategy/bin/mstrctl -s IntelligenceServer gs | grep state|  sed 's/<[^>]*[>]//g' | sed 's/\t//g' | sed 's/\n//g'"`
if [ $SALIDA_SSH="running" ]
then
    echo "OK - Proceso MicroStrategy corriendo"
    exit 0;
else
    echo "CRITICAL - Hay un problema con el proceso MicroStrategy"
fi
```

```perl
#!/usr/bin/perl
open SSH, ("ssh ".$ARGV[0]." -l monitoreo \"sudo /msis/var/opt/MicroStrategy/bin/mstrctl -s IntelligenceServer gs\" | grep state|  sed 's/<[^>]*[>]//g' | sed 's/\\t//g' | sed 's/\\n//g'|");

while ( defined( my $line = <SSH> )  ) {
    chomp($line);
    if ($line eq "running") {
	print "OK - Proceso MicroStrategy corriendo (".$line.")\n";
	exit 0;
    } else {
	print "CRITICAL - Hay un problema con el proceso MicroStrategy (".$line.")\n";
	exit 2;
  }
}
close SSH;
```

## Problemas comúnes

### Locales y Tomcat

A veces cuando usamos aplicaciones que parcialmente utilizan Tomcat, como es el caso de MicroStrategy podemos tener diferencias con los reportes generados en via web y los generados desde el Intelligence Server, si los generados via web por ejemplo tienen incorrecto los separadores de coma y simbolos de moneda es muy probable que sean las locales, podemos fijarnos en modificar ciertas opciones de arranque del Tomcat.

**/etc/init.d/tomcat5** (al principio del archivo)

```bash>
**/etc/profile** (al final del archivo)
<code bash>
export LC_ALL="es_AR.utf8"
```
