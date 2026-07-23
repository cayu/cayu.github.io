---
title: "Midiendo el rendimiento del sistema con IOSTAT"
date: 2014-10-07T16:52:30-03:00
---

Mediante el comando iostat podemos informarnos las estadísticas de entrada y salida de dispositivos y generar medidas de rendimiento de uso, lecturas/escrituras etc. Para obtener una descripción detallada del comando, podemos consultar el manual de dicho comando **man iostat 1**.

## Presentación de los datos

### Escrituras

Podemos confeccionar una planilla de calculo para mostras la tasa de cambios que hay en los discos. Esto es útil cuando por ejemplo tenemos replicación de los datos en caso de un disaster recovery. Para así saber cuando ancho de banda es necesario para cubrir nuestro respaldo.

A continuación se adjunta un plantilla para una planilla de calculo en formato Open Document, que ya incluye las fórmulas para hacer mas simple su lectura [iostat.ods](/notas/linux/iostat.ods)
