---
title: "Análisis del código fuente de Nagios"
date: 2019-08-26T13:50:44-03:00
---

## Plugins

La interfaz de plugins de Nagios es simple, pero también significa que Nagios no se preocupa por la longitud de salida del plugin. Si es superior a la longitud del búfer interno, no es informado y a menudo nadie se da cuenta. El contenido simplemente se corta o trunca.

Para los datos de rendimiento/performance : Si el buffer no es lo suficientemente grande, los datos de rendimiento pueden verse afectados.

<table>
<thead>
<tr>
<th>Comparativa de tamañao de buffer en versiones de Nagios</th>
<th></th>
<th></th>
</tr>
</thead>
<tbody>
<tr>
<td>Maximum plugin output<br />
in bytes</td>
<td>Nagios version</td>
<td>Include file</td>
</tr>
<tr>
<td>352</td>
<td>1.0</td>
<td>common/objects.h</td>
</tr>
<tr>
<td>348</td>
<td>2-0</td>
<td>include/objects.h</td>
</tr>
<tr>
<td>332</td>
<td>2-1</td>
<td>include/objects.h</td>
</tr>
<tr>
<td>4096</td>
<td>3-0a</td>
<td>include/nagios.h</td>
</tr>
<tr>
<td>8192</td>
<td>3-0</td>
<td>include/nagios.h</td>
</tr>
</tbody>
</table>

El buffer de datos se modifica en la siguiente constante **MAX_PLUGIN_OUTPUT_LENGTH**.

```c
#define MAX_PLUGIN_OUTPUT_LENGTH                8192    /* max length of plugin output (including perf data) */
```

<https://fossies.org/dox/nagios-4.4.5/>
