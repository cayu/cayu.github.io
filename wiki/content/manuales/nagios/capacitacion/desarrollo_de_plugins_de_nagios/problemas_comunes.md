---
title: "Problemas comunes de los plugins de Nagios"
date: 2019-08-26T16:14:48-03:00
---

## Reconocimiento del entorno

A veces necesitamos saber que variables de entorno muestra Nagios a nuestros scripts y lo podemos hacer de la siguiente forma y luego observar el retorno en los resultados de ejecución del servicio :

```perl
#!/usr/bin/perl -w
my $o_hoststate        = $ENV{NAGIOS_HOSTSTATE};
# Nagios monitored host check output data
my $o_hostoutput       = $ENV{NAGIOS_HOSTOUTPUT};
# Nagios date when the event was recorded
my $o_datetime         = $ENV{NAGIOS_LONGDATETIME};
# The recipients defined in $CONTACTEMAIL$
my $o_to_recipients    = $ENV{NAGIOS_CONTACTEMAIL};
use Data::Dumper::Concise;
print "<pre>";
print Dumper(%ENV);
print "</pre>";
```

## Códigos de error

| 13 | Problemas de permisos, no se puede ejecutar el plugin o si dicho plugin tiene que leer algun archivo, dicho archivo no es accesible por falta de permisos. |
|----|----|
