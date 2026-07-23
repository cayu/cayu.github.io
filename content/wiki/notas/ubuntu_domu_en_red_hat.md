---
title: "Xen Red Hat Dom0 y Ubuntu DomU"
date: 2009-07-05T00:03:21-03:00
---

Teniendo instalado un servidor Red Hat como Dominio 0 con Xen, y si surge la necesidad de instalar un Dominio U con ubuntu dentro.

Podemos bajarmos la imagen de ubuntu feisty de <http://jailtime.org/> , lo descomprimimos y montamos la imagen del disco con

mount -o loop ubuntu.7-04.img /mnt

luego hacemos un chroot /mnt y una vez dentro un

apt-get install xen-image-2.6.19-4-server

si instalamos el apt-get install xen-image-2.6.19-4-generic vamos a tener problemas ya que no viene con soporte de PAE, y si el dom0 tiene pae, este kernel no va a andar

luego copiamos el vmlinuz y el initrd generado al /boot local de nuestro dom0

##### /boot

```
System.map-2.6.19-4-server  initrd.img-2.6.19-4-server
config-2.6.19-4-server      vmlinuz-2.6.19-4-server
```

el ejemplo del archivo de configuracion de xen nuestro puede ser como este

##### vm-feisty.cfg

```bash
name = 'vm-feisty'
memory = 128
kernel = '/boot/vmlinuz-2.6.19-4-server'
ramdisk = '/boot/initrd.img-2.6.19-4-server' 
root = '/dev/sda1 ro'
vif = [ 'mac=00:16:3e:38:db:55, bridge=xenbr0']

disk = ['file:/root/ubuntu.7-04.img,sda1,w','file:/root/ubuntu.swap,sda2,w']
vcpus=1
on_reboot   = 'restart'
on_crash    = 'restart'
```
