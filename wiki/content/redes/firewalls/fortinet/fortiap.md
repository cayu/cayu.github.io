---
title: "FortiGate como controlador Wireless"
date: 2017-09-25T11:53:29-03:00
---

A partir de la versión de FortiOS 4.0 MR2 ya se soporta la funcionalidad de Controlador Wireless o lo que se conoce como Wireless Centralizado. Consiste en una funcionalidad que nos permite tener Access Points distribuidos, pero controlando los accesos y la seguridad desde un punto centralizado.

## FortiAP

### Administración

Ejemplo de configuración de IP y de controlador Wireless

```
cfg -a ADDR_MODE=STATIC
cfg -a AP_IPADDR=192.168.1.100
cfg -a AP_NETMASK=255.255.255.0
cfg -a IPGW=192.168.1.1
cfg -a AC_DISCOVERY_TYPE=1
cfg -a AC_IPADDR_1=192.168.1.155
cfg -c
```

### FortiAP CLI

<http://help.fortinet.com/fos50hlp/54/Content/FortiOS/fortigate-wireless-54/reference-fortiap-cli.htm>

The FortiAP CLI controls radio and network operation through the use of variables manipulated with the `cfg` command. There are also diagnostic commands.

The cfg command include the following

|              |                                     |
|--------------|-------------------------------------|
| `cfg -s` | List variables.                     |
| `cfg -a var=value` | Add or change a variable value.     |
| `cfg -c` | Commit the change to flash.         |
| `cfg -x` | Reset settings to factory defaults. |
| `cfg -r var` | Remove variable.                    |
| `cfg -e` | Export variables.                   |
| `cfg -h` | Display help for all commands.      |

The configuration variables are:

| Var | Description and Values |
|----|----|
| `AC_CTL_PORT` | WiFi Controller control (CAPWAP) port. Default 5246. |
| `AC_DATA_CHAN_SEC` | Data channel security.0 - Clear text1 - DTLS (encrypted)2 - Accept either DTLS or clear text (default) |
| `AC_DISCOVERY_TYPE` | 1 - Static. Specify WiFi Controllers2 - DHCP3 - DNS5 - Broadcast6 - Multicast0 - Cycle through all of the discovery types until successful. |
| `AP_IPADDR``AP_NETMASK``IPGW` | These variables set the FortiAP unit IP address, netmask and default gateway when ADDR_MODE is STATIC.Default 192.168.1.2 255.255.255.0, gateway 192.168.1.1. |
| `AC_HOSTNAME_1``AC_HOSTNAME_2``AC_HOSTNAME_3` | WiFi Controller host names for static discovery. |
| `AC_IPADDR_1``AC_IPADDR_2``AC_IPADDR_3` | WiFi Controller IP addresses for static discovery. |
| `AC_DISCOVERY_DHCP_OPTION_CODE` | Option code for DHCP server. Default 138. |
| `AC_DISCOVERY_MC_ADDR` | Multicast address for controller discovery. Default 224.0.1.140. |
| `ADDR_MODE` | How the FortiAP unit obtains its IP address and netmask.DHCP - FortiGate interface assigns address.STATIC - Specify in AP_IPADDR and AP_NETMASK.Default is DHCP. |
| `ADMIN_TIMEOUT` | Administrative timeout in minutes. Applies to Telnet and web-based manager sessions. Default is 5 minutes. |
| `AP_MGMT_VLAN_ID` | Non-zero value applies VLAN ID for unit management. Default: 0. |
| `AP_MODE` | FortiAP operating mode.0 - Thin AP (default)2 - Unmanaged Site Survey mode. See SURVEY variables. |
| `BAUD_RATE` | Console data rate: 9600, 19200, 38400, 57600, or 115200 baud. |
| `DNS_SERVER` | DNS Server for clients. If ADDR_MODE is DHCP the DNS server is automatically assigned. |
| `FIRMWARE_UPGRADE` | Default is 0. |
| `HTTP_ALLOW` | Access to FortiAP web-based manager1 - Yes (default), 0 - No. |
| `LED_STATE` | Enable/disable status LEDs.0 - LEDs enabled, 1 - LEDs disabled, 2 - follow AC setting. |
| `LOGIN_PASSWD` | Administrator login password. By default this is empty. |
| `STP_MODE` | Spanning Tree Protocol. 0 is off. 1 is on. |
| `TELNET_ALLOW` | By default (value 0), Telnet access is closed when the FortiAP unit is authorized. Set value to 1 to keep Telnet always available. |
| `WTP_LOCATION` | Optional string describing AP location. |
| **Mesh variables** |  |
| `MESH_AP_TYPE` | Type of communication for backhaul to controller:0 - Ethernet (default)1 - WiFi mesh2 - Ethernet with mesh backup support |
| `MESH_AP_SSID` | SSID for mesh backhaul. Default: fortinet.mesh.root |
| `MESH_AP_BSSID` | WiFi MAC address |
| `MESH_AP_PASSWD` | Pre-shared key for mesh backhaul. |
| `MESH_ETH_BRIDGE` | 1 - Bridge mesh WiFi SSID to FortiAP Ethernet port. This can be used for point-to-point bridge configuration. This is available only when MESH_AP_TYPE =1.0 - No WiFi-Ethernet bridge (default). |
| `MESH_MAX_HOPS` | Maximum number of times packets can be passed from node to node on the mesh. Default is 4. |
| The following factors are summed and the FortiAP associates with the lowest scoring mesh AP. |  |
| `MESH_SCORE_HOP_WEIGHT` |  Multiplier for number of mesh hops from root. Default 50. |
| `MESH_SCORE_CHAN_WEIGHT` |  AP total RSSI multiplier. Default 1. |
| `MESH_SCORE_RATE_WEIGHT` |  Beacon data rate multiplier. Default 1. |
| `MESH_SCORE_BAND_WEIGHT` |  Band weight (0 for 2.4GHz, 1 for 5GHz) multiplier. Default 100. |
| `MESH_SCORE_RSSI_WEIGHT` |  AP channel RSSI multiplier. Default 100. |
| **Survey variables** |  |
| `SURVEY_SSID` | SSID to broadcast in site survey mode (AP_MODE=2). |
| `SURVEY_TX_POWER` | Transmitter power in site survey mode (AP_MODE=2). |
| `SURVEY_CH_24` | Site survey transmit channel for the 2.4Ghz band (default 6). |
| `SURVEY_CH_50` | Site survey transmit channel for the 5Ghz band (default 36). |
| `SURVEY_BEACON_INTV` | Site survey beacon interval. Default 100msec. |

