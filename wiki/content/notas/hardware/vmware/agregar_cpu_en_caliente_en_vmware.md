---
title: "Agregar CPU en caliente en VMWare"
date: 2015-04-28T11:39:21-03:00
---

Una vez que asignamos CPU’s desde el cliente VMWare, nos fijamos en que estado están ellas en el sistema, tenemos que fijarnos por ejemplo si teniamos dos CPU’s *cpu0 cpu1* y agregamos 2 mas *cpu2 cpu3* dentro del archivo **/sys/devices/system/cpu/cpu\[numero\]/online** si el valor esta en 1 o en 0, si esta en 0 debemos setearlo con un **echo 1 \> online**.

To automatically activate a processor when using hot-add, create a file /etc/udev/rules.d/99-vmware-cpuhotplug-udev.rules in your virtual machine with the following contents:

```bash>
If you already added a processor with hot-add, activate the processors by issuing (as root) the following command:

<code bash>
for a in /sys/devices/system/cpu/cpu*/online; do echo "1" > "$a"; done
```

Ref.: <http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=1015501>
