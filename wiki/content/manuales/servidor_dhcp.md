---
title: "Servicio DHCP"
date: 2010-12-01T12:12:14-03:00
---

**DHCP** (sigla en inglés de **D**ynamic **H**ost **C**onfiguration **P**rotocol) es un protocolo de red que permite a los nodos de una red IP obtener sus parámetros de configuración automáticamente. Se trata de un protocolo de tipo cliente/servidor en el que generalmente un servidor posee una lista de direcciones IP dinámicas y las va asignando a los clientes conforme éstas van estando libres, sabiendo en todo momento quién ha estado en posesión de esa IP, cuánto tiempo la ha tenido y a quién se la ha asignado después.

Veremos como configurar un servidor DHCP con alta disponibilidad y configuracion replicada por medio de Cscync2

Para el esquema distribuido de configuracion DHCP tuvieron que modificarse parámetros de configuración de dicho servicio.

En la directiva wpad: se utilizo un nombre para indicar la ubicación del archivo de autoconfiguración del proxy, y en cada red dicha entrada DNS puede variar.

La configuracion de subredes y de hosts fijos se separo en diferentes archivos para una mejor edición por parte de los administradores

### Configuracion DHCP

Configuracion

##### /etc/dhcpd.conf

```java>
== /etc/dhcp.peer ==

<code java>
failover peer "dhcp" {
        secondary;
        address 10.1.100.10;
        port 519;
        peer address 10.1.100.11;
        peer port 519;
        max-response-delay 60;
        max-unacked-updates 10;
        load balance max seconds 3;
}
```

##### /var/www/html/proxy.pac

```javascript>
Segun desde que red nos conectemos nos asigna un proxy diferente



**MENSAJES DE LOG**

^^Mensaje^Quien lo envia^Descripcion^^
|DHCPDISCOVER|Cliente|Envía un mensaje de difusión para localizar a los servidores DHCP activos|
|DHCPOFFER|Servidor|Responde con una oferta de parámetros de configuración conforme a la situación del cliente|
|DHCPREQUEST|Cliente|Solicita los parámetros ofrecidos, en caso de que el mensaje del servidor haya sido aceptado, \\ rechazando la oferta, si el mensaje del servidor ha sido desestimado o confirmando \\ la solicitud de una dirección IP obtenida anteriormente|
|DHCPACK|Servidor|Mensaje de confirmación y cierre, indicando los parámetros definitivos|
|DHCPNACK|Servidor|Informe de que la dirección IP que solicita no es válida para la subred en la que se encuentra \\ o ya no la puede asignar porque la tiene otro equipo. |
|DHCPDECLINE|Cliente|Informe de que la dirección está en uso, normalmente porque otro usuario ha asignado \\ esa dirección manualmente|
|DHCPRELEASE|Cliente|Informe de que ha finalizado el uso de la dirección IP|
|DHCPINFORM|Cliente|Consulta sobre la configuración local. El cliente ya está configurado cuando envía este mensaje|


===== Software instalado =====

^^Nombre^Version^Descripcion^
|Kernel|2.6.18-8.el5xen|Nucleo|
|RedHat|Red Hat Enterprise Linux Server release 5|Distribucion Linux|
|System Imager|4.0.2|Cliente de System Imager|
|Internet Systems Consortium DHCP|3.0.5|Servidor de DHCP|
|nss_ldap|253-3|Autenticacion LDAP para PAM|

===== Archivo de configuración principal - dhcpd.conf =====

  * **authoritative** - Supone que la configuración correcta para la red es la definida en el servidor DHCP y tratará de reasignar datos a los clientes mal configurados. Este parámetro puede ser global o asigando a una declaración de subred. Los cambios realizados en en servidor marcado como authoritative tienen una rápida propagación en la subred ya que se reconfigura cualquier cliente con la antigua configuración

  * **option netbios-name-servers 10.1.200.3** - Define la dirección del servidor WINS para NetBios

  * Web Proxy Autodiscovery Protocol (WPAD)es un método usado por los navegadores, para localizar y autoconfigurar los proxy de conexión, aqui le especificamos de donde conseguir esa información de configuración
    * **option wpad code 252 = text**
    * **option wpad "http://proxyconf.cayu.com.ar/proxy.pac"**

  * **ddns-update-style none** activa o desactiva la actualización DNS con los valores asignados mediante DHCP.

  * Inclusión de los demás archivos de configuración generales
    * **include "/etc/dhcpd.peer"**
    * **include "/etc/dhcp/buenos_aires.conf"**
    * **include "/etc/dhcp/buenos_aires_hosts.conf"**
  
==== Archivo de configuración de failover - dhcpd.peer ====

Dentro de este archivo configuraremos la disponibilidad ante fallos


  * **secondary - primary** Si nos encontramos en el servidor primario pondremos secondary, especificando que nos referimos al secundario y viceverza

  * **address 10.1.1.11 - address 10.1.1.10** La dirección propia del servidor local desde el cual estamos ejecutando el servicio

  * **port 519** Puerto de escucha del servicio

  * **peer address 10.1.1.11 - peer address 10.1.1.10** Dirección propia del servidor local desde el cual estamos ejecutando el servicio

  * **peer port 519** Puerto de escucha del servicio del servidor local desde el cual estamos ejecutando el servicio

  * **max-response-delay 60**

  * **max-unacked-updates 2592000**

  * **mclt 2592000**

  * **split 128**

  * **load balance max seconds 3**

===== Notas =====

//Script para genera un archivo de configuracion con muchas subnets, del rango 10.216.64.0 al 10.216.98.0, con saltos de 60 a 200//

<code php>
authoritative;
ddns-update-style interim;
ignore client-updates;
<?php
for($i = 64; $i <= 98; $i++) {
print "
subnet 10.216.".$i.".0 netmask 255.255.255.0 {
    option subnet-mask 255.255.255.0;
    option domain-name-servers 10.1.200.10, 10.1.200.11;
    option routers 10.216.".$i.".254;
    option subnet-mask 255.255.255.0;
    max-lease-time 86400;
    default-lease-time 86400;
    pool {
	max-lease-time 86400;
	default-lease-time 86400;
	range 10.216.".$i.".60 10.216.".$i.".200;
	deny dynamic bootp clients;
    }
}
";
}
?>
```

