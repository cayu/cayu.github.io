---
title: "Network Grep - ngrep"
date: 2018-11-22T13:38:02-03:00
---

ngrep es un analizador de paquetes de red.

Muestra y busca paquetes. Ngrep se esfuerza por proveer de la mayoría de características comunes del “grep” de GNU, aplicándolas a la capa de network ({“network layer”} del modelo de referencia OSI). ngrep es consciente de la presencia de pcap y permite usar expresiones regulares que concuerden con el “payload” ( o sea la carga, el cuerpo, y \_no\_ los encabezados) de los paquetes. Actualmente reconoce TCP, UDP, e ICMP sobre Ethernet, PPP, SLIP e interfaces nulas {“null interfaces”}, y comprende la lógica de un filtro “bpf” de la misma manera que herramientas más comunes de sniffing como tcpdump y snoop.

Usage examples:

```
ngrep '' udp (print all UDP packets)

ngrep '' icmp (print all ICMP packets)

ngrep '' port 53 (print TCP or UDP port 53 packets)

ngrep '' tcp port 23 (print TCP port 23 packets)

ngrep 'LILWORD' port 138 (print Microsoft browsing traffic for NT domain LILWORLD)

ngrep -iq 'rcpt to|mail from' tcp port 25 (monitor current delivery and print sender and recipients)

ngrep 'user' port 110 (monitor POP3)

ngrep -q 'abcd' icmp (Microsoft operating systems fill the ICMP payload with the alphabet; is the "pinging" host running a 
Microsoft operating system?)

ngrep -iq 'user-agent' tcp port 80 (determine client application that client host is running)

ngrep '220' port 21 (determine version of FTP server)

ngrep 'SSH' port 22 (investigate Secure Shell)

ngrep -v '' port 23 (see all traffic but telnet)

ngrep -d le0 '' (listen to le0)
```

Useful flags:

```
-A n (prints out "n" packets after the match)

-l (pipe the output of ngrep to another program for more processing)

-v (print all lines not matching the expression)

-d (specify the device you want to monitor)
```

- <http://ngrep.sourceforge.net/>
- <https://github.com/jpr5/ngrep>

Un posteo interesante en un blog : <http://seguridadyredes.nireblog.com/post/2010/02/24/esas-pequenas-utilidades-ngrep>
