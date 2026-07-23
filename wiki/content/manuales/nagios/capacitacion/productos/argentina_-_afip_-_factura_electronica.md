---
title: "Argentina AFIP Factura Electrónica"
date: 2015-11-19T11:47:55-03:00
---

Factura Electrónica, es un documento comercial en formato electrónico que reemplaza al documento físico tradicional en papel.

El C.A.E. es el **C**ódigo de **A**utorización **E**lectrónico, que otorga la AFIP a cada documento para darle validez.

Un documento con C.A.E. indica que fue autorizado por la AFIP.

##### check_xml_url.sh

Esto lo necesite cuando tuve que XML de diferentes Webservices tengan la sintaxis correcta, para asi saber si habia una falla en los mismos.

```bash
#!/bin/bash
wget -q -O - --user-agent="Mozilla/5.0 (Macintosh; Intel Mac OS X 10.8; rv:21.0) Gecko/20100101 Firefox/21.0" $1 | xmlstarlet val - 1>/dev/null 2>/dev/null
EXIT_STATUS=$?

if [[ $EXIT_STATUS -eq "0" ]]; then
    echo "OK - La sintaxtis es correcta : $EXIT_STATUS"
fi

if [[ $EXIT_STATUS -ne "0" ]]; then
    echo "CRITICAL - La sintaxtis no es correcta : $EXIT_STATUS"
fi
```

Ejemplo de configuración :

```
	check_command                  	check_xml_url!https://wsaa.afip.gov.ar/ws/services/LoginCms?wsdl
	check_command                  	check_xml_url!https://serviciosjava.afip.gob.ar/wsmtxca/services/MTXCAService?wsdl
```

##### check_xml_afip.php

En Argentina tenemos un servicio de Factura Electronica, si queremos verificar su status, deberemos consumir este WebService <https://serviciosjava.afip.gob.ar/wsmtxca/services/MTXCAService/dummy> , del cual obtendremos esta respuesta que debemos parsear :

```xml>
<ns1:dummyResponse xmlns:ns1="http://impl.service.wsmtxca.afip.gov.ar/service/">
   <appserver>OK</appserver>
   <authserver>OK</authserver>
   <dbserver>OK</dbserver>
</ns1:dummyResponse>
```

```php
#!/usr/bin/php
<?php
/*
SimpleXMLElement Object
(
    [appserver] => OK
    [authserver] => OK
    [dbserver] => OK
)
*/
$xml_cae_dummy = simplexml_load_file('https://serviciosjava.afip.gob.ar/wsmtxca/services/MTXCAService/dummy');

$appserver_status	= $xml_cae_dummy->appserver;
$authserver_status	= $xml_cae_dummy->authserver;
$dbserver_status	= $xml_cae_dummy->dbserver;

if (($appserver_status == 'OK') && ($authserver_status == 'OK') && ($dbserver_status == 'OK')) {
    print("OK - [appserver] ".$appserver_status." [authserver] ".$authserver_status." [dbserver] ".$dbserver_status."|rc=0\n");
    exit(0);
} else {
    print("CRITICAL - [appserver] ".$appserver_status." [authserver] ".$authserver_status." [dbserver] ".$dbserver_status."|rc=2\n");
    exit(2);
}
?>
```

En Python

```python
#!/usr/bin/python
import errno, sys, urllib2
from xml.etree.ElementTree import XML

response = urllib2.urlopen('https://serviciosjava.afip.gob.ar/wsmtxca/services/MTXCAService/dummy')
xml_afip = XML(response.read())

dummy_afip = {}
contador = 0

for elem in xml_afip:
    dummy_afip[elem.tag]=elem.text
    if elem.text != 'OK':
	    if contador <= 0:
		contador = 1
	    else:
		contador = 1

if contador != 1:
    print "OK - ",dummy_afip,"|rc=0"
    sys.exit(0)
else:
    print "CRITICAL - ",dummy_afip,"|rc=1"
    sys.exit(1)
```

## Chequeos y referencias

CONSULTAS en PRODUCCION: MESA de AYUDA : 0800-333-6372 sri@afip.gov.ar

CONSULTAS en TESTING: (011) 4347-1219/1339/1289 webservices@afip.gov.ar

Al momento de comunicarse con AFIP, hay que ser lo mas exactos posibles ya que es una gran agencia gubernamental y día a días se levantan miles de tickets. Los datos principales a tener en cuenta son :

1.  IP Pública desde la cual realizamos el pedidos de generación
2.  CUIT que emite la factura
3.  Proveedor de internet
4.  Si es posible adjuntar los XMLs de request y response (con todo el envoltorio SOAP y token valido) de una transacción que exhiba el comportamiento reportado

Direcciones URL de consulta :

- <https://serviciosjava.afip.gob.ar/wsmtxca/services/MTXCAService/dummy>

<!-- -->

- <https://serviciosjava.afip.gob.ar/a.html>

<!-- -->

- <https://servicios1.afip.gob.ar/test.asp>
