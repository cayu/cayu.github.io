---
title: "Postfix"
date: 2014-10-28T10:10:07-03:00
---

#### Preferir resolución LOCAL antes que DNS

```
lmtp_host_lookup = native
smtp_host_lookup=native
#disable_dns_lookups = yes
ignore_mx_lookup_error = yes
```

#### Errores comunes

Error :

```
postfix/postdrop[13652]: warning: unable to look up public/pickup: No such file or directory
```

Solución

```
sudo mkfifo /var/spool/postfix/public/pickup
sudo /etc/init.d/postfix restart
```

#### Cambio de dominio

Ejemplo, tenemos X aplicacion empresarial con la libreta de direcciones completa de toda la empresa, supongamos que la empresa se fusiona, cambia etc lo que sea y pasa de tener @razonsocialvieja.com a tener @razonsocialnueva.com.ar, y resulta que por la arquitectura/código de la aplicación es muy engorroso cambiar toda la libreta de direcciones en masa.

Y resulta que tenemos un servidor Postfix que actua de relay para esa aplicacion.

Entonces debemos agregar una expresion regular en Postfix para reemplazar todo lo que venga como \*@razonsocialvieja.com hacia \$1@razonsocialnueva.com.ar

**/etc/postfix/main.cf**

```
virtual_alias_maps = regexp:/etc/postfix/virtualregexp
```

**/etc/postfix/virtualregexp**

```
# razonsocialvieja.com a razonsocialnueva.com.ar
/(.*)@razonsocialvieja.com/ $1@razonsocialnueva.com.ar
```

Luego hay que ejecutar

```
# postmap virtualregexp
```

Y este seria el test

```
# postmap -q sergio.cayuqueo@razonsocialvieja.com regexp:virtualregexp
sergio.cayuqueo@razonsocialnueva.com.ar
```

O sea postfix traduce todo al nuevo dominio.
