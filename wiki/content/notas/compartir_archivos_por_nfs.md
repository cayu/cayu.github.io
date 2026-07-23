---
title: "Como compartir filesystems por NFS"
date: 2016-12-27T15:24:22-03:00
---

[NFS](http://es.wikipedia.org/wiki/Network_File_System) (Network File System) es un protocolo de red que permite montar un directorio de una máquina remota en nuestra máquina y trabajar en él como si fuera un directorio local. De esta manera podemos compartir un directorio (y todo lo que cuelga de él) con las demás máquinas Linux de nuestra red local.

El protocolo NFS se implementa con arquitectura cliente-servidor, por lo que necesita:

- un **servidor NFS** en la máquina remota, la que exporta el directorio.
- un **cliente NFS** en la máquina local, la que monta el directorio.

## NFS trabaja sobre RPC

El servidor NFS trabaja sobre [RPC](http://es.wikipedia.org/wiki/RPC) (Remote Procedure Call), protocolo que utiliza como capa de transporte [UDP](http://es.wikipedia.org/wiki/User_Datagram_Protocol).

- Los servidores que trabajan sobre RPC (como NFS), no tienen puertos asignados oficialmente, por lo que cuando arrancan, contactan con el daemon portmap y le informan de qué puerto usan.
- Cuando un cliente quiere contactar con un servidor RPC, contacta primero con portmap (que escucha en el puerto 111 TCP) y le pregunta cuál es el puerto correspondiente al servidor en cuestión. Una vez que conoce ese dato, el cliente ya puede contactar directamente con el servidor.

Para ver los servicios RPC que tenemos activos y el puerto utilizado por cada uno, haremos:

```
$ rpcinfo -p
```

obteniendo (antes de instalar NFS) algo similar a:

```
programa vers proto puerto
 100000    2   tcp    111  portmapper
 100000    2   udp    111  portmapper
 100024    1   udp  32768  status
 100024    1   tcp  32899  status
```

## Instalación

Dentro de Debian deberemos instalar los paquetes nfs-common nfs-kernel-server portmap

```
apt-get install nfs-common nfs-kernel-server portmap
```

En SuSE SLES

```
nfs-kernel-server
nfs-client
```

## Servidor NFS

- Una vez instalado el servidor NFS ([nfs.sourceforge.net](http://nfs.sourceforge.net), paquete nfs-kernel-server), editaremos el archivo de configuración /etc/exports para definir qué directorios exportamos y quién puede acceder. La sintaxis de /etc/exports es la siguiente: \<directorio_exportado\> \<host_que_puede_acceder\>(opciones) Por ejemplo:

```
/home/francis 192.168.0.0/255.255.255.0(rw,sync) 
/home/sergio 192.168.0.2(ro,sync) 192.168.0.4(rw,sync) La opción ro es para sólo lectura y rw para lectura y escritura. 
```

- Guardamos los cambios y reiniciamos el servidor: \# /etc/init.d/nfs-kernel-server restart
- El servidor NFS tiene varios demonios, siendo los principales nfsd, rpc.mountd, rpc.statd y lockd.li\>
- Veamos los servicios RPC que tenemos activos una vez instalado el servidor NFS:

```
$ rpcinfo -p 
programa vers proto puerto
 100000    2   tcp    111  portmapper
 100000    2   udp    111  portmapper
 100024    1   udp  32768  status
 100024    1   tcp  32899  status
 100003    2   udp   2049  nfs
 100003    3   udp   2049  nfs
 100003    4   udp   2049  nfs
 100003    2   tcp   2049  nfs
 100003    3   tcp   2049  nfs
 100003    4   tcp   2049  nfs
 100021    1   udp  32786  nlockmgr
 100021    3   udp  32786  nlockmgr
 100021    4   udp  32786  nlockmgr
 100021    1   tcp  59688  nlockmgr
 100021    3   tcp  59688  nlockmgr
 100021    4   tcp  59688  nlockmgr
 100005    1   udp    903  mountd
 100005    1   tcp    906  mountd
 100005    2   udp    903  mountd
 100005    2   tcp    906  mountd
 100005    3   udp    903  mountd
 100005    3   tcp    906  mountd 
```

- Para ver los directorios que están siendo compartidos haremos: \# exportfs
- Podemos saber los clientes NFS conectados mirando /var/lib/nfs/rmtab:

```
$ cat /var/lib/nfs/rmtab 
```

## Cliente NFS

- Para montar un directorio exportado usaremos mount, que es el cliente para NFS: \# mount -t nfs 192.168.0.4:/home/francis /mnt
- Lo desmontaremos con umount: \# umount /mnt
- Para montarlo durante el arranque añadiremos una línea a /etc/fstab: \# File_system Mount_point Type Options Dump Pass

192.168.0.4:/home/francis /mnt nfs rw,hard,intr 0 0

## Problemas comúnes

### statd nfsdrpc.nfsd: address family inet6 not supported by protocol TCP

Si tenemos un error similar a este :

```
statd nfsdrpc.nfsd: address family inet6 not supported by protocol TCP
```

Deberemos comentar dentro del archivo */etc/netconfig*, las líneas que hagan referencia a **udp6** y **tcp6**.

Ref.: <https://www.novell.com/support/kb/doc.php?id=7011354>

### OOM Killer y problemas de memoria en NFS Grandes

Si tenemos por ejemplo uno o varios NFS de 4 o 5 TB con una gran carga de tráfico y un load average extremadamente alto, podemos optar por estas opciones

```bash>
 /proc/sys/net/core/rmem_default
echo 219136 > /proc/sys/net/core/rmem_max

echo 219136 > /proc/sys/net/core/wmem_default
echo 219136 > /proc/sys/net/core/wmem_max

echo 0 > /proc/sys/net/ipv4/tcp_sack
echo 0 > /proc/sys/net/ipv4/tcp_timestamps

echo 20 > /proc/sys/vm/swappiness
echo 200 > /proc/sys/vm/vfs_cache_pressure

echo 2 > /proc/sys/vm/overcommit_memory
echo 80 > /proc/sys/vm/overcommit_ratio
```

## Referencias y TIPS

\* Si utilizas aceleradores de banda como Riverbed SteelHead, es necesario forzar a la versión 3 de NFS ya que la versión 4 no esta soportada

```
nfsvers=3
```

Compartir archivos con otros Linux : <http://www.estrellateyarde.org/discover/nfs>

Configurar NFS para SuSE Linux desde Yast : <http://www.eloihr.net/linux/index.php?page=nfs>

NFS HOWTO: [nfs.sourceforge.net/nfs-howto/](http://nfs.sourceforge.net/nfs-howto/)
