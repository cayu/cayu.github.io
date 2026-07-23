---
title: "Seguridad"
date: 2019-03-27T17:07:46-03:00
---

- [Pequeña guía de seguridad informática](/seguridad/Pequeña guía de seguridad informática)

## Notas

Notas cortas de seguridad e inseguridad informática que me sirvieron en mi trabajo y algunos aspectos legales.

#### Linux Auditd

- [Auditd](/seguridad/auditd/)
- [Process Accounting (PSACCT)](/seguridad/process_accounting_psacct/)

#### Legales

- [Ley 25.326 - PROTECCION DE LOS DATOS PERSONALES](http://www.infoleg.gov.ar/infolegInternet/anexos/60000-64999/64790/texact.htm)
- [Ley 26.388 - CODIGO PENAL Modificación](http://www.infoleg.gov.ar/infolegInternet/anexos/140000-144999/141790/norma.htm)

<!-- -->

* 

#### Referencias aparte sobre el tema

- [OWASP](https://www.owasp.org)
- [Prey - Software Logger](https://preyproject.com/es)
- [ISO27001 Security](http://www.iso27001security.com)
- [Introducción SGSI](http://www.inteco.es/Formacion/SGSI/Conceptos_Basicos)
- [Publicaciones NIST](http://csrc.nist.gov/publications/PubsSPs.html)
- [Securia](http://www.securia.es/)
- [Magerit](https://www.ccn-cert.cni.es/publico/herramientas/pilar5/magerit/)
- [Windingo](http://www.welivesecurity.com/la-es/2014/03/18/operacion-windigo-malware-utilizado-para-atacar-mas-500-000-computadoras/)
- [Shared a secret](https://onetimesecret.com/)
- [Search device internet](http://www.shodanhq.com/) Server+SQ-WEBCAM country AR
- [isseg Training](http://isseg-training.web.cern.ch/isseg-training/Training/Training.htm)
- [Darik's Boot And Nuke](http://www.dban.org/)
- PCI DSS - Dominio 6
- SOX - Section 404
- [dSploit - The most complete and advanced IT security professional toolkit on Android](http://dsploit.net/)
- <https://www.robtex.com>
- <http://hashcat.net/oclhashcat/>
- [Reflexiones sobre la norma ISO/IEC 27037:2012. Directrices para la identificación, recolección, adquisición y preservación de la evidencia digital](http://insecurityit.blogspot.com.ar/2013/09/reflexiones-sobre-la-norma-isoiec.html)

#### Exploits

Exploits útiles cuando vas a un lugar donde no saben el password de root

[jessica_biel_naked_in_my_bed.c.gz](/notas/seguridad/jessica_biel_naked_in_my_bed.c.gz) Linux vmsplice Local Root Exploit - Linux 2.6.17 - 2.6.24.1

[2009-linux-sendpage.c.gz](/notas/seguridad/2009-linux-sendpage.c.gz_linux_kernel_2.4/2.6_sock_sendpage/) Local Root Exploit

[mempodipper.c.gz](/notas/seguridad/mempodipper.c.gz) Escalada de privilegios remota con /proc/pid/mem write - Kernel 2.6.39 a 3.2

[pty.c.gz](/notas/seguridad/pty.c.gz) CVE-2014-0196 DOS PoC \[Written May 5th, 2014\]

[58137_2.c.gz](/notas/seguridad/58137_2.c.gz) [Linux Kernel CVE-2013-1763 Local Privilege Escalation Vulnerability](http://www.securityfocus.com/bid/58137)

#### Bombas lógicas

- [Understanding Bash fork() bomb](/seguridad/enlaces/understanding_bash_fork_bomb/)
- [ Prevent a fork bomb by limiting user process](/seguridad/How to/ Prevent a fork bomb by limiting user process)

### Prevenir fingerprints

Como ayudar a que los fingerprint que realizen nuestros atacantes sean un poco menos exactos, configurar las sysctl de nuestro sistema de la siguiente manera:

```
net.ipv4.ip_default_ttl = 128
net.ipv4.tcp_timestamps = 0
net.ipv4.tcp_window_scaling = 0
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.all.send_redirects = 0
net.ipv4.icmp_echo_ignore_all = 1

# Enable IP spoofing protection
net.ipv4.conf.all.rp_filter=1
# Disable IP source routing
net.ipv4.conf.all.accept_source_route=0
# Ignoring broadcasts request
net.ipv4.icmp_echo_ignore_broadcasts=1
net.ipv4.icmp_ignore_bogus_error_responses=1
# Make sure spoofed packets get logged
net.ipv4.conf.all.log_martians = 1
net.ipv4.conf.default.log_martians = 1
```

### Conocer que procesos bloquean tal cosa

```
Uso: fuser [-fMuv] [-a|-s] [-4|-6] [-c|-m|-n ESPACIO] [-k [-i] [-SIGNAL]] NOMBRE...
       fuser -l
       fuser -V
Muestra que procesos usan los archivos, zócalos o sistemas de archivos indicados.

  -a,--all              muestra también los archivos no usados
  -i,--interactive      pregunta antes de finalizar (ignorado con -k)
  -k,--kill             finaliza los procesos que acceden al archivo NOMBRE
  -l,--list-signals     lista los nombres de señales disponibles
  -m,--mount            muestra todos los procesos que usan el sistema de ficheros o dispositivo de bloques con ese nombre
  -M,--ismountpoint     cumple la petición solo si NOMBRE es un punto de montaje
  -n,--namespace ESPACIO  busca en este nombre de espacio (archivo, udp, o tcp)
  -s,--silent           funcionamiento silencioso
  -SIGNAL               envía esta señal en lugar de SIGKILL
  -u,--user             muestra los ID de usuario
  -v,--verbose          salida detallada
  -V,--version          muestra información de la versión
  -4,--ipv4             buscar solamente zócalos IPv4
  -6,--ipv6             buscar solamente zócalos IPv6
  -                     opciones de reinicio

  nombres udp/tcp: [local_port][,[rmt_host][,[rmt_port]]]
```

Ejemplo si queremos ver que proceso nos bloquea el pendrive y no nos deja desmontarlo

```
$ fuser -m /dev/sdb1
/dev/sdb1: 13542
```

Que proceso bloquea el archivo /usr/local/nagios/var/nagios.lock

```
fuser /usr/local/nagios/var/nagios.lock 
/usr/local/nagios/var/nagios.lock:  3178 13194 13342 13459 13472
```

Si queremos ver que PID usa el puerto 80 tcp.

```
fuser 80/tcp
80/tcp:              17233 17652 17870 18252 18779 19234 19484 19547 19621 19679 32454
```

Referencia útil como ver procesos escondidos: <http://www.cyberciti.biz/tips/linux-unix-windows-find-hidden-processes-tcp-udp-ports.html>
