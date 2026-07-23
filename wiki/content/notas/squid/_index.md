---
title: "Squid"
date: 2018-11-27T10:58:56-03:00
---

Squid es un popular programa de software libre que implementa un servidor proxy y un dominio para caché de páginas web, publicado bajo licencia GPL. Tiene una amplia variedad de utilidades, desde acelerar un servidor web, guardando en caché peticiones repetidas a DNS y otras búsquedas para un grupo de gente que comparte recursos de la red, hasta caché de web, además de añadir seguridad filtrando el tráfico. Está especialmente diseñado para ejecutarse bajo entornos tipo Unix.

Squid ha sido desarrollado durante muchos años y se le considera muy completo y robusto. Aunque orientado a principalmente a HTTP y FTP es compatible con otros protocolos como Internet Gopher. Implementa varias modalidades de cifrado como TLS, SSL, y HTTPS.

## Delay pools

Por ejemplo si tengo usuarios que abusan del uso de internet, dado el caso de que bajan una iso y la dejan horas agotando el ancho de banda, lo que podemos hacer con Squid, es que cuando llegen a un tope, por ejemplo 15MB el ancho de banda baje considerablemente para todo el segmento de red que pasa por squid.

Ejemplo si un usuario baja un archivo ISO, cuando llega a los 15MB la velocidad baja a 16k/s para todo el segmento

```
delay_pools          1
delay_class          1       1
delay_parameters     1       16384/15728640
acl ficheros_iso     url_regex       \.iso$
delay_access         1       allow   ficheros_iso
```

Se puede hacer tambien

**acl restringir url_regex -i “/etc/squid/extensions”**

```
\.ace$

\.af$

\.afx$

\.arj$

\.asf$

\.asx$

\.au$

[...]

\.xla$

\.xls$

\.xlt$

\.xlw$

\.z$

\.zip$ 

youtube.com
```

Delay pool clase 2 para afectar a usuarios individuales

```
delay_pools          1
delay_class          1 2
delay_parameters     1 -1/-1 102400/3000
delay_access         1 allow ficheros_iso
```

Ejemplo de Delay pool por horarios

```
acl winupdate dstdomain .windowsupdate.com
acl peakperiod time 10:00-16:00
delay_pools 1
delay_class 1 1
# 64 Kbit/s
delay_parameters 1 8000/8000
delay_access 1 allow winupdate peakperiod
```

Sacado de : <http://www.linux.com/archive/feature/153221>

Otros ejemplos de delay pools

```
# 157 kB/s
delay_pools 1
delay_class 1 2
delay_parameters 1 98304/131072 49152/131072
delay_access 1 allow all ancho_de_banda
```

```
# 8 kB/s
delay_pools 1
delay_class 		1 1
delay_parameters 	1 8192/8192
delay_access 1 allow all ancho_de_banda
```

```
# 32 kB/s
delay_pools 1
delay_class             1       2
delay_parameters	1 	32768/32768 16384/32768
```

```
# 50 kB/s
delay_pools 1
delay_class             1       2
delay_parameters	1 	65536/65536 32768/65536
delay_access 1 allow all ancho_de_banda
```

Ejemplo de dos proxys hermanos (sibling) en una red local que comparten cache y ademas tiene dif parent proxys cada uno con prioridad cruzada

**squid.conf**

```
include squid-peers.conf
```

*squid-peers.conf* - proxy1

```
cache_peer 10.1.1.2      sibling 8080 3130 proxy-only no-delay
cache_peer 192.168.1.2   parent 8080 0 weight=100
cache_peer 192.168.1.3   parent 8080 0 weight=10
```

*squid-peers.conf* - proxy2

```
cache_peer 10.1.1.3      sibling 8080 3130 proxy-only no-delay
cache_peer 192.168.1.3   parent 8080 0 weight=100
cache_peer 192.168.1.2   parent 8080 0 weight=10
```

## Bloquear MSN apto para 7 y 8

MSN Messenger 7 y MSN 8 tienen a buscar por todo lados como conectarse, entonces esto se vuelve algo medio problematico con Squid y mas cuando tenemos muchos grupos LDAP configurados y queremos que algunos si puedan acceder etc. Mi solución por el momento es :