*Script para verificar la cantidad de leases activos expirados y abandonados*

```perl>
) {
	next if ( $line =~ /^\s*#/o );
	$mline = $line;
        chomp($mline);
	@wds = split( '=',$mline);
	next if ( $wds[0] ne "IPADDR" );
	$ipAddr = $wds[1];
	@numbers = split(/\./, $ipAddr);
	$ip_number = pack("C4", @numbers);
	($ipName) = (gethostbyaddr($ip_number, 2))[0];
}
close(CFG);
chomp($localhost = $ipName);

#
# Format (see man date) in that the expiration date is echoed.
# Select the format of your choice. If you want the lease time to 
# be printed in amarican style put a comment char ``#'' right in 
# front of the first $outputDateFormat line and remove it from the
# second one.
#
# European Style date
#$outputDateFormat="%H:%M:%S %d.%m.%Y ";

# American style date
$outputDateFormat="%m/%d/%Y %H:%M:%S";

# ### point this to your dhcpd.leases ###
# /etc/dhcpd.leases and /var/state/dhcpd/dhcpd.leases 
# will be seachred by default.
#
$LEASEFILE = "/var/lib/dhcpd/dhcpd.leases";
if( ! -r $LEASEFILE ){
	if( -r "/etc/dhcpd.leases" ){
		$LEASEFILE="/etc/dhcpd.leases";
	}else{
		die "Cannot find \"dhcpd.leases\" file \n";
	}
}

# ### get universal date from system    ###
#
$xTZ = &ParseDate("now");
$tz=&Date_TimeZone;
$x=Date_ConvTZ($xTZ, $tz, "UTC");

open(LEASES, "$LEASEFILE") or die "Can't open $LEASEFILE";
$inlease = 0;

while ($line = <LEASES>) {
	next if( $line =~ /^\s*#/o );
	$mline = $line;
      chomp($mline);
	$mline=~ s/  / /g;

	@wds = split( ' ',$mline);
	if( !$inlease ) {
		# Look for a lease record
		next if( $wds[0] ne "lease" );
		$ipAddr = $wds[1];
		@numbers = split(/\./, $ipAddr);
		$ip_number = pack("C4", @numbers);
		($ipName) = (gethostbyaddr($ip_number, 2))[0];
		if ($ipName) {
		   ;
		} else {
		   $ipName = "<unknown>";
		}
		$startDt = "<unknown>";
		$endDt = "<unknown>";
		$endNever = 0;
		$ddnsClient = "<unknown>";
		$ethAddr = "<unknown>";
		$hostName = "<unknown>";
		$leaseState = "<unknown>";
		$leaseAbandoned = 0;
		$inlease = 1;
		next;
	}
	# Parse each clause in lease
	if( $wds[0] eq "starts" ) {
		$startDt = join( ' ', $wds[2], $wds[3] );
		$startDt=~s/;\s*$//;
		# Parse Date in Date::Manip internel format
		$startDtUTC=ParseDate($startDt);
		#
		# Convert it to local timezone
		$startDtTZ=Date_ConvTZ($startDtUTC,"UTC", $tz);
		#
		# Finally make a human readable date string out of it
		$startDt=UnixDate($startDtTZ, ($outputDateFormat) ) ;
		next;
	}
	if( $wds[0] eq "ends" ) {
		if( $wds[2] eq "never" ) {
			$endDt = "2999\/01\/01 12:00:00";
			$endNever = 1;
		} else {
			$endDt = join( ' ', $wds[2], $wds[3] );
			$endDt=~s/;\s*$//;
		}
		# Parse Date in Date::Manip internel format
		$endDtUTC=ParseDate($endDt);
		#
		# Convert it to local timezone
		$endDtTZ=Date_ConvTZ($endDtUTC,"UTC", $tz);
		#
		# Finally make a human readable date string out of it
		$endDt=UnixDate($endDtTZ, ($outputDateFormat) ) ;
		next;
	}
	if( $wds[0] eq "hardware" ) {
		$ethAddr = $wds[2];
		$ethAddr=~s/;\s*$//;
		next;
	}
	if( $wds[0] eq "client-hostname" ) {
		$hostName = $wds[1];
		$hostName=~s/;\s*$//;
		$hostName=~ s/"//g;
		next;
	}
	if( $wds[0] eq "binding" ) {
		$leaseState = $wds[2];
		$leaseState=~s/;\s*$//;
		next;
	}
	if( $wds[0] eq "set" ) {
		if( $wds[1] eq "ddns-client-fqdn" ) {
			$ddnsClient = $wds[3];
			$ddnsClient=~s/"//g;
		}
	}
	if( $wds[0] eq "abandoned;" ) {
		$leaseAbandoned = 1;
		next;
	}
	if( $wds[0] ne "}" ) {
		next;
	}

	## End of lease data, generate output

	$inlease = 0;

	if( $ipName eq "<unknown>" ) {
		$ipName = $ddnsClient;
	}

	$endDt = "dyn-bootp: never" if( $endNever );
	$lease = sprintf "%-17s %-15s %-19s %-19s %-25s %s\n",
                         $ethAddr,
                         $ipAddr,
                         $startDt,
                         $endDt,
                         $hostName,
                         $ipName;

	$ClientLeases{$ethAddr} = $lease;
	$ClientLeaseTime{$ethAddr} = $endDtUTC;
	$ClientLeaseState{$ethAddr} = $leaseState;

	if( $leaseAbandoned ) {
		# Unexpectedly found someone with this address
		$AbandonedAddresses[$abandonedc++] = $lease;
		next;
	}

	if( $leaseState eq "active" ) {
		$ActiveLeases{$ipAddr} = $lease;
	} else {
		$ExpiredLease{$ipAddr} = $lease;
	}
}

close(LEASES);

if( !$opt_w ){
	$|=1;
	$q = new CGI;
	print $q->header('text/html');
	print $q->start_html( -title=>"DHCP status on $localhost",
			      -expires=>'+5s',
			      -status=>'200 OK',
			      -BGCOLOR=>'white' );
	print "<PRE>\n";
}

# Sort each hash & return the keys for in-order access

@ClientKeys = sort(keys(%ClientLeases));	# MAC address order
@ActiveKeys = sort(keys(%ActiveLeases));	# IP address order
@ExpiredKeys = sort(keys(%ExpiredLeases));	# IP address order
@abandoned = sort(@abandoned);			# MAC, IP address (?)

$xDate=UnixDate($xTZ, ($outputDateFormat) ) ;
print "DHCP leases issued by $localhost as of $xDate\n";

$actc = 0;
print "\nActive Clients:";
foreach $i  ( @ClientKeys ) {
	next if( $ClientLeaseState{$i} ne "active" );

	if( $actc++ == 0 ) {
		print "\nEthernet Address";
                print "     IP Address";
                print "     Lease Issue Time";
                print "    Expiration Time";
                print "	      Client Hostname";
                print "       DNS Hostname\n";
		print "-----------------";
                print " ---------------";
                print " -------------------";
                print " -------------------";
                print " -------------------------";
                print " --------------------";
                print "---------------------\n";
	}
	print $ClientLeases{$i};
}
if( $actc == 0 ) {
	printf " None\n";
} else {
	printf "Total active clients: %d\n", $actc;
}

$iactc = 0;
print "\nInactive Clients:";
foreach $i  ( @ClientKeys ) {
	next if( $ClientLeaseState{$i} eq "active" );

	if( $iactc++ == 0 ) {
		print "\nEthernet Address";
                print "     IP Address";
                print "     Lease Issue Time";
                print "    Expiration Time";
                print "	      Client Hostname";
                print "       DNS Hostname\n";
		print "-----------------";
                print " ---------------";
                print " -------------------";
                print " -------------------";
                print " -------------------------";
                print " --------------------";
                print "---------------------\n";
	}
	print $ClientLeases{$i};
}
if( $iactc == 0 ) {
	printf " None\n";
} else {
	printf "Total inactive clients: %d\n", $iactc;
}

$actn = 0;
print "\nActive Addresses:";
foreach $i  ( @ActiveKeys ) {
	if( $actn++ == 0 ) {
		print "\nEthernet Address";
                print "     IP Address";
                print "     Lease Issue Time";
                print "    Expiration Time";
                print "	      Client Hostname";
                print "       DNS Hostname\n";
		print "-----------------";
                print " ---------------";
                print " -------------------";
                print " -------------------";
                print " -------------------------";
                print " --------------------";
                print "---------------------\n";
	}
	print $ActiveLeases{$i};
}
if( $actn == 0 ) {
	printf " None\n";
} else {
	printf "Total Active: %d\n", $actn;
}

$expn = 0;
print "\nExpired Leases:";
foreach $i  ( @ExpiredKeys ) {
	@wds = split( ' ', $ExpiredLeases{$i});
	if( !defined($ActiveLeaseTime{$wds[1]}) ) {
		if( $expn++ == 0 ) {
			print "\nEthernet Address";
	                print "     IP Address";
			print "     Lease Issue Time";
			print "    Expiration Time";
			print "	      Client Hostname";
			print "       DNS Hostname\n";
			print "-----------------";
			print " ---------------";
			print " -------------------";
			print " -------------------";
			print " -------------------------";
                        print " --------------------";
                        print "---------------------\n";
		}
		print $ExpiredLeases{$i};
	}
}
if( $expn == 0 ) {
	printf " None\n";
} else {
	printf "Total expired: %d\n", $expn;
}

$abdn = 0;
print "\nAbandoned Addresses:";
foreach $i  ( @abandoned ) {
	if( $abdn++ == 0 ) {
		print "\nEthernet Address";
                print "     IP Address";
                print "     Lease Issue Time";
                print "    Expiration Time";
                print "	      Client Hostname";
                print "       DNS Hostname\n";
		print "-----------------";
                print " ---------------";
                print " -------------------";
                print " -------------------";
                print " -------------------------";
                print " --------------------";
                print "---------------------\n";
	}
	print $i;
}
if( $abdn == 0 ) {
	printf " None\n";
} else {
	printf "Total abandoned: %d\n", $abdn;
}

printf "\n";

if( !$opt_w ){
	print "</PRE>\n";
	$me = $q->url();
	print $q->a({href=>$me},"Refresh");
	print $q->br();
	print $q->a({href=>"/"}, "Home Page");
	print $q->end_html;
}
# Last line of /opt/bin/leaseholders.cgi
```
