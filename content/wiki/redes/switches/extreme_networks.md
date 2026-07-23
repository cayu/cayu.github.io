---
title: "Extreme Networks"
date: 2017-09-13T12:16:08-03:00
---

<http://www.extremenetworks.com>

## Configuración Minicom

```ini>
===== Administración =====
=== Setup summary ===
  - You must use the console serial connection to set up the switch. Use PuTTY, HyperTermial or other terminal emulation software. Use a null-modem cable and 9600,n,8,1 line settings with XON/XOFF flow control.
  - The switch will ask for login and password. Default login is admin. Default password is <blank>.
  - If the switch has been previously configured, it can be restored to factory defaults with unconfigure switch all. The switch will ask for confirmation then reboot.
  - After login, an unconfigured switch ask half a dozen questions. Default answers (No) for all question is an acceptable configuration starting point.
  - (optional) Assign an IP address for management with configure vlan default ipaddress <ip address> <net mask>
  - (optional) Enable web interface with enable web http
  - (optional) You may now connect to the switch through any of the front-panel Ethernet jacks and establish a telnet connection to continue configuration.
  - Q-Sys uses DSCP. Enable DSCP examination of incoming frames with enable diffserv examination port all command. The switch may respond with a Warning: Enabling diffserv examination will cause dot1p replacement of 802.1q tagged packets. message. This is normal and expected.
  - 802.1p based QoS is enabled by default and generally should be disabled with disable dot1p examination ports all command. The switch may respond with a WARNING: The intended usage of this command is when another QoS traffic grouping (e.g. diffserv examination, port QoS, vlan QoS, ACL QoS) is configured. Disabling all QoS traffic groupings will still result in 802.1p QoS selection. This is normal and expected.
  - Assign clock traffic as highest priority QP8 configure diffserv examination code-point 46 qosprofile qp8
  - Q-Sys audio is assigned to QP5. Use create qosprofile qp5 to create the QoS profile then configure diffserv examination code-point 34 qosprofile qp5 to assign audio traffic to it.
  - Use the save command to make these settings permanent. Confirm overwrite of current settings.
  - Test Managment port.
=== Reset config ===
Remove the power cable of the switch or power off the switch for 60 seconds.After that switch on and we can see the switch is booting via the terminal window.It will show “Press and hold spacebar to enter BootStrap mode“,we don’t want to enter this mode.Skip this and it will again show “Press and hold the spacebar to enter BootRom mode“.Just hold the spacebar and it will get you to BootRom prompt.Then give the commands as follows
<code>
BootRom>config none
BootRom>boot
```

Then we can see the booting process and it will reach at a point where username and password is asked.Enter username as “admin” and there is no password set for this account by default.So don’t provide any password and just hit enter.

After logging in successfully the prompt will ask you for enabling/disabling telnet,snmp,failsafe account etc.Configure your required options accordingly.After setting up all these it will take you to the basic command line.There we can set the password for the accounts.By default there are two accounts available in an extreme switch.One is “admin” which is the privileged account and other account is the “user” with only basic options.Following is the command to set password for admin account.

#### Basic Config

##### Config

- configure vlan mgmt ipaddress 192.168.1.50/25
- configure iproute add default 192.168.1.1 vr vr-mgmt
- configure vlan default ipaddress 192.168.2.5/24
- configure vlan default add ports 1
- download image 192.168.1.201 summitX-15.3.5.2.xos vr “VR-Default” secondary
- use image secondary
- reboot
- run update
- download image 192.168.1.201 summitX-15.3.5.2-ssh.xmod vr “VR-Default” primary
- run update
- start process exsshd
- enable ssh2

##### Install the SSH module:

1.  Download the module image to your TFTP server.
2.  Determine the active partition for your switch by typing: “show switch”
3.  The display shows the current selected and booted image partition.
4.  Download and install the module image to the active partition by typing the following command (specify the virtual router you are using to connect to the TFTP server): “download image XXX-ssh.xmod vr vr-mgmt (primary or secondary)”
5.  The system displays the following message: “Do you want to install image after downloading” (y - yes, n - no, - cancel)
6.  Type y, for yes, so that the image will be installed after downloading.
7.  Type “run update”
8.  To verify that the SSH module is running, issue the following command: “show process”
9.  You will see a process named “exsshd” listed. You can then configure and “enable ssh” on the switch.
10. To verify that the SSH module is installed, issue the following command: “show management”
11. The system displays the following message: SSH access : Disabled (Key invalid, tcp port 22 vr all)
12. If the SSH module is not installed, the system displays a message similar to: SSH Access : ssh module not loaded.

##### Config Syslog

- configure syslog add 192.168.54.12:514 vr VR-Default local7
- enable log target syslog 192.168.54.12:514 vr VR-Default local7
- configure log target syslog 192.168.54.12:514 vr VR-Default local7 filter DefaultFilter severity Debug-Data
- configure log target syslog 192.168.54.12:514 vr VR-Default local7 match Any
- configure log target syslog 192.168.54.12:514 vr VR-Default local7 format timestamp seconds date Mmm-dd event-name none priority tag-name

##### Config NTP

- configure sntp-client primary \$sntpserver
- configure timezone \$tzAR \$dst\_-180
- enable sntp-client

##### Config SNMP

- summit \# enable snmp access
- summit \# configure snmp delete community readonly public
- summit \# configure snmp add trapreceiver \<IP\> community \<communaute\>
- summit \# sh management

##### Config Banner

- configure banner ?

```
line1....<SPACE><Enter>
#blank line <SPACE> <Enter>
line2 <SPACE> <Enter>
end script with two enters
```

#### Direcciones IP

**Por DHCP**

```
enable dhcp vlan  all
```

**Estatica**

```
disable dhcp vlan all
configure vlan Default ipaddress 192.168.1.159 255.255.254.0
configure iproute add default 192.168.1.1
```

#### Usuarios

```
create account admin cayu
```

#### vlans

```
summit# create vlan vlan_200
summit# configure vlan_200 tag 200
summit# configure vlan_200 ipaddress 192.168.200.254 /24
summit# create vlan vlan_10
summit# configure vlan_10 tag 10
summit# configure vlan_10 ipaddress 192.168.10.254 /24
summit# enable ipforwarding vlan_200
summit# enable ipforwarding vlan_10
summit# configure iproute add default <next_hop_ip>
```

## TIPs

### SSH

Si tenemos un mensaje como este :

```
Unable to negotiate with 10.54.90.4 port 22: no matching key exchange method found. Their offer: diffie-hellman-group1-sha1
```

Podemos probar con estas opciones de SSH

```
ssh -o HostKeyAlgorithms=+ssh-dss -oKexAlgorithms=+diffie-hellman-group1-sha1 admin_sw@10.54.90.4
```

## Referencias

- <https://gtacknowledge.extremenetworks.com/articles/How_To/How-to-console-into-Extreme-and-Enterasys-switches>