```
acl MSN_Method 	method 		POST
acl MSN_Agent 	browser 	^Mozilla.compatible;.MSN Messenger
acl MSN_Agent 	browser 	^Mozilla/4\.0.*MSIE.6\.0.MSN
acl MSN_Mime 	req_mime_type 	^application/x-msn-messenger$
acl MSN_Port 	port 		1863
acl MSN_Dom 	dstdom_regex -i messenger.hotmail.com 207.46.110.40 gateway.messenger.hotmail.com msn.com local-bay.contacts.msn.com messenger.live.com proxy-sn.contacts.msn.com proxy-bay.contacts.msn.com gateway.edge.messenger.live.com bay.messenger.services.live.com geo.messenger.services.live.com
acl MSN_Url 	url_regex -i 	gateway.dll sqmserver.dll ADSAdClient31.dll gateway.dll?
acl MSN_Usr     ext_user        usuario1 usuario2 usuario3
```

```
http_access       deny MSN_Agent MSN_Mime MSN_Dom MSN_Url !MSN_Usr
http_reply_access deny MSN_Agent MSN_Mime MSN_Dom MSN_Url !MSN_Usr

http_access       deny MSN_Method MSN_Url !MSN_Usr
http_reply_access deny MSN_Method MSN_Url !MSN_Usr

http_access       deny CONNECT MSN_Port !MSN_Usr
http_reply_access deny CONNECT MSN_Port !MSN_Usr
```

El reply acces es para forzar a que si o si se cumpla dicha condición, ya que podemos tener una gran cantidad de ACL’s y algunas se pueden superponer, entonces con esto las forzamos.

## Autenticacion NTLM

Para conocer el estado de una cuenta podemos ejecutar

```
# wbinfo -a [domain]\\[user]%[password]
plaintext password authentication failed
error code was NT_STATUS_ACCOUNT_LOCKED_OUT (0xc0000234)
error messsage was: Account locked out
Could not authenticate user GRUPODERED\soporte%Password1234 with plaintext password
challenge/response password authentication failed
error code was NT_STATUS_ACCOUNT_LOCKED_OUT (0xc0000234)
error messsage was: Account locked out
Could not authenticate user GRUPODERED\soporte with challenge/response
```

Para ver la lista de usuarios del dominio

```
# wbinfo -u
```

## Blacklist

Listado de sitios prohibidos organizados por categorias. A continuacion describimos cada unas de las mismas:

