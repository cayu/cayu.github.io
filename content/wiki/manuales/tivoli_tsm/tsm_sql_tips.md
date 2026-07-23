---
title: "Mandatos de prueba de validación de la actualización de la base de datos"
date: 2012-04-10T10:21:37-03:00
---

Ejecute una serie de mandatos antes y después de actualizar un servidor para obtener un resumen de la información relativa al contenido de la base de datos del servidor. La comparación de los resultados de los mandatos antes y después de la actualización puede ayudar a confirmar que todos los datos se han transferido correctamente. Las muestras ofrecen un conjunto de mandatos con el que empezar.

Los siguientes mandatos muestran ejemplos de cómo obtener información resumida acerca de algunos tipos de objeto específicos

- **Espacios de archivos** select node_name, count`(*`) as “Number of Filespaces” from filespaces group by node_name order by 2
- **Nodos** select platform_name, count`(*`) as “Number of Nodes” from nodes group by platform_name
- **Archivos de los que se ha realizado copia de seguridad** select node_name,sum(num_files) as “Number of Backup Files” from occupancy where type=‘Bkup’ group by node_name
- **Archivos archivados** select node_name,sum(num_files) as “Number of Archive Files” from occupancy where type=‘Arch’ group by node_name
- **Clases de gestión** select count`(*`) as “Number of Management Classes” from mgmtclasses
- **Scripts de servidor** select count`(*`) as “Number of Server Scripts” from script_names
- **Agrupaciones de almacenamiento** select count`(*`) as “Number of Storage Pools” from stgpools

El siguiente ejemplo muestra un conjunto de mandatos más completo. Puede ejecutar este conjunto de mandatos como un script desde una línea de mandatos del servidor Tivoli Storage Manager. Redireccione la salida a un archivo para guardar los resultados.

```
select node_name, count(*) as "Number of Filespaces" from filespaces group by node_name order by 2
select platform_name, count(*) as "Number of Nodes" from nodes group by platform_name
select count(*) as "Number of Administrators" from admins
select node_name,sum(num_files) as "Number of Backup Files" from occupancy where type='Bkup'
    group by node_name
select node_name,sum(num_files) as "Number of Archive Files" from occupancy where type=‘Arch'
    group by node_name
select count(*) as "Number of Schedule Associations" from associations
select count(*) as "Number of Backupsets" from backupsets
select count(*) as "Number of Client Option Sets" from cloptsets
select count(*) as "Number of Collocation Groups" from collocgroup
select count(*) as "Number of Archive CopyGroups" from ar_copygroups
select count(*) as "Number of Backup CopyGroups" from bu_copygroups
select count(*) as "Number of Data Movers" from datamovers
select count(*) as "Number of Device Classes" from devclasses
select count(*) as "Number of Domains" from domains
select count(*) as "Number of Drives" from drives
select count(*) as "Number of Libraries" from libraries
select count(*) as "Number of Library Volumes" from libvolumes
select count(*) as "Number of Volumes" from volumes
select count(*) as "Number of Management Classes" from mgmtclasses
select count(*) as "Number of Node Groups" from nodegroup
select count(*) as "Number of Device Paths" from paths
select count(*) as "Number of Policy Sets" from policysets
select count(*) as "Number of Client Schedules" from client_schedules
select count(*) as "Number of Admin Schedules" from admin_schedules
select count(*) as "Number of Server Scripts" from scripts
select count(*) as "Number of Servers Defined" from servers
select count(*) as "Number of Servers Groups Defined" from server_group
select count(*) as "Number of Storage Pools Defined" from stgpools
```
