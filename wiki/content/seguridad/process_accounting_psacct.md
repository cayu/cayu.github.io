---
title: "Process Accounting (PSACCT)"
date: 2012-08-24T15:21:21-03:00
---

El paquete psacct contiene diversas utilidades para visualizar la actividad de los procesos, como ac, lastcomm, accton y sa. El comando ac visualiza las estadísticas de los usuarios conectados. El comando lastcomm visualiza la informacióm sobre comandos precedentemente ejecutados. El comando lastcomm resume la información sobre los comandos precedentemente ejecutados.

- El comando **ac** muestra estadísticas acerca de la cantidad de usuarios y tiempo que se han conectado.
- El comando **lastcomm** muestra información sobre los últimos comandos ejecutados.
- El comando **accton** convierte la contabilidad de procesos encendido o apagado.
- El comando **sa** resume información sobre commmands previamente ejecutadas.

## Instalar las utilidades

Para instalar en RHEL 4.0 :

```
# up2date psacct
```

Para instalar en CentOS/Fedora/RHEL 5 :

```
# yum install psacct
```

Para instalar en Debian :

```
$ sudo apt-get install acct
```

## Iniciar el servicio

En Redhat :

```
# chkconfig psacct on
# /etc/init.d/psacct start
```

En SuSE :

```
# chkconfig acct on
# /etc/init.d/acct start
```

## Consultar los datos

- Tiempo de loggueo **ac**
- Tiempo de loggueo por dia **ac -d**
- Tiempo de loggueo por usuario **ac -p**
