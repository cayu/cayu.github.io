---
title: "Sistema Operativo Windows"
date: 2019-10-29T11:48:57-03:00
---

<img src="/varios/tux_bsd_burning_windows.jpg" class="align-right" />

A menudo se puede dar que trabajemos en lugares donde utilizan este Sistema Operativo y como somos personas serias y responsables que somo por lo tanto no estamos acostumbrados a usar Windows y desconozcamos cosas simples que puedan causarnos problemas, que para un Administrador de Windows sean cosas muy cómunes.

## Windows

### How you can find out the name and IP address of the AD domain controller on your network

You can use Nslookup is a command-line tool that displays information you can use to diagnose Domain Name System (DNS) infrastructure.

1.  Click Start, and then click Run.
2.  In the Open box, type cmd.
3.  Type nslookup, and then press ENTER.
4.  Type set type=all, and then press ENTER.
5.  Type \_ldap.\_tcp.dc.\_msdcs.Domain_Name, where Domain_Name is the name of your domain, and then press ENTER.

<https://www.tecknowledgebase.com/6383/how-you-can-find-out-the-name-and-ip-address-of-the-ad-domain-controller-on-your-network/>

### Conocer el uptime de servidores Windows

Ejecutar desde una consola:

```
net statistics server
```

<http://support.microsoft.com/kb/555737/>

### Conocer la versión de Windows Exacta

Hacer Click en el menú “Inicio” y luego en “Ejecutar”

\- Escribir winver - Finalmente hacer click en “Aceptar”

### Change Executable Path Of Windows Services

En el registro ir a

1.  HKEY_LOCAL_MACHINE
2.  SYSTEM
3.  CurrentControlSet
4.  Services
5.  Service_Name

### Impresora de red muy lenta

Si por ejemplo tenemos el caso de una red LAN pequeña, en la cual una PC hace de servidor de impresión, para compartir la impresora. Y en algunas operatorias, como edición de propiedades de impresión se hace muy lenta deberemos al añadir la impresora, tener en cuenta estas opciones :

1.  add a local printer
2.  new port
3.  localport
4.  Asignar la ruta de la impresora \\maquina1\impresora2
5.  Realizar una prueba de impresión y de edición de propiedades

### Eliminar Impresión en Proceso (Pendiente)

Instrucciones

- Dirigirse a inicio, y luego das click en Ejecutar. Entonces teclea “cmd” y da un Enter.
- Ya en la ventana de comandos, escribe la siguiente instrucción y da enter: **net stop spooler**
- Esto detendrá el servicio encargado de administrar la cola de impresiones
- Ahora, ejecuta por separado cada una de las siguientes instrucciones:
- **del systemroot\system32\spool\printers\\.shd**
- **del systemroot\system32\spool\printers\\.spl**
- **net start spooler**

Lo hecho anteriormente eliminará los trabajos de impresión pendientes y reiniciará el servicio que detuvimos previamente. Seguidamente, debería ser suficiente para que la cola de impresión quede vacía y podamos preparar nuestra impresora para volver a intentar mandar algo a impresión.

### Habilitar PAE en Windows 2008 32 bits

```
BCDEdit /set pae ForceEnable
```

### Manejo de filesystems

```
fsutil fsinfo ntfsinfo c:
```

### Instalación de herramientas administrativas de Active Directory

Desde la línea de comandos de Ms-Windows

```
dism /online /enable-feature /featurename:RemoteServerAdministrationTools
dism /online /enable-feature /featurename:RemoteServerAdministrationTools-Roles
dism /online /enable-feature /featurename:RemoteServerAdministrationTools-Roles-AD
dism /online /enable-feature /featurename:RemoteServerAdministrationTools-Roles-AD-DS
dism /online /enable-feature /featurename:RemoteServerAdministrationTools-Roles-AD-DS-SnapIns
dism /online /enable-feature /featurename:RemoteServerAdministrationTools-Roles-AD-DS-AdministrativeCenter
dism /online /enable-feature /featurename:RemoteServerAdministrationTools-Roles-AD-DS-NIS
dism /online /enable-feature /featurename:RemoteServerAdministrationTools-Roles-AD-LDS
dism /online /enable-feature /featurename:RemoteServerAdministrationTools-Roles-AD-Powershell
```

## Productos

### PowerShell

- [Scripts varios de PowerShell](/notas/microsoft/windows/Scripts varios de PowerShell)

### Microsoft System Center

Microsoft System Center es un conjunto de aplicaciones desarrolladas por Microsoft para la gestión de los diferentes componentes de la infraestructura informática de una organización.

#### System Center Configuration Manager

**System Center Configuration Manager** (SCCM) es una solución que forma parte de la familia de productos Microsoft System Center. Se trata de una solución de software de administración para grandes ordenadores en red, optimizado para Windows que permite administrar de forma centralizada la configuración de todos los sistemas, físicos y virtuales de una organización o grupo de organizaciones.

### Microsoft Forefront

Microsoft Forefront es una gama de programas de seguridad tanto para clientes de Microsoft Windows y Windows Server. Contiene múltiples capas de defensa contra posibles problemas de vulnerabilidad de seguridad.
