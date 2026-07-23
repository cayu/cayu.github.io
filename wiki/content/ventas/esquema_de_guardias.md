---
title: "ESQUEMA"
date: 2015-01-13T12:17:41-03:00
---

## GUARDIA PASIVA

### Objetivo

Que el **administrador de sistemas** que detecta inconvenientes en algún servidor instalado en el Data Center pueda acceder al mismo.

### Método

Se deberá llamar al **teléfono de guardia** con un esquema de horarios como el siguiente:

- Días hábiles de 14:30 a 22:00 horas.
- Sábados de 8:00 a 18:00 horas.
- Domingos y feriados de 10:00 a 18:00 horas.

Se coordinará horario de encuentro en el datacenter, donde el personal de guardia abrirá el edificio para que el administrador tome contacto con el servidor en cuestión.

## GUARDIA ACTIVA

### Objetivo

Detectar y resolver problemas sobre servicios vitales de la red durante los períodos sin actividad laboral.

### Método

Nagios que envien sms al celular de guardia ante un problema.

#### Servicios Vitales

Definir

##### Días y horarios de monitoreo y aviso

7 x 24

##### Cobertura

Se estudia la causa y busca su resolución en los horarios siguientes: día hábil 7 a 22hs, sábados 8 a 18hs, domingos y feriados 10 a 18hs.

#### Servicios Importantes

Definir

##### Días y horarios de monitoreo y aviso

Domingos de 10 a 22hs, resto de los días 8 a 22hs.

##### Cobertura

Se estudia la causa y busca su resolución en los horarios siguientes: día hábil 7 a 22 hs, sábados 8 a 18 hs, domingos y feriados 10 a 18 hs.

**Nota:** Se llevó a cabo una prueba con nagios que arrojó un tiempo mínimo de aviso de 9 a 10 minutos y un máximo de 19 a 20 minutos desde que se cae el host.
