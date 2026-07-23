---
title: "Midiendo el rendimiento del sistema con SAR"
date: 2014-01-02T17:33:42-03:00
---

El nombre sar proviene de las siglas de “system activity report” (informe de la actividad del sistema). En Linux se encuentra normalmente en el paquete sysstat. El paquete sysstat incluye programas y scripts para recopilar y mostrar información sobre el rendimiento del sistema, generando informes detallados. Este conjunto de programas puede resultar de mucha utilidad a la hora de detectar cuellos de botella y para hacernos una idea de cómo se utiliza el sistema a lo largo del día

## Recopilar datos sobre el rendimiento

El programa que se ocupa de recopilar la información se llama `sadc` (system activity data collector o recolector de datos de la actividad del sistema). Obtiene su información, principalmente, del kernel, a través del sistema de archivos virtual en /proc. Después guarda los datos en un fichero (uno por día con nombre `/var/log/sa/saDD` donde DD es el día del mes.

El paquete incluye dos shell scripts. El primer script, `sa1`, recopila datos de forma regular, mientras que el script sa2 se utiliza para crear los informes resumidos (uno por día en `/var/log/sa/sarDD`. Ambos scripts se ejecutan usando cron. En Red Hat Enterprise estos scripts se añaden automaticamente a las tareas de cron de la siguiente forma:

```
# ejecuta la herramienta de recopilación de datos cada 10 minutos
*/10 * * * * root /usr/lib/sa/sa1 1 1
# genera un informe diario del rendimiento de los procesos a las 23:53
53 23 * * * root /usr/lib/sa/sa2 -A
```

Con la configuración por defecto los datos se recopilan cada 10 minutos y se genera un informe justo antes de la media noche. Si sospechas que puede existir un problema de rendimiento con un programa en particular, puedes utilizar `sadc` para recopilar datos sobre ese proceso (`-x`), o sus hijos (`-X`), pero necesitarás crear tu propio script que use esos flags.

Como demostró el Dr. Heisenberg, el simple hecho de realizar una medida cambia los datos medidos. Cualquier herramienta dedicada a recopilar datos sobre rendimiento tiene un cierto impacto negativo en el rendimiento del sistema, pero con sar, este parece ser mínimo. Hice una prueba en la que cron lanzaba el programa sa1 para obtener datos cada minuto (en un servidor que no estaba demasiado ocupado) y no causó ningún problema serio. Lo cual puede no resultar cierto en un sistema con mucha carga.

## Creando informes

Si los informes diarios creados por el script sa2 no son suficientes, puedes crear los tuyos propios utilizando `sar`. Por defecto este programa obtiene la información del archivo de datos del día actual, a menos que se especifique lo contrario. Para hacer que sar obtenga los datos de un archivo en particular, utilizamos el flag `-f /var/log/sa/saDD`. Puedes seleccionar multiples archivos usando varios flags `-f`. Dado que muchos de los informes que crea sar tienen un tamaño considerable, puede que quieras redirigir la salida a un archivo.

Para crear un informe básico que muestre el uso de CPU y el porcentaje de tiempo gastado esperando E/S, ejecutamos `sar` sin ningún argumento. Generará un informe similar al siguiente:\

```
01:10:00 PM       CPU     %user     %nice   %system   %iowait     %idle
01:20:00 PM       all      7.78      0.00      3.34     20.94     67.94
01:30:00 PM       all      0.75      0.00      0.46      1.71     97.08
01:40:00 PM       all      0.65      0.00      0.48      1.63     97.23
01:50:00 PM       all      0.96      0.00      0.74      2.10     96.19
02:00:00 PM       all      0.58      0.00      0.54      1.87     97.01
02:10:00 PM       all      0.80      0.00      0.60      1.27     97.33
02:20:01 PM       all      0.52      0.00      0.37      1.17     97.94
02:30:00 PM       all      0.49      0.00      0.27      1.18     98.06
Average:          all      1.85      0.00      0.44      2.56     95.14
```

Si %idle (desocupado) está cerca de cero, tu CPU está sobrecargada. Si el valor %iowait (espera por e/s) es grande, tus discos están sobrecargados.

Para comprobar cómo se comporta el fichero de paginación ejecuta `sar -B` para obtener un informe similar al siguiente:

```
11:00:00 AM  pgpgin/s pgpgout/s   fault/s  majflt/s
11:10:00 AM      8.90     34.08      0.00      0.00
11:20:00 AM      2.65     26.63      0.00      0.00
11:30:00 AM      1.91     34.92      0.00      0.00
11:40:01 AM      0.26     36.78      0.00      0.00
11:50:00 AM      0.53     32.94      0.00      0.00
12:00:00 PM      0.17     30.70      0.00      0.00
12:10:00 PM      1.22     27.89      0.00      0.00
12:20:00 PM      4.11    133.48      0.00      0.00
12:30:00 PM      0.41     31.31      0.00      0.00
Average:       130.91     27.04      0.00      0.00
```

Puede que el número de veces que se ha tenido que recurrir a la paginación no sea determinante, pero el que exista un alto número de fallos mayores de página (majflt/s) indica que el sistema necesita más memoria. (nota: majflt/s sólo es válido para versiones del kernel \>= 2.5).

Para obtener estadísticas de red, utilizamos `sar -n DEV`. Este comando genera un informe que muestra estadísticas con los datos transmitidos y recibidos para cada interfaz de red. Veamos una versión abreviada de este informe:

```
11:00:00 AM     IFACE   rxpck/s   txpck/s   rxbyt/s   txbyt/s
11:10:00 AM        lo      0.62      0.62     35.03     35.03
11:10:00 AM      eth0     29.16     36.71   4159.66  34309.79
11:10:00 AM      eth1      0.00      0.00      0.00      0.00
11:20:00 AM        lo      0.29      0.29     15.85     15.85
11:20:00 AM      eth0     25.52     32.08   3535.10  29638.15
11:20:00 AM      eth1      0.00      0.00      0.00      0.00
```

Para ver los errores de red, prueba con el comando `sar -n EDEV`.

## Informes en tiempo real

También podemos utilizar sar para ver que está pasando en este preciso momento con un componente del sistema. Si le pasamos un intervalo de tiempo (en segundos) y el número de informes a producir, podemos obtener información inmediata sobre un posible cuello de botella.

Por ejemplo, para ver el informe básico cada segundo durante los próximos 10 segundos, usaríamos `sar 1 10`. Cualquiera de las combinaciones de flags anteriores se pueden ejecutar con estos parámetros para obtener resultados cercanos al tiempo real.

## Pruebas de rendimiento

Incluso si contamos con potencia de sobra para correr nuestras aplicaciones, sar siempre puede ser útil para registrar cambios en el volumen de trabajo a lo largo del tiempo. Para hacer esto, guardaremos los informes (sar sólo guarda siete) en un directorio diferente durante un periodo de tiempo de unas pocas semanas o un mes. Este grupo de informes pueden servir como ejemplo del volumen de trabajo normal del sistema. Se pueden comparar los siguientes informes con estos resultados previos para ver cómo cambia el volumen de trabajo con el tiempo. Podemos incluso automatizar estos informes usando [Awk](/notas/programacion/introduccion_a_awk/) ú lenguaje de programación.

En la gestión de grandes sistemas, las pruebas de rendimiento son importantes para predecir qué hardware actualizar y cuándo hacerlo. También nos proporciona datos con los que justificar estas actualizaciones.

## Profundizando

La mayor parte de los problemas de rendimiento de hardware están relacionados con los discos, la memoria, o la CPU. Otras veces errores al programar las aplicaciones o bases de datos mal diseñadas pueden producir problemas de rendimiento realmente serios. De cualquier forma, sar y sus agregados nos pueden dar una visión detallada de cómo se comporta nuestro sistema y ayudarnos a detectar y arreglar problemas. Los ejemplos anteriores son algo superficial de lo que se puede hacer con `sar`. Se pueden crear informes adicionales muy detallados con los programas `iostat` y `mpstat` incluídos en el mismo paquete.
