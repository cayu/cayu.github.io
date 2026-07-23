---
title: "iDrac Dell"
date: 2014-02-17T17:26:54-03:00
---

### iDrac Dell

IPMI es un estándar que encontramos en el hardware de muchos servidores, se trata de un chipset que permite obtener información del estado de diferentes componentes hardware, temperaturas, voltajes, etc. Este estándar lo conforman empresas como HP, Dell e Intel.

Ejemplo de como activarlo desde el SSH de iDrac 6

```
racadm config -g cfgIpmiLan -o cfgIpmiLanEnable 1
racadm config -g cfgIpmiLan -o cfgIpmiLanPrivilegeLimit <nivel>
```

donde \<nivel\> es uno de los siguientes valores:

- 2 (Usuario)
- 3 (Operador)
- 4 (Administrador)

Como ver la configuración actual

```
racadm getconfig -g cfgIpmiLan
```

<http://support.dell.com/support/edocs/software/smdrac3/idrac/idrac1.11/sp/ug/chap09.htm>?~file=/software/smdrac3/idrac/idrac1.11/sp/ug/chap09.htm&~f=

Como obtener los datos desde nuestro sistema operativo

```
ipmitool -I lan -H 10.0.0.10 -U user -P pass chassis status
ipmitool -I lan -H 10.0.0.10 -U user -P pass sdr list
ipmitool -I lan -H 10.0.0.10 -U user -P pass sdr type "Power Supply"

Status           | 64h | ok  | 10.1 | Presence detected
Status           | 65h | ok  | 10.2 | Presence detected, Power Supply AC lost
Status           | 66h | ok  | 10.3 | Presence detected
Status           | 67h | ok  | 10.4 | Presence detected
PS Redundancy    | 74h | ok  |  7.1 | Redundancy Degraded
```

```
/usr/sbin/ipmimonitoring -h 10.15.3.107 --config-file /etc/freeipmi.conf -u user -p pass
```

