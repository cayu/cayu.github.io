---
title: "DRBD (Distributed Replicated Block Device)"
date: 2014-01-30T15:58:39-03:00
---

DRBD permite mantener replicados los sistemas de archivos de **n** servidores diferentes. Seria como un RAID 1 en Red.

- Solo uno de los servidores puede montar la partición DRBD al mismo tiempo.
- El servidor secundario no puede montar la partición mientras esté montada en el primario.
- Cada cambio que se produce sobre la partición DRBD del servidor primario, es replicado a través de la red sobre la partición DRBD sobre el servidor secundario.

# Notas extras

Podemos optimizar el funcionamiento de DRBD sobre puntos remotos, con WANPROXY.

## Referencias

<http://www.drbd.org/>

<http://www.mauriciomatamala.net/gt/index.php>

<http://wanproxy.org/>
