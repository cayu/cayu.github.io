---
title: "Actualizar SuSE Linux"
date: 2016-02-11T11:09:10-03:00
---

## Instalación de SuSE 9 en VMware

Si tenemos un mensaje como este : *Your computer doesn’t fulfill all requirements for a graphical installation*

Tenemos que agregar la opcion *x11i=fbdev* al booteo.

## Actualizar a SUSE 11 SP1

Para actualizar un servidor SUSE SLES, por ejemplo de “SUSE Linux Enterprise Server 10 SP3” a “SUSE Linux Enterprise Server 11 SP1”, podemos utilizar el sistema automático de actualización de SUSE.

Para ellos debemos reiniciar el servidor y luego iniciarlo con el CD/DVD booteable de SuSE, el proceso total dependiendo del número de paquetes a actualizar, demora como mínimo 40 minutos.

![suseupdate11-1.png](/notas/linux/upgrade_suse/suseupdate11-1.png) ![suseupdate11-2.png](/notas/linux/upgrade_suse/suseupdate11-2.png) ![suseupdate11-3.png](/notas/linux/upgrade_suse/suseupdate11-3.png) ![suseupdate11-4.png](/notas/linux/upgrade_suse/suseupdate11-4.png) ![suseupdate11-5.png](/notas/linux/upgrade_suse/suseupdate11-5.png) ![suseupdate11-6.png](/notas/linux/upgrade_suse/suseupdate11-6.png)

## Actualizar a SUSE 11 SP2

Para actualizar un servidor SUSE SLES, por ejemplo de “SUSE Linux Enterprise Server 11 SP1” a “SUSE Linux Enterprise Server 11 SP2”, podemos utilizar el sistema automático de actualización de SUSE.

Para ellos debemos reiniciar el servidor y luego iniciarlo con el CD/DVD booteable de SuSE, el proceso total dependiendo del número de paquetes a actualizar, demora como mínimo 40 minutos.

![suseupdate11-1.png](/notas/linux/upgrade_suse/suseupdate11-1.png) ![suseupdate11-2.png](/notas/linux/upgrade_suse/suseupdate11-2.png) ![suseupdate11-sp2-3.png](/notas/linux/upgrade_suse/suseupdate11-sp2-3.png) ![suseupdate11-sp2-4.png](/notas/linux/upgrade_suse/suseupdate11-sp2-4.png) ![suseupdate11-sp2-5.png](/notas/linux/upgrade_suse/suseupdate11-sp2-5.png) ![suseupdate11-sp2-6.png](/notas/linux/upgrade_suse/suseupdate11-sp2-6.png) ![suseupdate11-sp2-7.png](/notas/linux/upgrade_suse/suseupdate11-sp2-7.png)

## Actualizar a SUSE 11 SP3

Es necesario tener actualizado el sistema a la versión SLES 11 SP3, para poder actualizar directamente a SuSE 12,
{{% hint info %}}
*Upgrading from SUSE Linux Enterprise 11 SP3 to SUSE Linux Enterprise 12, SUSE Linux Enterprise 11 SP3 to SUSE Linux Enterprise 12 SP1, or SUSE Linux Enterprise 11 SP4 to SUSE Linux Enterprise 12 SP1 is supported using one of the following methods:*

- <https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_update_sle12.html>
- <https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_update_sle12_manual.html>
{{% /hint %}}
El proceso completo tarda alrededor de una hora, dependiendo de las configuraciones a tomar.

