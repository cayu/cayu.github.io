---
title: "CRON"
date: 2014-01-02T17:23:54-03:00
---

## CRONTAB

Cambiar editor Vim por defecto para poder usarlo con el comando crontab -e

```
#echo "export EDITOR=vim" >> /root/.bashrc
```

**COMANDOS**

```
#crontab -e (abrirá el editor preestablecido donde se podrá crear o editar el archivo crontab)   
#crontab -l (lista el crontab actual del usuario, sus tareas de cron)
#crontab -r (elimina el crontab actual del usuario)
```

**Como se componen las fechas**

```
##########################################################################################
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7)  OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  *  command to be executed
###########################################################################################
```

**Ejemplos:**

```
59 11 * 1-3 1,2,3,4,5	A las 11:59 a.m. de lunes a viernes, de enero a marzo

45 * 10-25 * 6-7	Al minuto 45 de todas las horas de los días 10 al 25 de todos los meses y que el día sea sábado o domingo

10,30,50 * * * 1,3,5	En el minuto 10, 30 y 50 de todas las horas de los días lunes, miércoles y viernes

*/15 10-14 * * *	Cada quince minutos de las 10:00a.m. a las 2:00p.m.

* 12 1-10/2 2,8 *	Todos los minutos de las 12 del día, en los días 1,3,5,7 y 9 de febrero a agosto. (El incremento en el tercer campo es de 2 y comienza a partir del 1)

0 */5 1-10,15,20-23 * 3	Cada 5 horas de los días 1 al 10, el día 15 y del día 20 al 23 de cada mes y que el día sea miércoles

3/3 2/4 2 2 2	Cada 3 minutos empezando por el minuto 3 (3,6,9, etc.) de las horas 2,6,10, etc (cada 4 horas empezando en la hora 2) del día 2 de febrero y que sea martes
```
