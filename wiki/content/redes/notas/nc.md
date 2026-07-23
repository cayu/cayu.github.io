---
title: "Netcat"
date: 2018-11-01T13:35:16-03:00
---

### Netcat

Netcat is a featured networking utility which reads and writes data across network connections, using the TCP/IP protocol. It is designed to be a reliable “back-end” tool that can be used directly or easily driven by other programs and scripts. At the same time, it is a feature-rich network debugging and exploration tool, since it can create almost any kind of connection you would need and has several interesting built-in capabilities.

It provides access to the following main features:

* Outbound and inbound connections, TCP or UDP, to or from any ports.
* Featured tunneling mode which allows also special tunneling such as UDP to TCP, with the possibility of specifying all network parameters (source port/interface, listening port/interface, and the remote host allowed to connect to the tunnel.
* Built-in port-scanning capabilities, with randomizer.
* Advanced usage options, such as buffered send-mode (one line every N seconds), and hexdump (to stderr or to a specified file) of trasmitted and received data.
* Optional RFC854 telnet codes parser and responder. 

The GNU Netcat is distributed freely under the GNU General Public License (GPL).

### Ejemplos de uso

#### GET HTTP

```bash>
=== Pasar un archivo de un equipo a otros ===
Desde el servidor
<code>
cat backup.iso | nc -l 3333
```

Desde el cliente

```
nc 192.168.0.1 3333 > backup.iso
```

Si queremos poner un contador

```
nc 192.168.0.1 3333 | pv -b > backup.iso
```

Otro ejemplo

Desde el servidor

```
dd if=/dev/hdb5 | gzip -9 | nc -l 3333
```

Desde el cliente

```
nc 192.168.0.1 3333 | pv -b > myhdb5partition.img.gz
```

#### Consultando puertos abiertos

```bash>
 /dev/null && echo "Port $port open" || echo "Port $port closed";
done
```

Fuente : <http://adamish.com/blog/archives/567>

#### Referencias

<http://netcat.sourceforge.net/>

Tutorial con ejemplos prácticos : <http://crysol.org/node/28>

Mas ejemplos : <http://www.g-loaded.eu/2006/11/06/netcat-a-couple-of-useful-examples/>
