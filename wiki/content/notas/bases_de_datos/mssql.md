---
title: "Microsoft SQL Server"
date: 2017-04-24T11:18:19-03:00
---

## Administración

## Extras

### Scripts

Ejemplo de volcado CSV de una Tabla

```powershell
sqlcmd -S 192.168.1.56 -d BaseDeDatos -E -W -w 999 -s "," -Q "select * from BaseDeDatos.dbo.TABLA_A_VOLCAR" 
```
