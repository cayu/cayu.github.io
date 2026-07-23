---
title: "OpenSSL"
date: 2014-10-16T10:38:05-03:00
---

OpenSSL es un proyecto de software desarrollado por los miembros de la comunidad Open Source para libre descarga y está basado en SSLeay, desarrollado por Eric Young y Tim Hudson.

Consiste en un robusto paquete de herramientas de administración y librerías relacionadas con la criptografía, que suministran funciones criptográficas a otros paquetes como OpenSSH y navegadores web (para acceso seguro a sitios HTTPS).

Estas herramientas ayudan al sistema a implementar el Secure Sockets Layer (SSL), así como otros protocolos relacionados con la seguridad, como el Transport Layer Security (TLS). Este paquete de software es importante para cualquiera que esté planeando usar cierto nivel de seguridad en su máquina con un sistema operativo libre basado en GNU/Linux. OpenSSL también permite crear certificados digitales que pueden aplicarse a un servidor, por ejemplo Apache.

## Operaciones Varias

Ver el emisor:

```
openssl x509 -noout -in cert.pem -issuer
```

Ver el propietario:

```
openssl x509 -noout -in cert.pem -subject
```

Ver período de validez:

```
openssl x509 -noout -in cert.pem -dates
```

Combinar opciones:

```
openssl x509 -noout -in cert.pem -issuer -subject -dates
```

Ver contenido del certificado:

```
openssl x509 -in cert.pem -noout -text
```

Verificar certificado:

```
openssl verify cert.pem
```

## Certificados PFX

```
openssl pkcs12 -in domain.pfx -clcerts -nokeys -out domain.cer
openssl pkcs12 -in domain.pfx -nocerts -nodes  -out domain.key
```

### Apache

```
<VirtualHost 10.1.1.14:443>
 ...
 SSLEngine on
 SSLCertificateFile /path/to/domain.cer
 SSLCertificateKeyFile /path/to/domain.key
 ...
</VirtualHost>
```

## Certificados con password

Si tenemos certificados protegidos por contraseña y no queremos tener que introducir la contraseña cada vez que reiniciamos los servicios, podemos configurarla para obtenerla automáticamente de esta manera

### Apache

**ssl.conf**

```
SSLCertificateFile /etc/httpd/conf.d/certs/certificado.pem
SSLCertificateKeyFile /etc/httpd/conf.d/certs/privada.pem
SSLPassPhraseDialog  exec:/usr/local/sbin/passwd-ssl.sh
```

### Squid

**squid.conf**

```
http_port 80 accel vhost
sslpassword_program /usr/local/sbin/passwd-ssl.sh
https_port 443 cert=/etc/squid/ssl/certificado.pem key=/etc/squid/ssl/privada.pem accel protocol=http vhost
https_port 9443 cert=/etc/squid/ssl/certificado.pem key=/etc/squid/ssl/privada.pem accel protocol=http vhost
```

### /usr/local/sbin/passwd-ssl.sh

```
#!/bin/bash
echo "SfR·eFf$4ffd"
```

Mas info en
