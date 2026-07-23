---
title: "Argentina - ARBA - Código de Operación de Traslado (COT)"
date: 2017-07-24T10:58:29-03:00
---

**ARBA - Agencia de Recaudación de la Provincia de Buenos Aires**

La clave de transporte es un número que sirve para autenticar al sujeto en todas aquellas operaciones vinculadas al sistema para generar el COT. Necesitará de ella para obtener el [COT](http://www.arba.gov.ar/Apartados/Agentes/AgenteCot.asp?Lugar=E)

Al recibir reclamos o mails automáticos reportando errores de COT seguir este procedimiento:

- Ingresar a <http://www.arba.gov.ar/Informacion/Incidentes.asp> para verificar que los problemas no se originen en ARBA, como ejemplo de reportes que ellos colocan.

<!-- -->

- URL WebService COT: <https://cot.arba.gov.ar/TransporteBienes/SeguridadCliente/presentarRemitos.do>

<!-- -->

- Respuesta usual, cuando pedimos el WebService desde el navegador :

```xml>
<TBError>
  <tipoError>DATO</tipoError>
  <codigoError>08</codigoError>
  <mensajeError>Error de parámetro: Parámetro requerido: user / usuario</mensajeError>
</TBError>
```

## Problemas usuales

### Certificado autofirmado de Webservice

En Agosto de 2014 **ARBA Agencia Gubernamental Argentina de provincia de Buenos Aires** modificó un certificado SSL en su servicio web de <u>Remito Electrónico - **Código de Operaciones de Traslado (COT)**</u>\
\
Modificaron su certificado SSL por otro certificado **autofirmado** el cual por lo tanto no es válido y <u>ninguna librería de SSL por lógica - NO LO Válida si no hay una configuración específica</u>\
\
\

<u>ANTERIOR CERTIFICADO</u>

\
**Titular :** CN=\*.arba.gov.ar, OU=Gerencia General de Tecnologias de la Informacion, O=Agencia de Recaudacion de la Provincia de Buenos Aires, L=La Plata, SP=Buenos Aires, C=AR, SN=tXK/g3wFbh8U8p08eboZPWnAq/YW6WuE\
**Autoridad certificante :**\
CN=GeoTrust SSL CA, O=“GeoTrust, Inc.”, C=US\
**Válidez :**\
Válido de 15.08.2011 01:48:59 A 16.08.2014 23:02:18\

<u>CERTIFICADO DEL DIA DE HOY</u>

\
**Titular :**\
EMAIL=seguridadweb@arba.gov.ar, CN=marvin.arba.gov.ar, O=ARBA, L=La Plata, SP=Buenos Aires, C=AR\
O=ARBA - Agencia de Recaudacion Prov. Bs. As., L=La Plata, SP=Buenos Aires, C=AR\
**Autoridad certificante :**\
O=ARBA - Agencia de Recaudacion Prov. Bs. As., L=La Plata, SP=Buenos Aires, C=AR\
**Válidez :**\
Válido de 14.02.2011 00:00:00 A 01.01.2020 23:59:59\
\
EL NUEVO CERTIFICADO SE BAJA DESDE AQUÍ <http://www.arba.gov.ar/Apartados/Agentes/remitoelectr%C3%B3nico.asp>\

- <http://www.arba.gov.ar/bajadas/Fiscalizacion/Operativos/TransporteBienes/Documentacion/CertificadoArba_13102009.rar>

\
\

\
Se analizaron notas de SAP que no tenían explicaciones claras.\
\
Dentro de la documentación de SAP se analizo el siguiente parámetro icm/HTTPS/verify_client que afecta a la CRYPTOLIB de SAP que es una especie de wrapper de SAP para la libreria OpenSSL\
\
Se estableció su valor a 0 dentro del profile de SAP con lo cual es mucho mas permisivo con las válidaciones de los certificados SSL\
\
No es la configuración mas segura, pero no hay otra forma.

```
icm/HTTPS/verif y_client = 0
```

<https://help.sap.com/saphelp_nw70/helpdata/en/0d/88153a1a5b4c2de10000000a114084/content.htm>

#### icm/HTTPS/verify_client

##### Use

This parameter specifies whether or not a client must produce a certificate.

\*\* Properties \*\*

|                        |                                                    |
|------------------------|----------------------------------------------------|
| Work area              | Internet Communication Manager, SAP Web Dispatcher |
| Unit                   | Integer value                                      |
| Standard value         | 1                                                  |
| Dynamically changeable | Local and on all servers                           |

\*\* Value Range and Syntax \*\*

There are three certification levels (0-2):

· **0**: No certification is required and the server does not ask for one.

· **1**: The server asks the client to transfer a certificate. If the client does not send a certificate, authentication is carried out by another method, for example, basic authentication (default setting).

· **2**: The client must transfer a valid certificate to the server, otherwise access is denied

Revisar siempre el <u>archivo de log dev_icm</u>

## Atención telefónica personalizada

*Horario de Atención: Lunes a Viernes de 8.00 Hs. a 19.00 Hs.*

- **0800-321-ARBA (2722)**
- **0221-4294656**
- **0221-4294653**

<http://www.arba.gov.ar/Aplicaciones/OrientacionTelefonica/OrientacionTel.asp>