![suseupdate11-sp2-1.png](/notas/linux/upgrade_suse/suseupdate11-sp2-1.png) ![suseupdate11-sp2-2.png](/notas/linux/upgrade_suse/suseupdate11-sp2-2.png) ![suseupdate11-sp2-3.png](/notas/linux/upgrade_suse/suseupdate11-sp2-3.png) ![suseupdate11-sp2-4.png](/notas/linux/upgrade_suse/suseupdate11-sp2-4.png) ![suseupdate11-sp2-5.png](/notas/linux/upgrade_suse/suseupdate11-sp2-5.png) ![suseupdate11-sp2-6.png](/notas/linux/upgrade_suse/suseupdate11-sp2-6.png) ![suseupdate11-sp2-7.png](/notas/linux/upgrade_suse/suseupdate11-sp2-7.png) ![suseupdate11-sp2-8.png](/notas/linux/upgrade_suse/suseupdate11-sp2-8.png) ![suseupdate11-sp2-9.png](/notas/linux/upgrade_suse/suseupdate11-sp2-9.png) ![suseupdate11-sp2-10.png](/notas/linux/upgrade_suse/suseupdate11-sp2-10.png) ![suseupdate11-sp2-11.png](/notas/linux/upgrade_suse/suseupdate11-sp2-11.png) ![suseupdate11-sp2-12.png](/notas/linux/upgrade_suse/suseupdate11-sp2-12.png) ![suseupdate11-sp2-13.png](/notas/linux/upgrade_suse/suseupdate11-sp2-13.png) ![suseupdate11-sp2-14.png](/notas/linux/upgrade_suse/suseupdate11-sp2-14.png) ![suseupdate11-sp2-15.png](/notas/linux/upgrade_suse/suseupdate11-sp2-15.png) ![suseupdate11-sp2-16.png](/notas/linux/upgrade_suse/suseupdate11-sp2-16.png) ![suseupdate11-sp2-17.png](/notas/linux/upgrade_suse/suseupdate11-sp2-17.png) ![suseupdate11-sp2-18.png](/notas/linux/upgrade_suse/suseupdate11-sp2-18.png) ![suseupdate11-sp2-19.png](/notas/linux/upgrade_suse/suseupdate11-sp2-19.png) ![suseupdate11-sp2-20.png](/notas/linux/upgrade_suse/suseupdate11-sp2-20.png) ![suseupdate11-sp2-21.png](/notas/linux/upgrade_suse/suseupdate11-sp2-21.png) ![suseupdate11-sp2-22.png](/notas/linux/upgrade_suse/suseupdate11-sp2-22.png)

## Actualizar a SUSE 12 SP1

### Comentarios de cambios generales en SUSE 12 SP1

- Es una buena practicar generar archivos personalizados es **/etc/ld.so.conf.d/\*.conf**, si en vez de eso generamos entradas en **/etc/ld.so.conf**, deberemos generarlas de nuevo.
- El montaje de sistemas de archivos **nfs** ahora se llama **nfs4**.
- Hay que volver a configurar las ip de las placas de red, esto igualmente es el primer paso de la instalación.
- Revisar el espacio del sistema de archivos **/boot**, ya que en el caso de haber dejado archivos viejos y utilizar el espacio por default automático de SuSE, podemos llegar a tener problemas con el espacio disponible.
- **Oracle 11gR2 (11.2.0.4)**
  - Configurar la variable “CV_ASSUME_DISTID=SUSE11” en database/stage/cvu/cv/admin/cvu_config
- **Servicio SNMP**
  - Hay que volver a configurar su ejecución al inicio
  - *chkconfig snmpd on*
- **rpcbind won’t start after upgrade from SLES 11 to SLES 12**
  - <https://www.novell.com/support/kb/doc.php?id=7017144>

![suseupdate12-sp1-1.png](/notas/linux/upgrade_suse/suseupdate12-sp1-1.png) ![suseupdate12-sp1-2.png](/notas/linux/upgrade_suse/suseupdate12-sp1-2.png) ![suseupdate12-sp1-3.png](/notas/linux/upgrade_suse/suseupdate12-sp1-3.png) ![suseupdate12-sp1-4.png](/notas/linux/upgrade_suse/suseupdate12-sp1-4.png) ![suseupdate12-sp1-5.png](/notas/linux/upgrade_suse/suseupdate12-sp1-5.png) ![suseupdate12-sp1-6.png](/notas/linux/upgrade_suse/suseupdate12-sp1-6.png) ![suseupdate12-sp1-7.png](/notas/linux/upgrade_suse/suseupdate12-sp1-7.png)

## Referencias útiles

### Notas SAP

- **171356** - SAP Software on Linux: General information
- **1984787** - SUSE LINUX Enterprise Server 12: Installation notes