| Record_ID | Sensor Name | Sensor Group | Monitoring Status | Sensor Units | Sensor Reading |
|----|----|----|----|----|----|
| 12 | Ambient Temp | Temperature | Nominal | C | 25.000000 |
| 22 | CMOS Battery | Battery | Nominal | N/A | ‘OK’ |
| 24 | VCORE PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 25 | VCORE PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 26 | VCORE | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 27 | VCORE | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 28 | VCACHE PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 29 | VCACHE PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 30 | VCACHE | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 31 | VCACHE | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 32 | VIO PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 33 | VIO PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 34 | CPU 1.8 PLL PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 35 | 8V Rear PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 36 | 8V Front PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 37 | 5V Rear PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 38 | 5V Front PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 39 | 3.3V PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 40 | 1.8V PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 41 | 1.5V PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 42 | 1.1V PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 43 | 0.9V PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 44 | Mem VR PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 45 | Mem VR PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 46 | Mem VR PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 47 | Mem VR PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 48 | Mem VR PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 49 | Mem VR PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 50 | Mem VR PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 51 | Mem VR PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 52 | VR PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 53 | VR1 PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 54 | VR2 PG | Voltage | Nominal | N/A | ‘State Deasserted’ |
| 58 | Heatsink Pres | Entity Presence | Nominal | N/A | ‘Entity Present’ |
| 59 | iDRAC6 Ent Pres | Entity Presence | Nominal | N/A | ‘Entity Present’ |
| 60 | USB Cable Pres | Entity Presence | Nominal | N/A | ‘Entity Present’ |
| 61 | Stor Adapt Pres | Entity Presence | Nominal | N/A | ‘Entity Present’ |
| 62 | PCIe Riser Pres | Entity Presence | Critical | N/A | ‘Entity Absent’ |
| 63 | IO Riser Pres | Entity Presence | Nominal | N/A | ‘OK’ |
| 64 | FAN 1 RPM | Fan | Nominal | RPM | 2400.000000 |
| 65 | FAN 2 RPM | Fan | Nominal | RPM | 2400.000000 |
| 66 | FAN 3 RPM | Fan | Nominal | RPM | 2400.000000 |
| 67 | FAN 4 RPM | Fan | Nominal | RPM | 2760.000000 |
| 68 | FAN 5 RPM | Fan | Nominal | RPM | 2640.000000 |
| 69 | FAN 6 RPM | Fan | Nominal | RPM | 2640.000000 |
| 74 | Presence | Entity Presence | Nominal | N/A | ‘Entity Present’ |
| 75 | Presence | Entity Presence | Nominal | N/A | ‘Entity Present’ |
| 76 | Presence | Entity Presence | Nominal | N/A | ‘Entity Present’ |
| 77 | Presence | Entity Presence | Nominal | N/A | ‘Entity Present’ |
| 78 | Presence | Entity Presence | Nominal | N/A | ‘Entity Present’ |
| 79 | Presence | Entity Presence | Nominal | N/A | ‘Entity Present’ |
| 80 | Presence | Entity Presence | Nominal | N/A | ‘Entity Present’ |
| 81 | Presence | Entity Presence | Nominal | N/A | ‘Entity Present’ |
| 82 | Presence | Entity Presence | Nominal | N/A | ‘Entity Present’ |
| 83 | Status | Processor | Nominal | N/A | ‘Processor Presence detected’ |
| 84 | Status | Processor | Nominal | N/A | ‘Processor Presence detected’ |
| 85 | Status | Processor | Nominal | N/A | ‘Processor Presence detected’ |
| 86 | Status | Processor | Nominal | N/A | ‘Processor Presence detected’ |
| 87 | Status | Power Supply | Nominal | N/A | ‘Presence detected’ |
| 88 | Status | Power Supply | Nominal | N/A | ‘Presence detected’ |
| 89 | Status | Power Supply | Nominal | N/A | ‘Presence detected’ |
| 90 | Status | Power Supply | Nominal | N/A | ‘Presence detected’ |
| 91 | Current | Current | Nominal | A | 0.800000 |
| 92 | Current | Current | Nominal | A | 0.880000 |
| 93 | Current | Current | Nominal | A | 0.680000 |
| 94 | Current | Current | Nominal | A | 0.800000 |
| 95 | Voltage | Voltage | Nominal | V | 222.000000 |
| 96 | Voltage | Voltage | Nominal | V | 224.000000 |
| 97 | Voltage | Voltage | Nominal | V | 224.000000 |
| 98 | Voltage | Voltage | Nominal | V | 222.000000 |
| 99 | Riser Config | Cable/Interconnect | Nominal | N/A | ‘Cable/Interconnect is connected’ |
| 100 | OS Watchdog | Watchdog 2 | Nominal | N/A | ‘OK’ |
| 102 | Intrusion | Physical Security | Nominal | N/A | ‘OK’ |
| 103 | PS Redundancy | Power Supply | Nominal | N/A | ‘Fully Redundant’ |
| 104 | Fan Redundancy | Fan | Nominal | N/A | ‘Fully Redundant’ |
| 105 | System Level | Current | Nominal | W | 700.000000 |
| 107 | Drive | Drive Slot | Nominal | N/A | ‘Drive Presence’ |
| 109 | Cable SAS A | Cable/Interconnect | Nominal | N/A | ‘Cable/Interconnect is connected’ |

Si queremos elegir mostrar solo uno de los grupos deberemos agregar a la linea de comandos la opcion *-g* y el número *-g 103*.

| Record_ID | Sensor Name | Sensor Group | Monitoring Status | Sensor Units | Sensor Reading |
|----|----|----|----|----|----|
| 103 | PS Redundancy | Power Supply | Nominal | N/A | ‘Fully Redundant’ |

Ejemplo consultar el Voltage y separalo en ;

```bash
ipmimonitoring  -h 10.1.1.7 --config-file /usr/local/nagios/etc/ipmi/freeipmi-esxi.conf --quiet-cache --sdr-cache-recreate -s 95,96,97,98 -g Voltage |tail -n 4 | cut -d "|" -f 6    | sed -e 's/^[ \t]*/Voltage=/' | sed ':a;N;$!ba;s/\n/ /g' | sed -e 's/  /;/g'| sed -e 's/^[ \t]*//' 
Voltage=218.000000;Voltage=220.000000;Voltage=218.000000;Voltage=218.000000 
```
