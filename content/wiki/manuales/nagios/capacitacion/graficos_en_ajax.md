---
title: "Gráficos en AJAX"
date: 2015-10-22T13:56:04-03:00
---

### Highcharts for Nagios

Highcharts es una librería de JavaScript para generar gráficos interactivos de líneas, área, barras, columnas, torta y muchos más. Es totalmente compatible con la mayoría de los navegadores incluyendo el Safari para iPhone y el Internet Explorer desde su versión 6. Entre sus características se destacan:

- Gratis para uso No-Comercial
- Puro JavaScript
- Sintaxis de configuración sencilla
- Numerosos tipos de gráficos
- Etiquetas Tooltip
- Múltiples ejes
- Eventos
- Ejes de fecha y hora
- Zooming
- Carga externa de datos
- Gráfico invertido o eje invertido
- Rotación del texto en las etiquetas

En este caso vamos mencionar la implementación para tomar gráficos RRD de PNP4Nagios y transformarlos en en formato AJAX de Highcharts.

<img src="/manuales/nagios/highcharts4nagios_rs.jpg" class="align-center" alt="highcharts4nagios_rs.jpg" />

<img src="/manuales/nagios/highcharts4nagios_load.png" class="align-center" alt="highcharts4nagios_load.png" />

Además podemos capturar datos por medio de MK LiveStatus y dibujar gráficos con dichos datos, podemos por ejemplo consultar un array en JSON y escribir las variables, por ejemplo consultamos el numero de servicios en Nagios, luego de dichos servicios cuantos estan en status OK, WARNING y CRITICAL y devolvemos en porcentaje y obtenemos este simple código, que nos va a generar un gráfico que luego podremos exportar en PNG, SVG, JPG, PDF :

```java>
{{ :manuales:nagios:highcharts4nagios_pie.jpg }}

Para poder configurar el módulo de exportación de los gráficos a PNG, SVG, PDF. Deberemos tener instalado en nuestro servidor el paquete http://xmlgraphics.apache.org/batik/ y luego indicar en el módulo de exportación lo siguiente :
<code php>
define ('BATIK_PATH', 'batik-1.7/batik-rasterizer.jar');
```

#### jVectorMap

Con jVectorMap podemos dibujar mapas dinámicos vectoriales en AJAX, podemos hacer que por ejemplo al pasar el mouse por cada país por medio de datos obtenidos de MKLiveStatus muestre el estado de hosts y servicios de cada región.

<img src="/manuales/nagios/jvectormap.jpg" class="align-center" alt="jvectormap.jpg" />

Ejemplo muy simple :

```java
    jQuery.noConflict();
    jQuery(function(){
      var $ = jQuery;
      $('#map').vectorMap({
        map: 'ar_mill_en',
	hoverOpacity: 0.7,
	hoverColor: false,
	backgroundColor: 'transparent',
	zoomButtons: false,
	zoomOnScroll: false,
        regionStyle: {
    	    initial: {
    		fill: '#E1AF6C'
    	    }
	},
	onRegionClick: function(event, code){
	    cargar_ajax('hostgroup_info.php?code='+code+'');
	}
      });
    })
```
