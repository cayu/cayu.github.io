---
title: "Csync para centralizar la configuracion de servicios"
date: 2010-03-08T15:35:04-03:00
---

Csync2 es un software para la sincronización de archivos de configuración <http://oss.linbit.com/csync2/>

Aqui se encuentran los paquetes para Red Hat 5 <http://mirror.datapipe.net/local/redhat-5ES/i386/>

En cada cliente se encuentra el archivo /etc/csync2.cfg que contiene la configuración de la replicación de archivos

La idea es tener uno o varios directorios con los archivos de configuración a sincronizar

## Configuracion

Creamos los certificados que usa para las comunicaciones SSL.

```
openssl genrsa -out /etc/csync2_ssl_key.pem 1024
yes '' | openssl req -new -key /etc/csync2_ssl_key.pem -out /etc/csync2_ssl_cert.csr
openssl x509 -req -days 600 -in /etc/csync2_ssl_cert.csr -signkey /etc/csync2_ssl_key.pem -out /etc/csync2_ssl_cert.pem
```

Creamos la clave compartida que tiene que estar en todos los servers:

```
csync2 -k /etc/csync2.key
```

Configuracion

```
group dhcp {
        host host-master;
        host (host-slave); # (1)

        key /etc/csync2.key;

        include /etc/dhcpd.conf;
        include /etc/dhcp/;

        action { # (2)
                    pattern /etc/dhcpd.conf;
                pattern /etc/dhcp/;
#                exec "/etc/init.d/dhcpd restart";
                logfile "/var/log/csync2.log";
                do-local; # (3)
        }
}
```

1.  **include**: son los elementos a incluir para la sincronizacion
2.  **pattern**: es el elemento que sufrirá modificaciones, las cuales seran tomadas como aviso para que csync2 realize la sincronización.
3.  **host**: Son los equipos que se sincronizan, línea por línea. Los paréntesis significan que ese equipo solo recibe copias, pero no las envía al resto. Esto quiere decir que csync2 se lo puede llegar a correr de cualquier lado y solo tomara las modificaciones de los hosts masters
4.  **action**: Indican que mirar para buscar cambios (pattern) y ejecutar un comando (exec). Opcional, un log de la acción ejecutada.
5.  **do-local**: Esto hace que la action se ejecute en el servidor donde se dispara el csync2. Recomendable.

Los nombres de los hosts tienen que ser resueltos o por DNS o por /etc/hosts
