---
title: "Tivoli Storage Manager - TSM"
date: 2015-10-22T11:53:19-03:00
---

**IBM Tivoli Storage Manager** es una plataforma de protección de datos y administración para copia de seguridad y restauración. Desde Nagios tenemos una gran variedad de scripts para verificar su correcto funcionamiento interno.

Deberemos tener configurado el cliente TSM dentro del mismo servidor donde tenemos instalado Nagios. Y dentro del archivo **dsm.sys** deberemos tener configurado los múltiples servidores para conectarnos. Es recomendable agregarle una variable dentro de Nagios a cada uno de los hosts definimos que funcione como servidor TSM.

```
SErvername  TSMPRINCIPAL1
  COMMmethod         TCPip
  TCPPort            1500
  TCPServeraddress   10.1.1.99
```

```
define host {
	host_name                      	server_tsm
	alias                          	TSM Server
	address                        	10.1.1.99
	use                            	plantilla_servidores
	_TSMSERVER                     	TSMPRINCIPAL1
}
```

Definicion del commands.cfg para los comandos que utilizan dicha variable como argumento :

```bash
$USER1$/tsm/tsm_path.sh 		$_HOSTTSMSERVER$
$USER1$/tsm/tsm_disk_pool.sh 		$_HOSTTSMSERVER$
$USER1$/tsm/tsm_db_backuptime.sh 	$_HOSTTSMSERVER$
$USER1$/tsm/tsm_fulldb_backup.sh 	$_HOSTTSMSERVER$
$USER1$/tsm/tsm_schedule_errors.sh 	$_HOSTTSMSERVER$
$USER1$/tsm/tsm_occupancy.sh 		$_HOSTTSMSERVER$
$USER1$/tsmmonitor.pl $ARG1$ $ARG2$ 	$_HOSTTSMSERVER$
$USER1$/tsm/check_tsm -H $HOSTADDRESS$ -U admin -P admin $ARG1$
```
