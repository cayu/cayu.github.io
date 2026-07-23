---
title: "Nagvis"
date: 2015-10-22T14:09:00-03:00
---

Nagvis es un addon para Nagios, con el cual podemos tener gráficos a modo de diagrama estructural de red, dinámicos, con lo cual podemos conocer el estado actual de la red mirando un gráfico amigable al usuario final para que pueda tomar acciones y/o informar sobre el estado de los mismos a personal responsable.

Se pueden utilizar imágenes propias de fondo (denominadas mapas) y luego integrarle iconos representativos de las máquinas y servicios de la red que muestren el estado actual de los mismos.

![c_by_dave_rearden_2.png](/manuales/nagios/c_by_dave_rearden_2.png)

![nagvis_map_2.png](/manuales/nagios/nagvis_map_2.png)

Deberemos bajar el paquete Nagvis desde su sitio web, y descomprimirlo y copiarlo a un directorio visible desde la interfaz web de Nagios.

```
root@localhost # tar xvzf nagvis-1.3.x.tar.gz
root@localhost # mv nagvis /usr/local/nagios/share
```

Para instalar la configuración por defecto deberemos dentro de /usr/local/nagios/share/nagvis renombrar o mover el archivo etc/nagvis.ini.php-sample a etc/nagvis.ini.php.

Para su correcto funcionamiento necesitaremos establecer una serie particular de permisos

```
root@localhost # chmod 664 /usr/local/nagios/share/nagvis/etc/nagvis.ini.php
root@localhost # chmod 775 /usr/local/nagios/share/nagvis/nagvis/images/maps
root@localhost # chmod 664 /usr/local/nagios/share/nagvis/nagvis/images/maps/*
root@localhost # chmod 775 /usr/local/nagios/share/nagvis/etc/maps
root@localhost # chmod 664 /usr/local/nagios/share/nagvis/etc/maps/*
root@localhost # chmod 775 /usr/local/nagios/share/nagvis/var
root@localhost # chmod 664 /usr/local/nagios/share/nagvis/var/*
```

**Nota sobre la generación del Automap**

Para una correcta generación de la característica Automap de Nagvis, primero es necesario configurar todos los *parents hosts* para poder diagramar en tiempo real la topología de red. Ademas deberemos tomar los iconos de la directiva *statusmap_image* que especificamos en el anexo *hostextinfo*, dicho icono se almacena en *nagios/share/images/logos*, en formato gd2 entendible por el binario statusmap.cgi para mostrarlo via web, pero no es entendible por nagvis siendo que este lo busca en base al parametro *statusmap_image* entonces aprovechando que dicho icono esta en formato gd2 y en formato png copiaremos en archivo en formato png al directorio *nagvis/images/shapes* pero cambiandole la extensión de png a gd2, y entonces Nagvis lo vera como un gd2 siendo que realmente es un PNG y debido a eso sera capas de mostrarlo.

**Nota Nagvis 1.5 y template integrada con PNP**

Modificar el archivo *nagvis/share/userfiles/templates/default.hover.html* de la siguiente manera, agregando solo las dos lineas donde especificamos el tag img.

```html>
	<tr><th colspan="2">[lang_obj_type] ([lang_last_status_refresh]: [last_status_refresh])</th></tr>
	<tr><td class="label"><label>[lang_name]</label></td><td>[obj_name]</td></tr>
	<!-- BEGIN service -->
	<tr><td class="label"><label>[lang_service_description]</label></td><td>[service_description]</td></tr>
	<!-- END service -->
	<tr><td class="label"><label>[lang_alias]</label></td><td>[obj_alias]</td></tr>
	<!-- BEGIN host -->
	<tr><td colspan="2" style="text-align:center;"><img src="/nagios/pnp/index.php?host=[obj_name]&srv=_HOST_&source=1&view=0&display=image" with="300px" height="94px"></td></tr>
	<tr><td class="spacer" colspan="2"></td></tr>
	<tr><td class="label[obj_state]"><label>[lang_state] ([lang_state_type])</label></td><td class="state[obj_state]">[obj_state] ([obj_state_type])[obj_in_downtime][obj_acknowledged]</td></tr>
	<tr><td class="label"><label>[lang_output]</label></td><td>[obj_output]</td></tr>
	<tr><td class="label"><label>[lang_perfdata]</label></td><td>[obj_perfdata]</td></tr>
	<tr><td class="label"><label>[lang_current_attempt]</label></td><td>[obj_current_check_attempt]/[obj_max_check_attempts]</td></tr>
	<tr><td class="label"><label>[lang_last_check]</label></td><td>[obj_last_check]</td></tr>
	<tr><td class="label"><label>[lang_next_check]</label></td><td>[obj_next_check]</td></tr>
	<tr><td class="label"><label>[lang_last_state_change]</label></td><td>[obj_last_state_change]</td></tr>
	<!-- END host -->
	<tr><td class="spacer" colspan="2"></td></tr>
	<tr><td class="label[obj_summary_state]"><label>[lang_summary_state]</label></td><td class="state[obj_summary_state]">[obj_summary_state] [obj_summary_in_downtime][obj_summary_acknowledged]</td></tr>
	<tr><td class="label"><label>[lang_summary_output]</label></td><td>[obj_summary_output]</td></tr>
	<!-- BEGIN service -->
	<tr><td colspan="2" style="text-align:center;"><img src="/nagios/pnp/index.php?host=[obj_name]&srv=[service_description]&source=1&view=0&display=image" with="300px" height="94px"></td></tr>
	<tr><td class="label"><label>[lang_perfdata]</label></td><td>[obj_perfdata]</td></tr>
	<tr><td class="label"><label>[lang_current_attempt]</label></td><td>[obj_current_check_attempt]/[obj_max_check_attempts]</td></tr>
	<tr><td class="label"><label>[lang_state_type]</label></td><td>[obj_state_type]</td></tr>
	<tr><td class="label"><label>[lang_last_check]</label></td><td>[obj_last_check]</td></tr>
	<tr><td class="label"><label>[lang_next_check]</label></td><td>[obj_next_check]</td></tr>
	<tr><td class="label"><label>[lang_last_state_change]</label></td><td>[obj_last_state_change]</td></tr>
	<!-- END service -->
	<!-- BEGIN childs -->
	<tr><td class="spacer" colspan="2"></td></tr>
	<tr><td colspan="2">
		<table width="100%">
			<tr>
			<!-- BEGIN servicegroup --><td class="label"><label>[lang_child_name1]</label></td><!-- END servicegroup --><td class="label"><label>[lang_child_name]</label></td><td class="label"><label>[lang_state]</label></td><td class="label"><label>[lang_output]</label></td>
			</tr>
			<!-- BEGIN loop_child -->
			<tr><!-- BEGIN servicegroup_child --><td>[obj_name1]</td><!-- END servicegroup_child --><td>[obj_name]</td><td class="state[obj_summary_state]">[obj_summary_state][obj_summary_in_downtime][obj_summary_acknowledged]</td><td>[obj_summary_output]</td></tr>
			<!-- END loop_child -->
		</table>
	</td></tr>
	<!-- END childs -->
</table>
```
