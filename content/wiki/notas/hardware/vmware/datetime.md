---
title: "Parametros para Vmware por cambio de hora de servidores"
date: 2015-04-28T11:38:12-03:00
---

Para el caso de que un servidor virtual, cambie su hora, hay que agregar estos parámetros, con la vm apagada :

<http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=1189>

[Timekeeping best practices for Linux guests](http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=1006427)

La idea es la de deshabilitar completamente time sync con el host.

“…Select the virtual machine in the VMware Infrastructure Client inventory. On the Summary tab, click Edit Settings, then click the Options tab and select General (under Advanced). Click Configuration Parameters, then click Add Row and add this information:

```ini
tools.syncTime = "0"
time.synchronize.continue = "0"
time.synchronize.restore = "0"
time.synchronize.resume.disk = "0"
time.synchronize.shrink = "0"
time.synchronize.tools.startup = "0"
```
