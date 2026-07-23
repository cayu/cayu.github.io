---
title: "Network Policy Server (NPS)"
date: 2019-08-30T12:05:52-03:00
---

Servidor de directivas de redes corre sobre : *Windows Server® 2008 and Windows Server® 2008 R2* en adelante.

Permite crear y aplicar directivas de acceso a la red en toda la organización con fines de mantenimiento de clientes, autenticación de solicitudes de conexión y autorización de solicitudes de conexión. Además, puede usar NPS como un proxy RADIUS (Servicio de autenticación remota telefónica de usuario) para reenviar solicitudes de conexión a un servidor que ejecute NPS u otros servidores RADIUS que configure en grupos de servidores RADIUS remotos.

Referencia : <https://technet.microsoft.com/es-es/library/cc732912.aspx>

## Introducción

En los últimos años se ha producido una necesidad de acceso a las redes corporativas utilizando distintos medios de acceso, además, se ha intensificado el uso de aplicaciones que requieren que la red le garantice condiciones de seguridad y confidencialidad para el tráfico de los datos que intercambian. En respuesta a esta demanda surgen iniciativas y tecnologías para resolverlas que se engloban dentro de lo que se conoce como Control de Acceso a la Red.

## Objetivo

El foco principal es reducir las amenazas de seguridad, las mismas pueden ser causadas por usuarios de una organización sin ninguna intención, por ejemplo:

- Si no tenemos control de quienes se conectan a nuestra red

<img src="/notas/microsoft/nps_intro.jpg" class="align-center" alt="nps_intro.jpg" /> Las soluciones NAC son diferentes pero pueden ser clasificadas en dos grupos

- **Clienless** no necesita de ningún software instalado en los dispositivos
- **Client-based** un componente de software es preinstalado en los dispositivos para poder asistir al proceso de NAC

Existe un numero de factores para decidir cual tipo de solución es la mas adecuada dependiendo de cómo esta formada la organización, NAC basado en cliente provee mas detalle del dispositivo pero también hay que tener en cuante que requiere su instalación equipo por equipo.

**Pre admisión NAC** : Determina que un dispositivo cumpla con ciertos criterios predeterminados antes de permitirle el acceso a la red. Si esos criterios no se cumplen, no permite que el dispositivo se conecte a la red, o le asigna un acceso restringido.

## Soluciones de Microsoft NAP (Network Access Protection)

Microsoft ofrece una variedad de tecnologías

- Microsoft Network Protection (NAP)
- 802.1X vía Microsoft

### NAP (Network Access Protection)

Es la solución propietaria de Microsoft. Consta de una plataforma de aplicación pensada para ser soportada por el sistema operativo de Windows.

      NAP protege las redes y dispositivos que componen la red aplicando políticas de confianza basadas en requerimientos de salud que deben cumplir los dispositivos al componer una red.
      La mayor diferencia que presenta esta solución es que al no ser fabricante de equipos de networking Microsoft basa su despliegue de agentes y aplicaciones en el lado del cliente y en el uso de distinto tipo de servidores en el lado de la red tanto para la verificación como para la admisión.
      Microsoft define NAP como sigue: La protección del acceso de red (Network Access Protection NAP) es una plataforma que proporciona componentes para la aplicación de políticas para ayudar a asegurarse que las computadoras conectadas o a conectarse en una red cumplan con los requisitos para la salud del sistema.

### Microsoft 802.1X

802.1X es una autenticación basada en el port que puede aplicarse tanto para redes alámbricas como inalámbricas, la autenticación comienza en el port de acceso y tiene dos componentes primarios:

- **Suplicante** : requiere el acceso a la red
- **Autenticador** : autentica suplicantes y decide o no darle acceso a red.

Para entender mejor **802.1X** podemos hablar de ports controlados y ports no controlados.

- Un port **controlado** es aquel que nos habilita a ciertas direcciones de red.
- Un port **no controlado** nos permite un acceso irrestricto a la red.

<img src="/notas/microsoft/nps_port.jpg" class="align-center" data-query="?" alt="nps_port.jpg" />

## Instalación sugerida

Podemos configurar nuestros equipos controladores de red, tanto wifi como cableada a que por cada pedido de acceso a la red, contacte al servicio autorizante.

### Configuración de equipos de red

#### Controlador WIFI Fortinet WLC

#### Switch Aruba

Ejemplo de configuración en un Switch Aruba 2930M con el puerto 24 afectado por el sistema de Control de Acceso a la red, con la VLAN ID 666 como VLAN de cliente no autorizado.

```
#Define authentication host and pre-shared key.
radius-server host 192.168.1.55 key "SecretKeyDeRadius"
#Enable processing of Disconnect and Change of Authorization messages from authentication server
radius-server host 192.168.1.55 dyn-authorization
snmp-server community "public" unrestricted
#Set selected authentication mode
aaa authentication port-access eap-radius
#Configure switch to use GVRP VLANs assigned by RADIUS server; prevent GVRP-enabled clients from gaining access to additional tagged VLANs
aaa port-access gvrp-vlans
int 24 unknown-vlans disable
#Configure specified ports for authentication
aaa port-access authenticator 24
aaa port-access authenticator 24 reauth-period 5
#Assign authenticated client VLAN to authenticator ports
aaa port-access authenticator 24 auth-vid 1
#Assign unauthenticated client VLAN to authenticator ports
aaa port-access authenticator 24 unauth-vid 666
#Configure selected backup authentication method
aaa authentication port-access eap-radius authorized
#Set authenticator ports to client-based mode and configure client limit
aaa port-access authenticator 24 client-limit 1
#Activate authentication on assigned ports with configured options
aaa port-access authenticator active
oobm
   ip address dhcp-bootp
   exit
vlan 1
   name "DEFAULT_VLAN"
   untagged 1-24
   ip address dhcp-bootp
   ipv6 enable
   ipv6 address dhcp full
   exit
vlan 666
   name "VLAN666"
   no ip address
   exit
```

### Configuración de clientes de red

#### Configuración en cliente Windows para conexión cableada

Para que los clientes Windows puedan autenticarse y conectarse a la red, primero deberemos habilitar el servicio de “Configuración automática de redes cableadas” : El Servicio de configuración automática de redes cableadas (DOT3SVC) se encarga de realizar la autenticación IEEE 802.1X en interfaces Ethernet. Si la implementación de la red cableada actual exige autenticación 802.1X, el servicio DOT3SVC debe configurarse de modo que se ejecute para establecer la conectividad de nivel 2 y/o proporcionar acceso a los recursos de red. Las redes cableadas que no exigen autenticación 802.1X no se verán afectadas por el servicio DOT3SVC. <img src="/notas/microsoft/nps_windows_srv.png" class="align-center" alt="nps_windows_srv.png" />

<img src="/notas/microsoft/nps_windows_srv_2.png" class="align-center" alt="nps_windows_srv_2.png" />

## Operaciones

Backup de la configuración :

```
netsh nps show config
netsh nps export filename = c:\temp\npsconfig.xml exportPSK = YES
```
