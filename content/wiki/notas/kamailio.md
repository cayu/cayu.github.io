---
title: "Kamailio"
date: 2010-11-17T11:42:01-03:00
---

## Kamailio

| Kamailio | 1.5.1                                                 |
|----------|-------------------------------------------------------|
| RTPProxy | 1.2.0                                                 |
| CPU      | Genuine Intel(R) CPU 2160 @ 1.80GHz                   |
| MHz      | 1795.654                                              |
| \# CPU   | 2                                                     |
| RAM      | 1538872 kB                                            |
| Swap     | 514072 kB                                             |
| System   | Linux 2.6.18-128.1.10.el5                             |
| OS       | Red Hat Enterprise Linux Server release 5.3 (Tikanga) |

El proxy SIP se encarga de negociar las comunicaciones entre las partes, asegurandose que las mismas se realicen por las direcciones y puertos que correspondan, forzando a que el tráfico pase por el proxy RTP. Además permite autenticar usuarios vía LDAP para controlar el acceso.

El proxy RTP se encarga de hacer el relay del tráfico multimedia entre las partes, para evitar comunicaciones directas entre las mismas.

## Proyectos

Proxy SIP

###### Spec de la instalacion

#### Aplicaciones instaladas

Kamailio 1.5.1 RTPProxy 1.2.0

#### Configuracion

      /usr/local/etc/kamailio/kamailio.cfg

      /usr/local/etc/kamailio/kamctlrc

#### Scripts de inicio

      /etc/init.d/kamailio
      /etc/init.d/rtpproxy

#### Archivo de logs

      /var/log/kamailio.log

##### Comandos desde consola

Ver Estado del Servidor:

      kamctl monitor

#### Usuarios

\* Agregar

      kamctl add <username> <password>

\* Borrar

      kamctl rm <username>

#### Alias

\* Agregar

      kamctl alias_db add <alias> <sip-id>

\* Borrar

      kamctl alias_db rm <alias>

#### Registraciones

\* Listar usuarios registrados

      kamctl ul show --brief

\* Ver locacion de un usuario

      kamctl ul show <username>

##### Red

#### Puertos

### RTPProxy

\* Protocolo: UDP

\* Puertos: 50000 - 50100

\* Marca TOS: 0xb8

### Kamailio

\* Protocolo: UDP

\* Puertos: 5060

### Notas extras

El campo dbaliases.username establecido como un INDEX UNIQUE

agregado el campo nombre a la tabla subscriber nombre varchar(200) y el campo comentario comentario varchar(255)

Sobre los campos ha1 y ha1b que contienen Hashes MD5

generar ha1

```
[root@proxysip admin]# echo -n 'cayu:sip.cayu.com.ar:123456' | md5sum
113399d22a9f2b40b926ccbd1e6726bf  -
```

ha1b

```
[root@proxysip admin]# echo -n 'cayu@sip.cayu.com.ar:sip.cayu.com.ar:123456'|md5sum
302b4aafb02330871b688a2438d0b1ee  -
```

Si sale un error como ip_msg_cloner: cannot allocate memory” o “ERROR: new_t: out of mem:”

modificar el parametro STARTOPTIONS en kamctlrc como STARTOPTIONS=“-m 512”

— *Sergio Cayuqueo 2009/07/10 11:13*
