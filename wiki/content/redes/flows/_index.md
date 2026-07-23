---
title: "Network Flow"
date: 2018-10-19T12:49:12-03:00
---

Los flujos de red nos dan el medio para ver el tráfico que fluye a través de una red. Tenemos las sesiones de conexión a cada punto y en diferentes capas de comunicación.

# Netflow

NetFlow es un protocolo de red para recolectar información sobre tráfico IP. Es de carácter necesario para administrar la red.

Ejemplo de configuración de Netflow para routers Cisco :

```
ip flow-export version <5|9>
ip flow-export destination <ip> <port>
```

# Sflow

sFlow es protocolo para el muestreo de paquetes, para el monitoreo de redes.

Ejemplo de configuración en switches Aruba :

```
sflow 1 destination <ip> <port>
```

Ejemplo de configuración en Firewalls de Fortinet :

```
config system sflow
    set collector-ip <ip>
    set collector-port <port>
end
```

En la interfaz :

```
...
edit "port8"
...
set sflow-sampler enable
set sample-rate <every_n_packets>
set sample-direction [tx | rx | both]
set polling-interval <seconds>
...
```

Actualmente los switches modernos soportan Netflow, en el caso de poseer un switch que no soporte envío de Netflow, podemos realizar un port mirroring y analizar los datos obtenidos de allí con algun software analizador de trafico o un sniffer. El problema de esto, es que aumenta el uso de CPU del Switch en cuestión.

## Monitoreo de flujos de red

### NFDUMP - Netflow processing tools

**nfdump** is a set of tools to collect and process netflow data. It’s fast and has a powerful filter pcap like syntax. It supports netflow versions v1, v5, v7, v9 and IPFIX as well as a limited set of sflow. It includes support for CISCO ASA (NSEL) and CISCO NAT (NEL) devices, which export event logging records as v9 flows. nfdump is fully IPv6 compatible.

Features

- Processes netflow v1, v5, v7 and v9 FNF
- Powerful pcap like syntax for netflow
- Fully IPv6 compatible
- Processes IPFIX (beta)

Las nomenclaturas de directorios pueden ser asi :

```
       -S <num>
          Allows  to  specify  an additional directory sub hierarchy to store the data files. The default is 0, no sub hierarchy, which means the files go directly in the base directory (-l). The base directory (-l) is concatenated with the specified sub hierarchy
          format to form the final data directory.  The following hierarchies are defined:
            0 default     no hierarchy levels
            1 %Y/%m/%d    year/month/day
            2 %Y/%m/%d/%H year/month/day/hour
            3 %Y/%W/%u    year/week_of_year/day_of_week
            4 %Y/%W/%u/%H year/week_of_year/day_of_week/hour
            5 %Y/%j       year/day-of-year
            6 %Y/%j/%H    year/day-of-year/hour
            7 %Y-%m-%d    year-month-day
            8 %Y-%m-%d/%H year-month-day/hour
```

Ref : <https://blog.ipswitch.com/es/qu%C3%A9-es-el-monitoreo-de-flujo-de-red-network-flow-monitor>

<https://labs.lacnic.net/monitoreando-ipv4-e-ipv6-con-sflow/>
