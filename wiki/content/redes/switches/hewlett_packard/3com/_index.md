---
title: "3COM"
date: 2017-02-15T11:30:53-03:00
---

HP adquirió 3Com, una empresa que desarrolla equipos de telecomunicaciones e infraestructuras para redes, con la intención de mejorar el posicionamiento de la empresa en China y poder competir con Cisco Systems, que actualmente lidera este campo de la tecnología.

## 5500 Series

### Configuración Minicom

```ini>
==== Pantalla de booteo ====
<code>
Starting......                                          
                                                        
      ******************************************************************
      *                                                                *
      *   SuperStack 4 Switch 5500G-EI 24-Port BOOTROM, Version 4.03   *
      *                                                                *
      ******************************************************************
                                                        
      Copyright (c) 2004-2007 3Com Corporation and its licensors.
      Creation date   : Jan 23 2007, 11:19:21           
      CPU type        : BCM1122
      CPU Clock Speed : 400MHz
      BUS Clock Speed : 33MHz
      Memory Size     : 128MB

      Mac Address     : 0022573c6480


Press Ctrl-B to enter Boot Menu... 0
Auto-booting...
Decompress Image....................................................................!
Starting at 0x80020000...

Press Ctrl-B to enter Boot Menu... 1
 
password: 


  BOOT  MENU

1. Download application file to flash
2. Select application file to boot
3. Display all files in flash
4. Delete file from flash
5. Modify bootrom password
6. Enter bootrom upgrade menu
7. Skip current configuration file
8. Set bootrom password recovery
9. Set switch startup mode
0. Reboot

Enter your choice(0-9):
```

### Configuración

#### Resetear configuración

The following commands are used to return the switch to factory default settings:

**\<5500G-EI\>reset saved-configuration** – This command will remove the user saved configuration file. To see which file will be deleted, enter display startup .

**\[5500G-EI\]change self-unit to auto-numbering** – This command will remove any saved Switch unit number (enter display ftm topo to see which switches in a stack are configured with a manual unit number, these are marked with priority 5, automatic numbered switches are marked with a priority of 10).

**\[5500G-SI\]rsa local-key-pair destroy** – This command will remove any local rsa keys used for SSH access.

**\<5500G-SI\>startup bootrom-access enable** – This command will re-enable access to the bootrom.

From the boot menu, the following commands may be used to select the appropriate defaults: **Modify bootrom password** – Factory default is no bootrom password. **Skip current configuration file** – Factory default is process configuration file. **Set bootrom password recovery** – Factory default is enable bootrom password recovery. **Set switch startup mode** – Factory default is fast startup.

**\<5500G-EI\> undo clock timezone** – This command will return the timezone to the factory default UTC timezone. Enter display clock to see what timezone is configured.

Referencia: <http://serverfault.com/questions/297967/how-do-i-factory-reset-a-3com-5500g-ei>

### Administración

#### port-security

```
[5500G-SI] display port-security
```

```
interface GigabitEthernet1/0/5
port access vlan 8

port-security max-mac-count 64
port-security port-mode secure
port-security port-mode autolearn
port-security intrusion-mode blockmac
port-security mac-address security 0002-0000-0015 vlan 1
port-security mac-address security 0002-0000-0014 vlan 1
port-security mac-address security 0002-0000-0013 vlan 1
port-security mac-address security 0002-0000-0012 vlan 1
port-security mac-address security 0002-0000-0011 vlan 1
```

<http://www.h3c.com.hk/Technical_Support___Documents/Technical_Documents/Switches/H3C_S5500_Series_Switches/Configuration/Operation_Manual/H3C_S5500-EI_OM_2102(V1.01)/200711/210978_1285_0.htm>

#### Comandos especiales

**\_hidecmd**

*Now you enter a hidden command view for developer’s testing, some commands may affect operation by wrong use, please carefully use it with our engineer’s direction.*
