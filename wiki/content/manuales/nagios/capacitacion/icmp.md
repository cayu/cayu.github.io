---
title: "ICMP"
date: 2015-09-18T14:33:27-03:00
---

El protocolo ICMP (*Internet Control Message Protocol*) puede ser considerado como parte de la capa IP. La especificación completa de este protocolo se encuentra en [RFC 792](http://www.ietf.org/rfc/rfc792.txt). Aunque sus mensajes son encapsulados en datagramas IP como cualquier otro protocolo de capa superior, su uso corresponde a mensajes especiales de la propia capa de red, aunque también pueden acceder a él las propias aplicaciones (por ejemplo el programa ping).

Algunos ejemplos de uso de ICMP son: obtención de máscaras de red (solicitud y respuesta), obtención de marcas de tiempo (solicitud y respuesta), condiciones de error del tipo “port unreachable” o “se necesita fragmentar el datagrama habiéndose solicitado la no-fragmentación“, petición y respuesta de eco para comprobar la actividad de otro host, información sobre el estado de las comunicaciones en una red, etc.

##### Algunas consultas ICMP

Los mensajes de query incluyen tras la cabecera 2 bytes de identificación, para que el cliente distinga las respuestas dirigidas a él de las dirigidas a otros procesos, y 2 btes de número de secuencia, para que el cliente pueda identificar ‘requests’ con ‘replays’. La generación de replys suele correr a cargo del kernel (no es un proceso de usuario). Los mensajes ICMP de ‘query’ más importantes son:

- ***Petición/respuesta de máscara***: Aunque normalmente se usa [DHCP](/dhcp/), también es posible para el host hacer una petición durante el proceso de arranque para obtener la máscara y una petición [ARP#RARP](/arp/#rarp) para obtener su IP. La petición de máscara suele enviarse en difusión. Un servidor de máscaras se encarga de contestar.
- ***Petición/respuesta de marca de tiempo***: Se utiliza para conocer el tiempo que un host tarda en procesar un mensaje y contestarlo o para sincronizar relojes entre hosts. Los tiempos se ponen en milisegundos desde las 0h UTC (Tiempo Coordinado Universal). Se proveen tres campos, a parte de los anteriormente citados para este tipo de mensajes. ‘Marca de tiempo de generación’, a rellenar por el emisor, y ‘Marca de tiempo de recepción’, y ‘Marca de tiempo de transmisión’, a rellenar por el receptor. Los significados de los campos resultan evidentes.
- ***Solicitud/anuncio de router***: Se trata de una forma de inicializar la tabla de enrutamiento. Tras el arranque, un host envía en broadcast o multicast un mensaje de solicitud de router. Uno o más routers responderán con mensajes de anuncio de router. Esto lo podrán hacer periódicamente permitiendo a los hosts actualizar sus tablas de enrutamiento.
- ***Petición/respuesta de eco***: Se utiliza para conocer si un host está en red. El mensaje ICMP contiene unos datos OPCIONALES cuyo contenido es irrelevante. Muchos SO implementan este mensaje mediante el programa ping. En las implementaciones UNIX de ping el campo identificador suele asociarse con el ID del proceso que envía el request. Esto permite identificar las respuestas si hubiera varias instancias de ping corriendo en el mismo host. En el campo de secuencia, se suele llevar un contador que permite ver si se han perdido, duplicado o desordenado paquetes, cosa típica en la capa IP sobre la que se encapsulan los mensajes ICMP.

Veamos un ejemplo de la salida del programa ping.

```
[deivid@localhost ~]$ ping www.virtuamanager.com
PING virtuamanager.com (213.194.149.188) 56(84) bytes of data.
64 bytes from virtuamanager.com (213.194.149.188): icmp_seq=1 ttl=56 time=42.6 ms
64 bytes from virtuamanager.com (213.194.149.188): icmp_seq=2 ttl=56 time=41.8 ms
64 bytes from virtuamanager.com (213.194.149.188): icmp_seq=3 ttl=56 time=41.6 ms
64 bytes from virtuamanager.com (213.194.149.188): icmp_seq=4 ttl=56 time=43.7 ms
64 bytes from virtuamanager.com (213.194.149.188): icmp_seq=5 ttl=56 time=42.7 ms
64 bytes from virtuamanager.com (213.194.149.188): icmp_seq=6 ttl=56 time=42.2 ms
64 bytes from virtuamanager.com (213.194.149.188): icmp_seq=7 ttl=56 time=46.2 ms

--- virtuamanager.com ping statistics ---
7 packets transmitted, 7 received, 0% packet loss, time 6005ms
rtt min/avg/max/mdev = 41.666/43.038/46.270/1.477 ms
```

##### ALGUNOS ICMP’S DE ERROR

Ya se ha dicho que el formato de los mensajes de error tan sólo incluye, además de la cabecera, una copia de la cabecera IP del datagrama que generó el error y los 8 primeros bytes del datagrama. En algunos de los ejemplos que siguen veremos la razón de ser de este formato:

- ***Destination unreachable***: Este tipo de mensajes de error se generan cuando por alguna razón, el datagrama no pudo alcanzar su destino (puerto, host, red inalcanzable, necesidad de fragmentar con bit DF activado…). La forma de comunicar este error a la capa superior es copiando la cabecera IP en el propio mensaje de error. Gracias al campo ‘protocolo’ el módulo ICMP que lo reciba sabrá asociarlo con el protocolo adecuado. Supongamos que se produce un ‘port unreachable’ en el envío de un datagrama UDP. La cabecera IP permite identificar que el error se produjo en el protocolo UDP, y además esto sirve para interpretar los 8 bytes adicionales, que pertenecerán a la cabecera UDP. Estos contendrán los puertos origen y destino (ver tema UDP). El puerto de origen puede servir para asociar el error con un determinado proceso de usuario (ej. puerto 2924 asociado a un cliente tftp), mientras que el destino nos indica qué puerto originó el error ‘port unreachable’.

<!-- -->

     Otro ejemplo, al intentar hacer ping al otro equipo de la red, que está apagado, se obtiene lo siguiente:

```
[deivid@localhost ~]$ ping 192.168.1.2
PING 192.168.1.2 (192.168.1.2) 56(84) bytes of data.
From 192.168.1.100 icmp_seq=2 Destination Host Unreachable
From 192.168.1.100 icmp_seq=3 Destination Host Unreachable
From 192.168.1.100 icmp_seq=4 Destination Host Unreachable
From 192.168.1.100 icmp_seq=6 Destination Host Unreachable
From 192.168.1.100 icmp_seq=7 Destination Host Unreachable
From 192.168.1.100 icmp_seq=8 Destination Host Unreachable

--- 192.168.1.2 ping statistics ---
9 packets transmitted, 0 received, +6 errors, 100% packet loss, time 8002ms
, pipe 3
```

Se han producido mensajes ICMP de error en respuesta a mensajes ICMP de query, lo cual SI está permitido, como ya se ha dicho.

- ***Source quench***: Este tipo de mensaje es generado por un router con problemas de congestión para avisar al host fuente de que reduzca el flujo de transmisión. Es muy poco usado, normalmente los routers se limitan a tirar paquetes y a intentar solucionar la congestión mediante algoritmos de enrutamiento.
- ***Parameter problem***: Si un router intermedio o el host destino encuentran un problema en la cabecera IP que le obliga a desecharlo, puede enviar un mensaje ICMP de este tipo al host fuente. Si el código es cero, un puntero marca el lugar de la cabecera en el que se produjo el error.
- ***Redirección***: Son enviados por un router al remitente de un datagrama IP cuando éste debería haber sido enviado a través de otro router.
- ***Time Exceeded***: Son enviados al remitente de un datagrama IP cuando se cumple el tiempo de vida máximo del paquete. Esto puede ocurrir porque el campo TTL del datagrama se haya puesto a 0 en un router, o bien el tiempo de reensamblado de fragmentos se haya cumplido en el destino. Basándose en este mensaje, y variando el campo TTL de un mensaje de petición de eco es posible obtener la ruta a un destino. Si el TTL es insuficiente, se recibirá un mensaje de tiempo excedido por cada router donde al recalcular el TTL valga 0, y un mensaje de respuesta de eco cuando el TTL sea suficiente para llegar al destino. El comando `tracert` de windows se basa en este algoritmo.
