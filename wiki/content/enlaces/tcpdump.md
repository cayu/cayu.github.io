---
title: "TCPDUMP"
date: 2011-01-24T11:29:22-03:00
---

Permite al usuario capturar y mostrar a tiempo real los [paquetes](http://es.wikipedia.org/wiki/Paquete_de_datos) transmitidos y recibidos en la red a la cual el ordenador está conectado. Está escrito por Van Jacobson, Craig Leres, y Steven McCanne que trabajaban en ese momento en el Grupo de Investigación de Red del Laboratorio Lawrence Berkeley. Más tarde el programa fue ampliado por Andrew Tridgell.

tcpdump funciona en la mayoría de los sistemas operativos UNIX: Linux, Solaris, BSD, Mac OS X, HP-UX y AIX entre otros. En esos sistemas, tcpdump hace uso de la librería libpcap para capturar los paquetes que circulan por la red.

Existe una adaptación de tcpdump para los sistemas Windows que se llama WinDump y que hace uso de la biblioteca Winpcap.

En UNIX y otros sistemas operativos, es necesario tener los privilegios del root para utilizar tcpdump.

El usuario puede aplicar varios filtros para que sea más depurada la salida. Un filtro es una expresión que va detrás de las opciones y que nos permite seleccionar los paquetes que estamos buscando. En ausencia de ésta, el tcpdump volcará todo el tráfico que vea el adaptador de red seleccionado.

##### Utilización frecuente de tcpdump

\*Para depurar aplicaciones que utilizan la red para comunicar. \*Para depurar la red misma. \*Para capturar y leer datos enviados por otros usuarios u ordenadores. Algunos protocolos como telnet y HTTP no cifran los datos que envían en la red. Un usuario que tiene el control de un router a través del cual circula tráfico no cifrado puede usar tcpdump para conseguir contraseñas u otras informaciones.

##### Funcionamiento

#### Parámetros

     tcpdump [-aAdDeflLnNOpqRStuUvxX] [-c count] [ -C file_size ]
             [ -E algo:secret ] [ -F file ] [ -i interface ] [ -M secret ]
             [ -r file ] [ -s snaplen ] [ -T type ] [ -w file ]
             [ -W filecount ] [ -y datalinktype ] [ -Z user ]
             [ expression ]

* -A: Imprime cada paquete en código ASCII
* -D: Imprime la lista de interfaces disponibles
* -n: No convierte las direcciones de salida
* -p: No utliza la interfaz especificada en modo promiscuo
* -t: No imprime la hora de captura de cada trama
* -x: Imprime cada paquete en hexadecimal
* -X: Imprime cada paquete en hexadecimal y código ASCII

* -c count: Cierra el programa tras recibir 'count' paquetes
* -C file_size
* -E algo:secret
* -F file
* -i interface: Escucha en la interfaz especificada
* -M secret
* -r file
* -s snaplen
* -T type
* -w file: Guarda la salida en el archivo 'file'
* -W filecount
* -y datalinktype
* -Z user

#### Filtros

* type [host|net|port]: Máquina en particular [host], red completa [net] o puerto concreto [port].
* dir [src|dst|src or dst|src and dst]: Especifica desde [src] o hacia dónde [dst] se dirige la información.
* proto [tcp|udp|ip|ether]: Protocolo que queremos capturar.

#### Salida del comando

src \> dst: flags \[dataseq ack window urgent options\]

     15:23:44.772291 IP 192.168.1.17.52798 > 85.Red-83-37-170.dynamicIP.rima-tde.net.65000: . ack 1791 win 7851 <nop, nop, timestamp 5520421 997821>

* 15:23:44772291: Indica hh:mm:fracciones
* src: Dirección y puerto origen.
* dst: Dirección y puerto destino.
* flags: Flags de la cabecera TCP. (.) si no hay flags o combinación de S (SYN), F (FIN), P (PUSH), W (reducción de la ventana de congestión), E (ECN eco).
* dataseq: Número de secuencia del primer byte de datos en este segmento TCP. El formato es primero:último (n).
* ack: El número de asentimiento. Indica el número siguiente de secuencia que se espera recibir.
* window: Tamaño de la ventana de recepción.
* urgent: Existen datos urgentes.
* options: Indica la existencia de opciones. Van entre "<"...">".

#### Ejemplos

Capturar tráfico cuya dirección IP de origen sea 192.168.3.1

      tcpdump src host 192.168.3.1

Capturar tráfico cuya dirección origen o destino sea 192.168.3.2

      tcpdump host 192.168.3.2

Capturar tráfico con destino a la dirección MAC 50:43:A5:AE:69:55

      tcpdump ether dst 50:43:A5:AE:69:55

Capturar tráfico con red destino 192.168.3.0

      tcpdump dst net 192.168.3.0

Capturar tráfico con red origen 192.168.3.0/28

      tcpdump src net 192.168.3.0 mask 255.255.255.240
      tcpdump src net 192.168.3.0/28

Capturar tráfico con destino el puerto 23

      tcpdump dst port 23

Capturar tráfico con origen o destino el puerto 110

      tcpdump port 110

Capturar los paquetes de tipo ICMP

      tcpdump ip proto \\icmp

Capturar los paquetes de tipo UDP

      tcpdump ip proto \\udp
      tcpdump udp

Capturar el tráfico Web

      tcpdump tcp and port 80

Capturar las peticiones de DNS

      tcpdump udp and dst port 53

Capturar el tráfico al puerto telnet o SSH

      tcpdump tcp and \(port 22 or port 23\)

Capturar todo el tráfico excepto el web

      tcpdump tcp and not port 80

otra forma:

      tcpdump tcp and ! port 80

Capturar todo el trafico a host 10.168.1.100 puerto 80, en full verbose mode, full snap length, sin ponerla en modo promiscuo, sin convertir las direcciones de salida, imprimir en ASCII y volcar todo el dump en un file

tcpdump -vvv -n -s 65535 -A -p -w /tmp/tcpdump host 10.168.1.100 and port 80 \[editar\]

##### Enlaces externos

- [http://www.tcpdump.org/ Sitio oficial de tcpdump y libpcap](http://www.tcpdump.org/ Sitio oficial de tcpdump y libpcap)
- [http://www.winpcap.org/windump/ Sitio oficial de WinDump](http://www.winpcap.org/windump/ Sitio oficial de WinDump)
- <http://es.wikipedia.org/wiki/Tcpdump>

## Sintaxis

[Expresiones validas para wireshark, para tcpdump y tcptrack](/enlaces/ethereal-tcpdump.pdf)

Mas ejemplos

```
nt all packets arriving at or departing from sundown
tcpdump host sundown
To print traffic between helios and either hot or ace:
tcpdump host helios and \( hot or ace \)
To print all IP packets between ace and any host except helios:
tcpdump ip host ace and not helios
To print all traffic between local hosts and hosts at Berkeley:
tcpdump net ucb-ether
To print all ftp traffic through internet gateway snup: (note that the expression is quoted to prevent the shell from (mis-)interpreting the parentheses):
tcpdump 'gateway snup and (port ftp or ftp-data)'
To print traffic neither sourced from nor destined for local hosts (if you gateway to one other net, this stuff should never make it onto your local net).
tcpdump ip and not net localnet
To print the start and end packets (the SYN and FIN packets) of each TCP conversation that involves a non-local host.
tcpdump 'tcp[13] & 3 != 0 and not src and dst net localnet'
To print IP packets longer than 576 bytes sent through gateway snup:
tcpdump 'gateway snup and ip[2:2] > 576'
To print IP broadcast or multicast packets that were not sent via ethernet broadcast or multicast:
tcpdump 'ether[0] & 1 = 0 and ip[16] >= 224'
To print all ICMP packets that are not echo requests/replies (i.e., not ping packets):
tcpdump 'icmp[0] != 8 and icmp[0] != 0"
```

## Análisis de filtrados

[Tcpdstat](/enlaces/utilidades_de_red/#tcpdstat)
