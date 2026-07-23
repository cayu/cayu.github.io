---
title: "Hewlett Packard"
date: 2016-07-27T11:46:20-03:00
---

[Debug y análisis de equipos HP/3com](/redes/switches/hewlett_packard/Debug y análisis de equipos HP/3com)

## Equipos

### HP 1910/1920 Series

#### Configuración Minicom

```ini>
=== How to Enable Full Command Line Access ===
== HP 1910  ==
<file>
<HP 1910G Switch>_cmdline-mode on
All commands can be displayed and executed. Continue? [Y/N]Y

↓↓↓↓↓↓↓↓ Default password is below, ↓↓↓↓↓↓↓↓
Please input password: 512900

Warning: Now you enter an all-command mode for developer's testing, 
some commands may affect operation by wrong use, please carefully 
use it with our engineer's direction.
<HP 1910G Switch> system-view 
System View: return to User View with Ctrl+Z.
[HP 1910G Switch]?
</file>
== HP 1920-24G or 1920-48G ==
<code>
<HP 1920G Switch> _cmdline-mode on
All commands can be displayed and executed. Continue? [Y/N] Y

↓↓↓↓↓↓↓↓ Default password is below, ↓↓↓↓↓↓↓↓
Please input password: Jinhua1920unauthorized

<HP 1920G Switch> system-view
System View: return to User View with Ctrl+Z.
```

### Usuarios y autenticación

#### HP 1920 Series Switch default username and password

- **Username:** admin
- **Password:** \<blank\>

#### Dar de alta un usuario administrador

```
local-user cayu
authorization-attribute level 3
service-type ssh telnet terminal
```

#### Dar de alta el servicio SSH

```
ssh server enable
```