| Categoria | Descripcion |
|----|----|
| ads | Servidores de Publicidad |
| adult | Sitios que contienen material adulto |
| aggressive | Sitios que promueven la violencia |
| antispyware | Sitios que remueven Spyware |
| artnudes | Sitios de arte que contienen desnudos aritsticos |
| astrology | Sitios de Astrologia |
| audio-video | Sitios de descarga de videos o audio |
| banking | Sitios de Banca en linea |
| beerliquorinfo | Sitios con informacion relacionada con Cervezas o Licores |
| beerliquorsale | Sitios de Compra de Cervezas o licores |
| Blog | Sitios de bítacoras |
| cellphones | Material para Telefonos Celulares |
| chat | Sitios con salas de chat |
| childcare | Sitios relacionados con el Cuidado de niños |
| cleaning | Sitios relacionados con limpieza |
| clothing | Sitios relacionados con la venta de indumentaria |
| culnary | Sitios relacionados con la cocina |
| dating | Sitios relacionados con citas en linea |
| desktopsillies | Sitios que contienen salvapantallas, backgrounds, cursores, punteros, temas de escritorio y material peligroso |
| dialers | Sitios con marcadores automaticos como los utilizados por Troyanos o sitios pornograficos |
| drugs | Sitios Reladionados Con Drogas |
| ecommerce | Sitios que proveen compras en linea |
| entertainment | Sitios que promocionan peliculas, libros, revistas, humor |
| filehosting | Sitios para hostear archivos |
| frencheducation | Sitios relacionados con educacion francesa |
| gambling | Sitios de Apuestas |
| games | Sitios relacionados a los juegos |
| gardening | Sitios de Jardineria |
| government | Sitios Gubernamentales |
| guns | Sitios relacionados con armas |
| hacking | Sitios con información sobre Hacking/cracking |
| homerepair | Sitios sobre reparaciones hogareñas |
| hygiene | Sitios sobre higiene y material relacionado al aseo personal |
| instantmessaging | Sitios que contienen descargas de clientes de mensajeria y mensajeria basada en la web |
| jewelry | Sitios relacionados con la joyeria y venta de la misma |
| jobsearch | Sitios de busqueda de empleo |
| kidstimewasting | Sitios donde los niños suelen perder el tiempo |
| mail | Sitios de Webmail / email |
| marketingware | Sitios sobre productos de Marketing |
| medical | Sitios web medicos |
| mixed_adult | Sitios con material adulto mezclado |
| naturism | Sitios que contienen fotos de desnudos y/o promueven el nudismo |
| news | Sitios de Noticias |
| onlineauctions | Sitios de Acciones en linea |
| onlinegames | Sitios de Juegos en linea |
| onlinepayment | Sitios de pago online |
| personalfinance | Sitios de Finanzas Personales |
| pets | Sitios sobre mascotas |
| phishing | Sitios que intentan engañar a la gente para que ingrese información privada. |
| porn | Pornografía |
| proxy | Sitios con proxies para saltearse filtros |
| radio | Sitios relacionados a la radio y television que no tienen relacion con noticias |
| religion | Sitios que promueven la religion |
| ringtones | Sitios que contienen ring tones, juegos, dibujos y otros |
| searchengines | Motores de Busqueda, como Google |
| sect | Sitios sobre grupos religiosos |
| sexuality | Sitios dedicados a la sexualidad, que probablemente incluyen material adulto |
| shopping | Sitios de Compras |
| socialnetworking | Sitios de Redes Sociales |
| sportnews | Sitios de noticias deportivas |
| sports | Sitios sobre deportes (todos) |
| spyware | Sitios que corren o tienen software espia para descargar |
| updatesites | Sitios donde hay actualizaciones de software para descargar que podrian contener virus |
| vacation | Sitios sobre vacacionar |
| violence | Sitios que contienen violencia |
| virusinfected | Sitios que contienen archivos infectados con virus |
| warez | Sitios que contienen software pirateado |
| weather | Sitios de noticias del Clima y relacionados con el Clima |
| weapons | Sitios que detallan o venden armas |
| webmail | Sitios de Webmail |
| whitelist | Contienen Sitios 100% adecuados para niños |

#### Referencias

| URL                                                      | Descripción |
|----------------------------------------------------------|-------------|
| <http://urlblacklist.com%7CSitio> con las blacklists     |             |
| <http://www.squid-cache.org%7CPagina> del proyecto Squid |             |
| <http://www.squidguard.org%7CPagina> de SquidGuard       |             |
| <http://dansguardian.org/%7CDansGuardian>                |             |

## Formato de LOG

##### Cambiar formato de logs

Agregar la directiva *emulate_httpd_log on* en el squid.conf y se va a leer mas facil :)

##### time.sh

*Script a cual por medio de una pipe se le envia el access.log, lo analiza y nos interpreta el formato de fecha* **UNIX** *a formato de fecha* **Human Readable** :-P.

```bash>
Otra opción :
<code perl>
#!/usr/bin/perl -pw

s/^\d+\.\d+/localtime $&/e;
```

Enlace interesante: <http://www.tenon.com/support/webten/papers/squidlog.shtml>

## Información de Squid

Si queremos ver los mensajes de error de Squid traducidos

```
error_directory	/usr/local/squid/share/errors/Spanish
```

Si queremos ocultar la versión del Squid a mostrar

```
httpd_suppress_version_string	on
```

Si queremos modificar el nombre del host a mostrar en el mensaje de error

```
visible_hostname mihostproxy
```

Enlace interesante: <http://techspalace.blogspot.com/2008/01/remove-squid-footer.html>

## Script para ver los top sites

