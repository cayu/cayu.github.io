---
title: "Utilizar dos o mas placas de red con Xen"
date: 2009-11-25T14:24:07-03:00
---

Ejemplo de como usar si tenemos un eth0 y un eth1

Creamos un script **/etc/xen/scripts/network-multi-bridge**

```bash>
Dentro de **/etc/xen/xend-config.sxp** agregamos

<code>
(network-script network-multi-bridge)
```

En la configuracion de nuestra maquina virtual

```
name = "virtserv"
memory = 2048
vcpus = 1
bootloader = "/usr/bin/pygrub"
on_poweroff = "destroy"
on_reboot = "restart"
on_crash = "restart"
disk = [ "phy:/dev/mapper/VolGroup00-root,hda1,w ]
vif = [ "mac=00:17:4e:37:f5:47,bridge=xenbr0", "mac=00:17:4e:37:f5:48,bridge=xenbr1" ]
```