Diagnose commands include:

|              |                                                        |
|--------------|--------------------------------------------------------|
| `cw_diag help` | Display help for all diagnose commands.                |
| `cw_diag uptime` | Show daemon uptime.                                    |
| `cw_diag --tlog <onZQXVERB7ZQXoff>` | Turn on/off telnet log message.                        |
| `cw_diag --clog <onZQXVERB8ZQXoff>` | Turn on/off console log message.                       |
| `cw_diag baudrate [9600 ZQXVERB9ZQX 19200 ZQXVERB10ZQX 38400 ZQXVERB11ZQX 57600 ZQXVERB12ZQX 115200]` | Set the console baud rate.                             |
| `cw_diag plain-ctl [0ZQXVERB13ZQX1]` | Show or change current plain control setting.          |
| `cw_diag sniff-cfg ip port` | Set sniff server ip and port.                          |
| `cw_diag sniff [0ZQXVERB14ZQX1ZQXVERB15ZQX2]` | Enable/disable sniff packet.                           |
| `cw_diag stats wl_intf` | Show wl_intf status.                                   |
| `cw_diag admin-timeout [30]` | Set shell idle timeout in minutes.                     |
| `cw_diag -c wtp-cfg` | Show current wtp config parameters in control plane.   |
| `cw_diag -c radio-cfg` | Show current radio config parameters in control plane. |
| `cw_diag -c vap-cfg` | Show current vaps in control plane.                    |
| `cw_diag -c ap-rogue` | Show rogue APs pushed by AC for on-wire scan.          |
| `cw_diag -c sta-rogue` | Show rogue STAs pushed by AC for on-wire scan.         |
| `cw_diag -c arp-req` | Show scanned arp requests.                             |
| `cw_diag -c ap-scan` | Show scanned APs.                                      |
| `cw_diag -c sta-scan` | Show scanned STAs.                                     |
| `cw_diag -c sta-cap` | Show scanned STA capabilities.                         |
| `cw_diag -c wids` | Show scanned WIDS detections.                          |
| `cw_diag -c darrp` | Show darrp radio channel.                              |
| `cw_diag -c mesh` | Show mesh status.                                      |
| `cw_diag -c mesh-veth-acinfo` | Show mesh veth ac info, and mesh ether type.           |
| `cw_diag -c mesh-veth-vap` | Show mesh veth vap.                                    |
| `cw_diag -c mesh-veth-host` | Show mesh veth host.                                   |
| `cw_diag -c mesh-ap` | Show mesh ap candidates.                               |
| `cw_diag -c scan-clr-all` | Flush all scanned AP/STA/ARPs.                         |
| `cw_diag -c ap-suppress` | Show suppressed APs.                                   |
| `cw_diag -c sta-deauth` | De-authenticate an STA.                                |

## Troubleshooting

Para la gestión de problemas en el controlador wireless de FortiOS siempre es aconsejable de antemano que el controlador apunte a un Syslog para poder analizar los mensajes de debug.

Entre otras cosas, necesitamos tener presente por ejemplo el siguiente puerto donde va el tráfico CAPWAP

- UDP/5246
- UDP/5247

Lo primero que debemos hacer es verificar si el equipo efectivamente se esta comunicando con el controlador :

- fap-get-status
- diagnose sniff packet any “port 5246 and src \[IP FortiAP\]” 4

### Opción I

Activar debug en el FortiAP :

```
cw_diag plain-ctl 1
cw_debug app cwWtpd 0x7fff
```

Desde el controlador :

```
diag wireless-controller wlac plain-ctl [FortiAP S/N]
```

Desactivar el debug desde el FortiAP :

```
cw_debug app cwWtpd 0x0
```

### Opción II

Verificar tráfico entre el cliente y el FortiAP desde el controlador :

```
diagnose wireless-controller wlac sta_filter clear
diagnose wireless-controller wlac sta_filter [MAC_CLIENTE] 2
diagnose debug enable
```

Verificar tráfico entre el controlador y el FortiAP :

```
diagnose wireless-controller wlac wtp_filter [FortiAP S/N] 0-[IP FortiAP]:5246 2
diagnose de console timestamp en
diagnose de application cw_acd 0x7ff
diagnose de en
```

En ambos casos para finalizar el debug :

**Controlador**

```
diagnose debug disable
```

### Referencias

- <http://cookbook.fortinet.com/wifi-network-troubleshooting/>
- <http://kb.fortinet.com/kb/documentLink.do?externalID=FD33214>
- <http://www.blisli.com/b/?p=38>
