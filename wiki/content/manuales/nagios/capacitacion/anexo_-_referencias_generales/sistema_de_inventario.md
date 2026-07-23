---
title: "Sistema de inventario"
date: 2017-08-24T09:37:29-03:00
---

Si por error no se pueden asociar elementos en el plugin de Servicios ITIL de GLPI, hay que corregir la PRIMARY KEY de esta tabla :

```sql>
 desc glpi_plugin_services_services_items ;
+-----------------------------+--------------+------+-----+---------+----------------+
| Field                       | Type         | Null | Key | Default | Extra          |
+-----------------------------+--------------+------+-----+---------+----------------+
| id                          | int(11)      | NO   | PRI | NULL    | auto_increment |
| plugin_services_services_id | int(11)      | NO   | MUL | 0       |                |
| items_id                    | int(11)      | NO   | MUL | 0       |                |
| itemtype                    | varchar(100) | NO   | MUL | NULL    |                |
+-----------------------------+--------------+------+-----+---------+----------------+
4 rows in set (0.00 sec)
```
