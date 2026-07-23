---
title: "Thruk"
date: 2015-10-22T11:46:15-03:00
---

## Introducción
**Thruk Monitoring** - Es una interfaz web de monitoreo para Nagios, Icinga, Shinken y Naemon. Utiliza como backend MK Livestatus. Está diseñado para ser un reemplazo 'completo' y cubre casi el 100% de los elementos originales. Además añade mejoras adicionales para grandes instalaciones y una mayor facilidad de uso.

**Características principales / Ventajas**

- Múltiples backends
- Más rápido, sin necesidad de analizar status.dat para cada petición
- Menos uso de la CPU
- Mostrar los datos en vivo, no hay retraso entre el núcleo y la interfaz gráfica de usuario
- Independiente del núcleo de monitoreo, se puede instalar en un host remoto
- Clusterizable, se pueden agrupar hosts
- Cambiar el filtro status.cgi línea
- Búsqueda extendida archivo de registro
- Mejor registro, utiliza Log4perl
- Código HTML válido
- No hay marcos HTML
- Fácil de ampliar con plugins
- 100% Perl (usando catalizador marco)
- Paginación
- Múltiples temas incluidos
- Exportación a Excel para el estado y los archivos de registro
- Menú lateral ajustable
- CLI plugins para testeo
- Guardar búsquedas en marcadores personales
- Herramienta de configuración incluido
- Interfaz móvil incluido
- SLA informes/reportes en formato PDF
- Tiempos de parada Recurrente
- Dashboard completo
- Business Process Addon
### Como trabaja
Thruk está escrito en Perl usando el [Framework Catalyst](http://www.catalystframework.org/). Como Backend se conecta por medio del módulo Perl [Monitoring::Livestatus](http://search.cpan.org/dist/Monitoring-Livestatus). Thruk se ejecuta como un proceso fastcgi. La Disponibilidad se calcula con el módulo [Monitoring::Availability](http://search.cpan.org/dist/Monitoring-Availability). La autenticación es proporcionada por el servidor web Apache (ejemplo mod_auth_basic, mod_auth_ldap, mod_auth_mysql, …​).
#### Arquitectura
<img src="/manuales/nagios/thruk/arch_preview.png" alt="" class="align-center" />
#### Soporte multisitio
Thruk conecta con tantos sistemas de monitoreo como quiera (pueden ser múltiples instancias de Nagios etc). Incluso se puede combinar instancias Naemon, Nagios, Icinga y Shinken en una vista combinada grande. De esta manera se obtiene la ventaja de varios lugares independientes y todavía tiene una visión general acerca de todas sus instalaciones. Los sitios pueden ser puestos en grupos para una mejor disposición.
<img src="/manuales/nagios/thruk/multi_site.png" alt="" class="align-center" />
#### Dashboard
El plugin Panorama ofrece una solución flexible para generar un tablero/dashboard. Cada usuario puede crear sus propios cuadros de mando con unos pocos clics y compartirlos con sus colegas. Varios elementos y fondos e imágenes se pueden posicionar libre sin ningún tipo de restricciones. Múltiples Dashboards pueden ser unidos entre sí y conectados a las acciones. Esto permite crear un centro de control propio.
<img src="/manuales/nagios/thruk/dashboard.png" alt="" width="900" class="align-center" />
<img src="/manuales/nagios/thruk_panorama.png" alt="" class="align-center" />

#### Reportes SLA
El plug-in 'reportes' crea informes SLA completos en formato HTML y PDF que se pueden enviar por correo electrónico a intervalos regulares. Además de que puede convertir cualquier página Thruk en un informe periódico vía E-Mail.
<img src="/manuales/nagios/thruk/sla_reporting.png" alt="" class="align-center" />
#### Integración de procesos de negocio BP
El complemento de procesos de negocio permite modelar sus aplicaciones y procesos de negocio. Editor y notificaciones en línea incluidos.
<img src="/manuales/nagios/thruk/bp.png" alt="" class="align-center" />
#### Exportación Excel
La función Favoritos le permite guardar sus búsquedas y añadirlos como su elemento de menú personal. Además de los marcadores, se puede ajustar el menú fácilmente por medio de la configuración en *menu_local.conf* para todos los usuarios. La exportación de Excel crea archivos reales de Excel que se pueden enviar por correo o utilizarlos para extraer los nombres de host ú otra información.
<img src="/manuales/nagios/thruk/bookmarks_and_excel_export.png" alt="" class="align-center" />
#### Herramienta de configuración
La herramienta de configuración hace que sea muy fácil de cambiar la configuración de objetos de monitoreo. También permite realizar cambios rápidos en la configuración de Thruk, así como la gestión de acceso de los usuarios a través de archivos htpasswd o ajustar el archivo cgi.cfg.
<img src="/manuales/nagios/thruk/config_tool.png" alt="" class="align-center" />
#### Envío de múltiples comandos
Se pueden enviar varios comandos a la vez. Incluso es posible enviar comandos de **host** y de **servicio** al mismo tiempo. Cuando se reprogramen los chequeos de hosts y servicios, Thruk esperará hasta que su chequeo sea terminado y mostrará el resultado tan pronto como el chequeo vuelva.
<img src="/manuales/nagios/thruk/reschedule.png" alt="" class="align-center" />
#### Filtrado simple
Con Thruk es posible cambiar rápidamente su filtro de visualización. Se pueden combinar los filtros múltiples para crear tipos de vistas. También se soporta una búsqueda en Ajax, por lo que no es necesario adivinar los nombres de host o de servicio, además de utilizar el poder de las expresiones regulares.
<img src="/manuales/nagios/thruk/filter.png" alt="" class="align-center" />
#### PNP4Nagios
Cuando su action_url contiene /pnp4nagios/, habrá automáticamente un gráfico en pantalla. Esto otorga una vista rápida sobre el historial de rendimiento. La imagen se vincula a pnp4nagios para obtener información detallada.
<img src="/manuales/nagios/thruk/pnp4nagios.png" alt="" class="align-center" />
#### Múltiples líneas en la salida del plugin
Cuando su chequeo devuelve varias líneas de salida. Thruk marca la salida en color azul y con un clic sobre ella muestra la salida completa. Esto es especialmente útil para los controles check_multi. Además de eso, los comentarios y los tiempos de parada también tienen una pequeña ventana emergente con sus datos. Así no es necesario abrir la página de host / servicio sólo para ver quien estableció un comentario y cuando hay un tiempo de inactividad o caída.
<img src="/manuales/nagios/thruk/pluginoutput.png" alt="" class="align-center" />
#### Mine Map
El Mine Map es la herramienta perfecta para obtener una visión general rápida. Es especialmente útil si se tiene una gran cantidad de servicios comunes a través de los hosts. De lo contrario usar se pueden utilizar hostgroups o servicegroups para obtener buenos resultados. Un filtrado normal es posible también.
<img src="/manuales/nagios/thruk/minemap.png" alt="" class="align-center" />
#### Interfaz Móvil
La interfaz móvil da acceso a las cosas más importantes y permite una vista rápida para reconocer problemas.
<img src="/manuales/nagios/thruk/mobile.png" alt="" class="align-center" />
## Instalacion
Hay diferentes formas de instalar Thruk
### Por medio de paquetes
Se puede realizar una instalación desde paquetes binarios, es la forma más simple de instalar Thruk.

Hay que descargar los paquetes desde la [página de descargas](http://www.thruk.org/download.html).

Todos los paquetes tienen esta estructura de directorios :

| /etc/thruk | Thruks config |
| --- | --- |
| /etc/httpd/conf.d/thruk.conf | Apache config |
| /usr/share/thruk | shared files |
| /usr/lib/thruk/perl5 | Perl libraries |
| /var/cache/thruk | temporary files |
| /var/lib/thruk | stored user settings |

Luego de la instalación, Thruk estará disponible en **http://your_host/thruk/** y tiene un usuario predeterminado 'thrukadmin' con la contraseña 'thrukadmin' configurada. Puede que tenga que cambiar la configuración del servidor. Esto se debe hacer desde el archivo de configuración thruk_local.conf donde todos los ajustes se pueden modificar.

#### Debian / Ubuntu
```
#> dpkg -i thruk_1.84_debian6_amd64.deb
```


En caso de errores de dependencia, ejecutar 'apt-get -f install' y reintentar la orden 'dpkg -i ...'.
#### Centos / Redhat
```
#> yum install --nogpgcheck thruk-1.84-1.rhel6.x86_64.rpm
```


Puede que haya que incluir un repositorio externo para el módulo mod_fastcgi. Epel o rpmforge deberían tenerlo.
#### SLES
```
#> zypper install thruk-1.84-1.sles11.x86_64.rpm
```


Es posible que necesite el DVD SDK SLES para incluir dependencias adicionales.
### Por medio de código fuente
Una instalación desde el código fuente, a veces puede ser útil si tenemos requisitos especiales de los usuarios.
#### Requerimientos
Para instalar Thruk desde el código fuente, necesitaremos cumplir las siguientes dependencias en nuestro sistema :
- Perl
- Cliente GIT
- Herramientas de compilación: automake, make, g++, gcc
- Apache Webserver (opcional solo para fastcgi)
#### Creación de un usuario de ejecución
Deberemos crear los siguientes usuarios y grupos
- **Usuario:**  thruk
- **Grupo:** thruk
#### Instalar librerías de Thruk
Todos los módulos Perl requeridos están disponibles desde el repositorio *thruk_libs*.


```
%> su - thruk
%> cd /tmp
%> git clone git://github.com/sni/thruk_libs.git
%> cd /tmp/thruk_libs
%> make
```


Ahora todos los módulos requeridos se encuentran en **/tmp/thruk_libs/local-lib/dest**.
#### Git Clone Thruk
```
%> cd /tmp
%> git clone https://github.com/sni/Thruk.git
%> cd /tmp/Thruk
%> ./configure
%> make
```


#### Mover las librerías de Perl
Deberemos mover las librerías al directorio principal de Thruk


```
%> mv /tmp/thruk_libs/local-lib/dest/lib/perl5 perl5 ~/Thruk/
```


Deremos agregar lo siguiente al *perfil de usuario*, generalmente dentro del archivo *.profile*.


```bash>
Luego deremos reiniciar la sesión de usuario y verificar que la variable **$PERL5LIB** contiene la ruta correcta.

Finalmente podemo verificar si thruk correra correctamente por medio del siguiente comando :
<code>
%> cd ~/Thruk && ./script/thruk
```


O corriendo el servidor de desarrollo :


```
%> cd ~/Thruk && ./script/thruk_server.pl
```


Deberemos abrir el navegador y acceder a **http://your_host:3000**
### Configuración
Deberemos copiar el archivo *thruk.conf* a *thruk_local.conf* y ajustar las opciones de livestatus que necesitemos.


```
%> cp thruk.conf thruk_local.conf
```


El archivo *thruk.conf* a menudo sera re-escrito cuando se apliquen actualizaciones. En cambio el archivo thruk_local.conf contiene las configuraciones locales que están por sobre la demás y nunca será sobre-escrito.

Luego habrá que editar el archivo *cgi.conf* y ajustar las configuraciones que se crean necesarias.

## Configuracion
### Configuración de Thruk
La configuración de Thruk es principalmente administrada en estos archivos de configuración :

| thruk.conf | Configuración **default** principal de Thruk |
| --- | --- |
| thruk_local.conf | Configuración **local** principal, sobreescribe la **default** |
| cgi.cfg | Naemon/Nagios cgi.cfg |
| log4perl.conf | Configuración de logging |
| menu.conf | Configuración **default** de menú de navegación de Thruk |
| menu_local.conf | Configuración **local** de menú de navegación, sobreescribe la **default** |

**thruk.conf**

El achivos thruk.conf contiene los valores predeterminados. No debería editar este archivo directamente. Se debe sobrescribir la configuración dentro del archivo thruk_local.conf en su lugar.

**thruk_local.conf**

El archivo thruk_local.conf es una copia del archivo thruk.conf. Ambos archivos son utilizados por thruk. La idea es que el archivo thruk.conf proporcione los valores predeterminados y el archivo thruk_local.conf reemplace dichos ajustes cuando sea necesario.

#### Opciones generales
| Valor | Descripción | Ejemplo |
| --- | --- | --- |
| title_prefix | Establecer el prefijo de título para todas las urls, esta parte del texto se antepondrá a todos los títulos de las páginas | title_prefix = Prod |
| use_bookmark_titles | Establece el título de la página con el nombre de un marcador | use_bookmark_titles = 1 |
| use_dynamic_titles | Utiliza títulos de las páginas más descriptivos | use_dynamic_titles = 1 |
| url_prefix | Cambia la ruta URL habitual para Thruk | url_prefix = / |
| use_timezone | Cambia la zona horaria respecto de la predeterminada. Sólo usar esta opción si hay problemas con la visualización de timestamps | use_timezone = CET |
| mobile_agent | Especifica los user-agent que se redireccionarán al plugin móvil (si está activado) | mobile_agent=iPhone,Android,IEMobile |
| default_theme | Tema predeterminado para todos los usuarios | default_theme = Thruk |
| first_day_of_week | Primer día de la semana. Se utiliza en los reportes   
Sunday: 0 Monday: 1 | first_day_of_week = 0 |
| report_use_temp_files | Reportes grandes usarán archivos temporales para evitar el uso de memoria. Con *report_use_temp_files* se puede ajustar la duración en días del reporte que hará uso de archivos temporales. El valor predeterminado es 14 días, así que por ejemplo el informe *last31days* utilizará archivos temporales, el *thisweek* no. Se puede desactivar ajustando a 0. | report_use_temp_files = 14 |
| report_max_objects | No crear informes con más equipos/servicios que este número. El propósito es no sobrecargar el servidor debido a uso de memoria. Aumente este número si es necesario | report_max_objects = 1000 |
| start_page | Este enlace se utilizará como página de inicio | start_page = /thruk/main.html |
| home_link | Este enlace se utiliza cada vez que se hace clic en uno de los principales logotipos. Predeterminadamente los logotipos son de Thruk y el enlace le llevará a la página de Thruk | home_link = http://www.thruk.org |
| documentation_link | Este enlace es utilizado para acceder a los datos de Documentación | documentation_link = /thruk/docs/ |
| all_problems_link | Enlace donde se muestran los problemas detectados | all_problems_link = /thruk/cgi-bin/status.cgi?... |
| allowed_frame_links | Patrones de enlaces permitidos a mostrar en el marco de Thruk | allowed_frame_links = http://intranet.my-company.com   
allowed_frame_links = https://wiki.my-company.com |
#### Opciones de autorización
| Valor | Descripción | Ejemplo |
| --- | --- | --- |
| can_submit_commands | Permitir que enviar comandos por defecto para todos los contactos | can_submit_commands = 1 |
| command_disabled | Deshabilitar comandos específicos. Se puede utilizar múltiples veces para deshabilitar varios comandos.  
El número se puede encontrar en el parámetro CGI 'cmd_typ' en los enlaces de la página de comandos.  
Se pueden utilizar rangos. | command_disabled = 14  
command_disabled = 35  
command_disabled = 17-34,50-65 |
| make_auth_user_lowercase | Convertir nombre de usuario autenticado a minúsculas | make_auth_user_lowercase = 1 |
| make_auth_user_uppercase | Convertir nombre de usuario autenticado a mayúsculas | make_auth_user_uppercase = 1 |
^make_auth_replace_regex|Convertir nombre de usuario autenticado, utilizando expresiones regulares.|Eliminar el @ y todo lo que viene despúes dentro de un nombre de usuario  


```perl>make_auth_replace_regex
|
^use_strict_host_authorization|Configurar para que los usuarios solo vean los hosts/servicios donde estan definidos como contacto|use_strict_host_authorization = 1|
^csrf_allowed_hosts|Permitir a hosts específicos saltear la protección csrf que requiere la generación de un token para ciertos pedidos POST, como enviar comandos|csrf_allowed_hosts=127.0.0.1\\ csrf_allowed_hosts=192.168.*|
^max_process_memory|Máxima memoria a utilizar en MB, solo afecta al demonio fcgid|max_process_memory=1000|
=== Configuración de rutas ===
^Valor^Descripción^Ejemplo^
^cgi_cfg|Ruta al archivo cgi.cfg|cgi_cfg = cgi.cfg|
^log4perl_conf|Archivo de configuración de log4perl|log4perl_conf = ./log4perl.conf|
^plugin_path|Ruta de acceso a los plugins, no debe ser accesible vía web|plugin_path = ./plugins|
^themes_path|Ruta de acceso a los temas, no debe ser accesible vía web|themes_path = ./themes|
^var_path|Ruta del directorio //var// de thruk|var_path = ./var|
^tmp_path|Ruta del directorio de archivos temporales|tmp_path = /tmp|
^ssi_path|Ruta donde se encuentran los archivos SSI (Server Side Includes)|ssi_path = ssi/|
^user_template_path|Directorio adicional de plantillas. Se pueden priorizar fácilmente por sobre las plantillas por default de Thruk.\\ El orden de búqueda es :\\ -> users template path\\ -> themes template path\\ -> plugins template path\\ -> thruks template path|user_template_path = ./my_templates|
^logo_path_prefix|cambia la ruta de acceso a las imágenes de logotipo. El valor predeterminado es $url_prefix+'thruk/themes/'$current_theme'/images/logos/'|logo_path_prefix = /icons/|
^physical_logo_path|Ubicación de sus logos en su sistema de archivos. Este directorio debe tener relación con 'logo_path_prefix' dónde 'logo_path_prefix' es la ruta relativa al directorio raíz del servidor web y 'physical_logo_path' es la ruta del sistema de archivos correspondiente.|physical_logo_path = /usr/share/icons/|
^mode_file|Modo usado para crear o guardar archivos|mode_file = 0660|
^mode_dir|Modo usado para crear directorios|mode_dir = 0770|
^resource_file|Establezca un archivo de recursos en general. Se recomienda solo usar esta opción si no hay contraseñas se utilizan en este archivo o en combinación con la opción 'expand_user_macros' que limitará qué macros están expuestos al usuario.|resource_file = /etc/nagios3/resource.cfg|
=== Opciones de búsqueda ===
^Valor^Descripción^Ejemplo^
^use_new_search|The new search supports regular expressions and searches in many attributes. For example: plugin_output, groups, names, descriptions|use_new_search = 1|
^default_service_filter|Set a default service filter which is used when no other filter is applied (except from links to hosts or groups). The filter is negated by a leading exclamation mark. The example filters out all services starting with "test_". You can use regular expressions. The Default is not set.|default_service_filter = !<html>^</html>test_|
^use_ajax_search|Enables the ajax search field. There will be suggestions while typing into the search field.|use_ajax_search = 1|
^ajax_search_hosts|Enables the suggestion of hosts in the ajax search field. Depending on the number of hosts, this can make the search slow.|ajax_search_hosts = 1|
^ajax_search_hostgroups|Enables the suggestion of hostgroups in the ajax search field.|ajax_search_hostgroups = 1|
^ajax_search_services|Enables the suggestion of services in the ajax search field. Depending on the number of services, this can make the search slow.|ajax_search_services = 1|
^ajax_search_servicegroups|Enables the suggestion of servicegroups in the ajax search field.|ajax_search_servicegroups = 1|
^ajax_search_timeperiods|Enables the suggestion of timeperiods in the ajax search field. Timeperiods will only displayed when filtering by check- or notification period.|ajax_search_timeperiods = 1|

=== Opciones de paginación ===
^use_pager|Using the pager will make huge pages much faster as most people don’t want a services page with 100.000 services displayed. Can be disabled if you don’t need it.|use_pager = 1|
^paging_steps|Define the selectable paging steps. Use the * to set the default selected value.|paging_steps = *100, 500, 1000, all|
^group_paging_overview|Just like the paging_steps, but only for the groups overview page.|group_paging_overview =  *3,  10, 100, all|
^group_paging_summary|Just like the paging_steps, but only for the groups summary page.|group_paging_summary = *10, 50, 100, all|
^group_paging_grid|Just like the paging_steps, but only for the groups grip page.|group_paging_grid = *5,  10, 50,  all|

=== Opciones de pantalla ===
^host_action_icon|Change path to your host action icons. You may use relative paths to specify completely different location. You also may want to use 'action_pnp.png' when using pnp. Icon can be overridden by a custom variable '_ACTION_ICON'.|host_action_icon = action.gif|
^service_action_icon|Change path to your service action icons. You may use relative paths to specify completely different location. You also may want to use 'action_pnp.png' when using pnp. Icon can be overridden by a custom variable '_ACTION_ICON'.|service_action_icon = action.gif|
^initial_menu_state|Set initial menu state.|closed  => 0\\ open    => 1<code>
<initial_menu_state>
  General        = 1
  Current_Status = 1
  Reports        = 1
  System         = 1
  Bookmarks      = 1
</initial_menu_state>
```

|
| use_frames | Set whether you want to use a framed navigation or not. With using frames it’s sometimes easier to include addons. See allowed_frame_links option for how to integrate addons. | use_frames = 0 |
| --- | --- | --- |
| use_new_command_box | Show the new split command box on the host / service details page. | use_new_command_box = 1 |
| bug_email_rcpt | what email address bug reports will be sent to | bug_email_rcpt = bugs@thruk.org |
| datetime_format | Default timeformat. Use POSIX format. | datetime_format = %Y-%m-%d  %H:%M:%S |
| datetime_format_long | Default long timeformat. | datetime_format_long = %a %b %e %H:%M:%S %Z %Y |
| datetime_format_log | Default log timeformat. | datetime_format_log = %B %d, %Y  %H |
| datetime_format_trends | Default trends timeformat. | datetime_format_trends = %a %b %e %H:%M:%S %Y |
| datetime_format_today | Default timeformat for todays date. Can be useful if you want a shorter date format for today. | datetime_format_today = %H:%M:%S |
| info_popup_event_type | On which event should the comments / downtime or longpluginoutput popup show up. Valid values are onclick or onmouseover. | info_popup_event_type = onmouseover |
| info_popup_options | Options for the popup window used for long pluginoutput, downtimes and comments.  
See http://www.bosrup.com/web/overlib/?Command_Reference for what options are available | info_popup_options = STICKY,CLOSECLICK,HAUTO,MOUSEOFF |
| show_notification_number | Display the current number of notification after the current / max attempts on the status details page. | show_notification_number = 0 |
| show_backends_in_table | Display the backend/site name in the status table. This is useful if you have same hosts or services on different backends and need to know which one returns an error. | Valid values are:  
1 - show site name at the end  
2 - put site name in front  
  
show_backends_in_table = 0 |
| show_config_edit_buttons | Show links to config tool for each host / service. You need to have the config tool plugin enabled and you need proper permissions for the link to appear. | show_config_edit_buttons = 1 |
| show_full_commandline | Display the full command line for host / service checks . Be warned, the command line could contain passwords and other confidential data. In order to replace the user macros for commands, you have to set the 'resource_file' in your peer config or a general resource_file option.  
0 = off, don’t show the command line at all  
1 = show them for contacts with the role: authorized_for_configuration_information   
2 = show them for everyone | show_full_commandline = 0 |
| show_full_commandline_source | Usually the source of your expanded check_command should be the check_command attribute of your host / service. But under certain circumstances you might want to use display expanded commands from a custom variable. In this case, set 'show_full_commandline_source' to '_CUST_VAR_NAME'. | show_full_commandline_source = check_command |
| show_logout_button | Show additional logout button next to the top right preferences button. (works only together with cookie authentication) | show_logout_button=1 |
| show_long_plugin_output | When a plugin returns more than one line of output, the output can be displayed directly in the status table, as popup or not at all. Choose between popup, inline and off | show_long_plugin_output = popup |
| status_color_background | Color complete status line with status colour or just the status itself. | status_color_background=0 |
| show_modified_attributes | Show if a host / service has modified attributes. | show_modified_attributes = 1 |
| show_host_attempts | Show check attempts for hosts too. The default is to show them on the problems page only. Use this value to force a value. | show_host_attempts = 1 |
| perf_bar_mode | This option enables a performance bar inside the status/host list which create a graph from the performance data of the plugin output. Available options are 'match', 'first', 'all', 'worst' and 'off'.  
match: try to set graph which matches the output  
all: graph all performance values available  
first: graph only the first performance value  
worst: graph only the graph for the worst state  
off: graph no value at all | perf_bar_mode = match |
| perf_bar_pnp_popup | Show pnp popup if performance data are available and pnp is used as graph engine. | perf_bar_pnp_popup = 1 |
| shown_inline_pnp | Show inline pnp graph if available. If a service or host has a pnp4nagios action or notes url set.  
Thruk will show a inline graph on the extinfo page.  
This works for /pnp4nagios/ urls and /pnp/. | shown_inline_pnp = 1 |
| graph_word | graph_word is a regexp used to display any graph on the details page. if a service or host has a graph url in action url (or notes url) set it can be displayed by specifying a regular expression that always appears in this url. You can specify multiple graph_words.When using pnp4nagios, no graph_word is required, just keep it empty. | graph_word = /render/ # for graphite |
| show_custom_vars | Show custom vars in host / service ext info. List variable names to display in the host and service extinfo details page. Can be specified more than once to define multiple variables. You may use html in your variables. Use * as wildcard, ex.: _VAR* | show_custom_vars = _VAR1 |
| expand_user_macros | Expand user macros ($USERx$) for host / service commands and custom variables.  
Can be specified more than once to define multiple user macros to expand.  
Be warned, some user macros can contain passwords and expanding them could expose them to unauthorized users. Use * as wildcard, ex.: USER*  
Defaults to 'ALL' which means all user macros are expanded, because its limited to admin users anyway. | expand_user_macros = USER1  
expand_user_macros = USER10-20  
expand_user_macros = PLUGIN*  
expand_user_macros = ALL  # expands all user macros  
 expand_user_macros = NONE # do not expand user macros |
| show_error_reports | Show link to bug reports when internal errors occur. Set to '1' to show a error icon which links to a error report mail. Set to 'server' to log js error server side. Set to 'both' to log server side but still show the icon. | show_error_reports = both |
| skip_js_errors | don’t report some known harmless javascript errors | skip_js_errors = cluetip is not a function |
| strict_passive_mode | Normally passive checks would be marked as disabled.  
With this option set, disabled checks will only be displayed as disabled if their last result was active. Otherwise they would be marked as passive checks.  
This option also changes the passive icon only to be shown when the last check was passive, otherwise the disabled icon will be displayed. | strict_passive_mode = 1 |
| hide_passive_icon | Normally passive checks whould be displayed with a passive icon if their last result is passive. With this option, passive icon will be hidden in status details. | hide_passive_icon = 0 |
| sitepanel | The sitepanel is used to display multiple backends/sites at a glance. With than 10 or more sites, the list of backends will be combined into the 'compact' site panel which just displays the totals of available / down / disabled sites. The 'compact' panel will also automatically be used if you use sections. With more than 50 backends, the 'collapsed' will be selected in 'auto' mode. Set sitepanel to list/compact/collapsed/auto/off to change the default behaviour. | sitepanel = auto |
| apache_status | You can integrate the output of apache status into Thruk. The following list of apache status pages will be accessible from the performance info page.  
Make sure the page is accessible from Thruk, credentials will be passed through. So both, basic authentication or ip based authentication would be possible.  
Read more about Apaches mod_status here: http://httpd.apache.org/docs/current/mod/mod_status.html |

```
<apache_status>
  Site        http://127.0.0.1:5000/server-status
  System      http://127.0.0.1/server-status
  Other       http://127.0.0.1/server-status
</apache_status>
```

|

#### Opciones de conexión
#### Cookie Authentication Settings
#### Command Settings
#### Cron Settings
#### Action Menu Settings
#### Icinga Settings
#### Shinken Settings
#### Other Settings
#### Component Thruk::Backend
#### Component Thruk::Plugin::ConfigTool
#### Component Thruk::Plugin::Panorama
#### Component Thruk::Plugin::Reports2
#### Component Thruk::Plugin::Statusmap
#### Component Thruk::Plugin::Minemap
#### Component Thruk::Plugin::BP
#### User & Group Specific Overrides
### Configuración de CGI
## FAQ
## Plugins
## Themas
## Avanzado
### Business Process
### Dashboard
### Reportes de SLA
#### Como trabajan
La generación de los reportes SLA consisten en :
- Configuración del reporte
- Plantilla del reporte
- Convertir el reporte HTML en PDF
- Opcionalmente ser envíados por email
#### Configuración del reporte
Se pueden crear y ajustar las opciones del reporte en la sección de **Reportes**. El plugin de reportes debe estar habilitado. Luego se debe seleccionar una plantilla para el reporte. Luego de seleccionar la plantilla hay opciones específicas.
<img src="/manuales/nagios/thruk/new_report.png" alt="" class="align-center" />
<img src="/manuales/nagios/thruk/new_report_options.png" alt="" class="align-center" />
#### Plantillas de reporte
Todas las plantillas usadas para los reportes se almacenan en la siguiente ubicación 'plugins/plugins-enabled/reports2/templates/reports'. Se pueden ajustar o sobreescribir todas las plantillas que sean necesarias dentro del subdirectorio 'reports' o se puede establecer por medio de la opción 'user_template_path'.
| Nomenclatura de directorios del agregado de reportes |   |
| --- | --- |
| reports | Contiene los reportes seleccionables |
| comp | Contiene los componentes utilizados por los reportes |
| locale | Contiene las opciones de localización |
| style | Contiene las hojas de estilo CSS |
Las plantillas fueron desarrolladas utilizando **Template::Toolkit** documentado en : http://template-toolkit.org/docs/manual/index.html
#### Conversión
El reporte generado en HTML, puede ser convertido a PDF utilizando **wkhtml2pdf** con **xvfb**. Ambas herramientas deben ser instaladas en el mismo equipo que Thruk. Hay una descarga en formato binario de **wkhtml2pdf** disponible en : http://code.google.com/p/wkhtmltopdf/. Utilizar una version igual o mayor a 0.11.
#### Envío de reportes por email
Se puede enviar fácilmente reportes de disponibilidad SLA por email. Lo único que se necesita es establecer las opciones de envío en la configuración. También es posible enviar un mismo reporte múltiples veces.
#### Customized Reports
Los reportes pueden ser personalizados por medio de HTML y CSS. Los reportes en general tiene bloques específicos que detallan los pasos para su producción.
- styles
- edit
- prepare
- render
- mail
#### Hojas de estilo
Personalizar las hojas de estilos CSS es realmente simple. Para ellos es necesario crear un un archivo 'reports/style/custom.tt' dentro de 'user_template_path'.

Ejemplo :


```html>
/* override css styles */
BODY {
    font-family: arial, sans-serif;
    font-size: 11px;
    line-height: 18px;
    font-size: 16px;
}
</style>

<script language="javascript" type="text/javascript">
/* add complete new elements by javascript */
  jQuery(document).ready(function() {
    jQuery('<img src="' + url_prefix + 'themes/Thruk/images/logo_thruk.png">').insertBefore('DIV.page.cover DIV.titletext');
  });
</script>
```


Si eso no es suficiente, además se puede cambiar cada plantilla utilizada para crear los reportes.
#### Bloque de edición
El bloque de edición contiene elementos de configuración utilizados para la generación del reporte. El bloque es el encargado de definir la página de configuración que se presenta cuando alguien elige crear o editar un reporte de este tipo. Básicamente este bloque contiene todos los campos obligatorios y opcionales utilizados para el reporte.

Ejemplo :


```bash>
 [ 'Host',        'hosts',         ''              , '', 1 ] },
        { 'timeperiod' => [ 'Timeperiod',  'rptimeperiod',  'last12months'          ] },
    ];
  %]
[% END %]
```


La clave en hash define el parámetro que definirá dicho valor. Cada campo tiene que apuntar a una matriz con al menos 3 valores. La opción de **requerido** es opcional.
- Nombre
- Tipo
- Valor por defecto
- Opción de requerido
#### Bloque de preparación
El bloque de preparación se utiliza para recopilar datos utilizados en el reporte. Por lo general, se ha utilizado para obtener datos sobre la disponibilidad a través livestatus o recuperar eventos de los archivos de registro.

Ejemplo :


```bash>
Se pueden utilizar las funciones disponibles en http://www.thruk.org/api/Thruk/Utils/Reports/Render.html
=== Bloque de renderizado ===
El bloque de renderizado define la disposición del reporte.

Ejemplo :
<code bash>
[% IF block == 'render' %]
  [% PROCESS 'reports/locale/en.tt' %]
  [%
    title        = 'Hello World Report'
    subtitle     = r.name
    coverdetails = [ 'Report Timeperiod:', get_report_timeperiod(start, end, reportDateFormat),
                     'Host:', param.host,
                   ]
  %]

  [% WRAPPER 'reports/comp/report.tt' %]
    [% WRAPPER 'reports/comp/page_cover.tt' %][% END %]
    [% WRAPPER 'reports/comp/page_standard.tt' %]
      <b>hello world</b>
    [% END %]
  [% END %]
[% END %]
```


Este reporte contendrá dos páginas. En primero lugar dentro de report.tt definirá el diseño básico del reporte y contendrá las hojas de estilo. Entonces se deberá definir algunos parámetros utilizados por la portada. '**r**' es una referencia al objeto del informe, en este ejemplo solo se utiliza como título secundario. '**converdetails**' es una lista de nombres/valores, que se utiliza en los detalles a mostrar en la portada. Finalmente tenemos 'Hello World Report' en una página standard. El contenido puede tener etiquetas HTML estándar, como tablas e imágenes también.
#### Bloque de email
Este bloque define la imagén visual que tendrá el email en el cual se adjuntará el reporte a enviar.

Ejemplo :


```bash>
Utilizamos el nombre del reporte '**r.name**' como **asunto** y ponemos la descripción del reporte '**r.desc**' en el cuerpo del mensaje.
=== Renderizado personalizado ===
Es posible crear un módulo en Perl llamado '**Thruk::Utils::Reports::CustomRender**', que estará disponibile automáticamente, por lo que las funciones de dicho módulo se podrán utilizar en las plantillas de reportes.
==== Menu Customizing ====
==== Action Menu ====
==== JSON Exporting ====
==== Logfile Cache ====
==== Logfiles ====
==== Server Side Includes ====
==== CLI & Scripting ====
=== Herramienta de línea de comandos ===
**thruk** - Utilidad de línea de comandos para la GUI de Monitoreo Thruk

Este script da acceso a diversas partes de la GUI Thruk y permite generar informes de Excel, exportación CSV o cualquier otra URL usando la línea de comandos y sin pasar por el servidor web. Con esta herramienta se hace que sea fácil la forma de enviar reportes periódicos por cron/mail.

Lista de backends disponibles:
<code>
%> thruk -l
```


Exportar el log de evento a un archivo excel:


```
%> thruk -A thrukadmin -a 'url=/thruk/cgi-bin/showlog.cgi?view_mode=xls' > eventlog.xls
```


Las urls pueden ser acortadas. Exportar toda la información de servicios de un archivo Excel:


```
%> thruk 'status.cgi?view_mode=xls&host=all' > allservices.xls
```


Exportar datos de disponibilidad de servicio a un archivo CSV:


```
%> thruk -A thrukadmin -a 'url=avail.cgi?host=all&timeperiod=last7days&csvoutput=1' > all_host_availability.csv
```


Generar reporte pdf:


```
%> thruk -a 'report=1' > report.pdf
```


Generar reporte pdf y enviarlo por email:


```
%> thruk -a 'reportmail=1'
```


Re-programar el próximo chequeo para el host localhost para ahora:


```
%> thruk 'cmd.cgi?cmd_mod=2&cmd_typ=96&host=localhost&start_time=now'
```


### CGI Parameter
### Modified Attributes
### Benchmarks
### API
**Thruk::Utils::CLI** - Utilidades Colección de secuencias de comandos CLI. Permiten acceder a las estructuras internas y cambiar la información de configuración.

Rutas de búsqueda de librerias para el paquete Thruk :


```
/usr/share/thruk/lib
/usr/share/thruk/plugins/plugins-available/conf/lib
/usr/lib/thruk/perl5
```


Ruta de la configuración del Framework Catalyst :


```perl>
Inclusión de los valores de configuración :
<code perl>
use lib qw(/usr/share/thruk/lib /usr/share/thruk/plugins/plugins-available/conf/lib /usr/lib/thruk/perl5);
$ENV{ CATALYST_CONFIG } = "/etc/thruk";
```




```perl>
 0-2,         # be more verbose
    credential      => 'secret',    # secret key when accessing remote instances
    remoteurl       => 'url',       # url where to access remote instances
    local           => 0|1,         # local requests only
 }
```



Scripts de ejemplo


```perl
#!/usr/bin/perl
use Thruk::Utils::CLI;
my $cli   = Thruk::Utils::CLI->new;
my $odb   = $cli->get_object_db();
my $hosts = $odb->get_objects_by_type('host');
printf("%-20s   %s\n", "Host", "File");
print("===========================================================\n");
for my $h (@{$hosts}) {
    printf("%-20s   %s:%d\n", $h->get_primary_name(), $h->{'file'}->{'display'}, $h->{'line'});
}
```




```perl
#!/usr/bin/perl
use Thruk::Utils::CLI;
my $states = {
    '0' => 'Ok',
    '1' => 'Warning',
    '2' => 'Critical',
    '3' => 'Unknown',
};
my $cli      = Thruk::Utils::CLI->new;
my $db       = $cli->get_db();
my $services = $db->get_services();
printf("%-20s   %-30s %-10s\n", "Host", "Service", "Status");
print("===============================================================\n");
for my $s (@{$services}) {
    printf("%-20s   %-30s %-10s\n", $s->{'host_name'}, $s->{'description'}, $states->{$s->{'state'}});
}
```



**contacts2csv /tmp/object.cache > /tmp/contacts.csv**


```perl
#!/usr/bin/perl
use Pod::Usage;
use Monitoring::Config;

if(!defined $ARGV[0]) {
    pod2usage( { -verbose => 2,  -exit => 3, -message => "\nERROR: missing argument: object.cache\n\n" } );
}
if(!-f $ARGV[0]) {
    pod2usage( { -verbose => 2,  -exit => 3, -message => "\nERROR: ".$ARGV[0].': '.$!."\n\n" } );
}

my $odb      = Monitoring::Config->new({ obj_file => $ARGV[0], 'force' => 1 })->init();
my $contacts = $odb->get_objects_by_type('contact');
my $groups   = $odb->get_objects_by_type('contactgroup');
my $def      = $odb->get_default_keys('contact', { no_alias => 1, sort => 1 });

# convert groups
my $groups_by_contact = {};
for my $g (@{$groups}) {
    my $name = $g->{'conf'}->{'contactgroup_name'};
    for my $m (@{$g->{'conf'}->{'members'}}) {
        $groups_by_contact->{$m}->{$name} = 1;
    }
}

# print header
print join(';', map(ucfirst, @{$def})), ";Groups\n";

# print data
for my $o (@{$contacts}) {
    for my $attr (@{$def}) {
        if(!defined $o->{'conf'}->{$attr}) {
            print ";";
        } else {
            if(ref $o->{'conf'}->{$attr} eq 'ARRAY') {
                print join(',', @{$o->{'conf'}->{$attr}}), ";";
            } else {
                print $o->{'conf'}->{$attr}, ";";
            }
        }
    }
    my $name = $o->{'conf'}->{'contact_name'};
    print join(',', keys %{$groups_by_contact->{$name}});
    print "\n";
}
```



**objectcache2csv /tmp/object.cache host > /tmp/hosts.csv**   
**objectcache2csv /tmp/object.cache service > /tmp/hosts.csv**


```perl
#!/usr/bin/perl
use Pod::Usage;
use Monitoring::Config;

if(!defined $ARGV[0]) {
    pod2usage( { -verbose => 2,  -exit => 3, -message => "\nERROR: missing argument: object.cache\n\n" } );
}
if(!defined $ARGV[1]) {
    pod2usage( { -verbose => 2,  -exit => 3, -message => "\nERROR: missing argument: object type\n\n" } );
}
if(!-f $ARGV[0]) {
    pod2usage( { -verbose => 2,  -exit => 3, -message => "\nERROR: ".$ARGV[0].': '.$!."\n\n" } );
}

my $odb  = Monitoring::Config->new({ obj_file => $ARGV[0], 'force' => 1 })->init();
my $objs = $odb->get_objects_by_type($ARGV[1]);
my $def  = $odb->get_default_keys($ARGV[1], { no_alias => 1, sort => 1 });

# print header
print join(';', map(ucfirst, @{$def})), "\n";

# print data
for my $o (@{$objs}) {
    my $first = 1;
    for my $attr (@{$def}) {
        if(!defined $o->{'conf'}->{$attr}) {
            print ";" unless $first;
            $first = 0;
        } else {
            if(ref $o->{'conf'}->{$attr} eq 'ARRAY') {
                print join(',', @{$o->{'conf'}->{$attr}}), ";";
            } else {
                print $o->{'conf'}->{$attr}, ";";
            }
        }
    }
    print "\n";
}
```



## Acerca de la Guía
Esta guía **no oficial** esta inspirada en la documentación original de Thruk. Está pensada para ir presentando casos de uso que se van dando en la operatoria día a dia. 

En principio es casi una copia de la original, con algunas partes traducidas y otras no. La idea es que con el tiempo y poniendo códigos y experiencias personales.

Thruk es un proyecto muy bueno que me ayuda en mostrar mi trabajo día a día y tiene una comunidad muy activa. Si estas leyendo esto y estas siendo beneficiado por Thruk, te invito a realizar una [donación al proyecto](http://www.thruk.org/donate.html)