```perl>
$cfgOutput") || die("Cannot open output file");


##
# Print header HTML
##
print OUTPUT <<END;
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
	<head profile="http://gmpg.org/xfn/11">
		<title>Squid Top-Site Report</title>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
		<style type="text/css">
		<!--
			body { font: normal 1.0em 'Trebuchet MS' }
			ul { list-style-type: none }
			ul a { position: absolute; left: 150px }
			#Generated { margin: 0; padding: 0; font-style:italic }
			#Footer { color: #cecece; line-height: 150%; border-top: 1px solid #cecece; padding: 0.4em }
		-->
		</style>
	</head>
	<body>
	<h1>Most frequently visites sites</h1>
	<p id="Generated">Report generated on $cfgDate</p>
	<ul>
END


##
# Iterate through lines inaccess.log
##
open(LOG, "$cfgLog") or die "Can't open file $cfgLog!";
  
while(<LOG>)
{
	$row = $_;

	($epoch_time_miliseconds, $unknown_integer1, $ip, $tcp_and_http_code, $unknown_integer2, $http_method, $url, $minus, $squid_method_site_url, $content_type) = split(/\s+/, $row);

	# Not checking for http:// etc because we want to include CONNECT's and
	# multi-protocol data.
	$url =~ s/http:\/\///;
	$url =~ s/ftp:\/\///;
	$url =~ s/www.//;
	$url =~ s/\/$//;

	# If the url is empty (which occurs occasionally in my logs, no idea
	# why, then don't add it to the list.
	if (!$url )
	{
		next;
	}

	# Patern match data between slashes.
	($site_url) = ($url =~ m{ ([A-Za-z0-9.\-:]+)}x );

	# Only match html content.
	if($content_type =~ m{text/html})
	{
		# If hash already contains an entry for the exact URL.
		if ($sites{$site_url})
		{
		     $sites{$site_url}->{count}++;
		}
		# If no matching entry exists, create one.
		else
		{
			$sites{$site_url}->{count} = 1;
		}
	}

	# Update size of content for website regardless of content-type.
	if (!$sites{$site_url}->{size})
	{
		$sites{$site_url}->{size} = 0;
	}
	$sites{$site_url}->{size} = $sites{$site_url}->{size} + $unknown_integer2;
}


##
# Iterate through visited sites.
##
foreach $site_url ( sort ($cfgSortMethod keys (%sites) ) )
{
	# Only show top x entries.
	if ($cfgNumberToShow > 0)
	{
		if ($cfgSortMethod eq "by_times_visited_then_name")
		{
			print OUTPUT "		<li>" . $sites{$site_url}->{count} ."<a href=\"http://" . $site_url . "/\">" . $site_url . "</a></li>\n";
		}
		elsif ($cfgSortMethod eq "by_size_then_name")
		{
			print OUTPUT "          <li>" . format_size($sites{$site_url}->{size}) ."<a href=\"http://" . $site_url . "/\">" . $site_url . "</a></li>\n";
		}

		$cfgNumberToShow--;
	}
}


##
# Print Footer HTML
##
print OUTPUT <<END;
	</ul>
	<div id="Footer">Producted by <a href="http://davehope.co.uk">Squid Top-Site Report Generator</a>.</div>
	</body>
</html>
END


##
# Sort sites by frequency visited, then alphabetically.
##
sub by_times_visited_then_name {
	$sites{$b}->{count} <=> $sites{$a}->{count} 
		||
	$a cmp $b
}

##
# Sort by size, then alphabetically.
##
sub by_size_then_name {
        $sites{$b}->{size} <=> $sites{$a}->{size}
                ||
        $a cmp $b
}

#-----------------------------------------------------------------------------#
# Version: 1.0
# Copyright: Bryant H. McGill - 11c Lower Dorset Street, Dublin 1, Ireland
# Web Address: http://www.bryantmcgill.com/Shazam_Perl_Module/
# Use Terms: Free for non-commercial use, commercial use with notification.
#
# Legal: This code is provided "as is" without warranty of any kind.
# The entire risk of use remains with the recipient.
# In no event shall Bryant McGill be liable for any direct,
# consequential, incidental, special, punitive or other damages.
#-----------------------------------------------------------------------------#
sub format_size{
  my($bytes) = @_;

  return '' if ($bytes eq '');

  my($size);
  $size = $bytes . ' Bytes' if ($bytes < 1024);
  $size = sprintf("%.2f", ($bytes/1024)) . ' KB' if ($bytes >= 1024 && $bytes < 1048576);
  $size = sprintf("%.2f", ($bytes/1048576)) . ' MB' if ($bytes >= 1048576 && $bytes < 1073741824);
  $size = sprintf("%.2f", ($bytes/1073741824)) . ' GB' if ($bytes >= 1073741824 && $bytes < 1099511627776);
  $size = sprintf("%.2f", ($bytes/1099511627776)) . ' TB' if ($bytes >= 1099511627776);

  return $size;
}
```
