---
title: "Taller de uso de Consola Linux"
date: 2019-07-02T13:53:34-03:00
---

<table>
<thead>
<tr>
<th>COMANDOS DE UNIDADES</th>
<th></th>
<th>COMANDOS DE PROCESOS</th>
<th></th>
<th>COMANDOS DE ARCHIVOS Y DIRECTORIOS</th>
<th></th>
</tr>
</thead>
<tbody>
<tr>
<td>Relacionados con las unidades del equipo</td>
<td></td>
<td>Relacionados con los procesos del sistema</td>
<td></td>
<td>Manejo de archivos/directorios</td>
<td></td>
</tr>
<tr>
<td>mount</td>
<td>monto un disco</td>
<td>top</td>
<td>mostrar los procesos que se estan ejecutando</td>
<td>ls</td>
<td>listar archivos</td>
</tr>
<tr>
<td>umount</td>
<td>desmonto un disco</td>
<td>ps</td>
<td>mostrar la lista de procesos</td>
<td>mkdir</td>
<td>crear directorio</td>
</tr>
<tr>
<td>df</td>
<td>muestra el espacio libre de discos</td>
<td>pstree</td>
<td>mostrar los procesos en forma de arbol</td>
<td>rmdir</td>
<td>borrar directorio</td>
</tr>
<tr>
<td>du</td>
<td>muestra el espacio usado por un directorio</td>
<td>pidof</td>
<td>da el ID del proceso que está corriendo</td>
<td>cd</td>
<td>cambiar de directorio</td>
</tr>
<tr>
<td>mkfs</td>
<td>formateo un disco</td>
<td>kill ­-9</td>
<td>matar proceso por ID</td>
<td>pwd</td>
<td>muestra ruta actual</td>
</tr>
<tr>
<td>fsck</td>
<td>check de disco</td>
<td>killall</td>
<td>mata proceso por nombre</td>
<td>cp</td>
<td>copiar archivo</td>
</tr>
<tr>
<td>fdisk<br />
cfdisk</td>
<td>gestion de particiones</td>
<td>xkill</td>
<td>mata proceso de forma grafica</td>
<td>rm</td>
<td>borrar archivos</td>
</tr>
<tr>
<td>lsscsi</td>
<td>Lista dispositivos SCSI</td>
<td>time</td>
<td>mide el tiempo de ejecución de un proceso</td>
<td>mv</td>
<td>mover archivos y directorios</td>
</tr>
<tr>
<td>lsusb</td>
<td>Lista dispositivos USB</td>
<td>fg</td>
<td>trae a primer plano un proceso en segundo plano</td>
<td>find</td>
<td>buscar archivos</td>
</tr>
<tr>
<td>lspci</td>
<td>Lista dispositivos PCI</td>
<td>bg</td>
<td>pone un proceso en segundo plano</td>
<td>file</td>
<td>identificar tipo de archivo</td>
</tr>
<tr>
<td>lscpu</td>
<td>Lista las CPU</td>
<td>nice</td>
<td>ajusta la prioridad de un proceso</td>
<td>stat</td>
<td>mostrar estado de archivo</td>
</tr>
<tr>
<td>lshw</td>
<td>Lista hardware presente</td>
<td>ps ax -o pid,comm,nice</td>
<td>lista proceso y prioridad</td>
<td>ln</td>
<td>crea enlaces</td>
</tr>
<tr>
<td>COMANDOS DE USUARIOS</td>
<td></td>
<td>COMANDOS DE RED</td>
<td></td>
<td>more</td>
<td>ver el contenido de un archivo</td>
</tr>
<tr>
<td>Manejo de usuario/grupo</td>
<td></td>
<td>Relacionados con la Red</td>
<td></td>
<td>less</td>
<td>ver el contenido de archivo</td>
</tr>
<tr>
<td>adduser</td>
<td>agregar nuevo usuario al grupo</td>
<td>netstat</td>
<td>muestra estado de la red</td>
<td>cat</td>
<td>ver el contenido de un archivos</td>
</tr>
<tr>
<td>userdel</td>
<td>borrar usuario</td>
<td>ifconfig</td>
<td>configuracion de dispositivo de red</td>
<td>split</td>
<td>dividir archivos</td>
</tr>
<tr>
<td>passwd</td>
<td>cambiar contraseña</td>
<td>iwconfig</td>
<td>configuracion de dispositivos wlan</td>
<td>wc</td>
<td>cuenta lineas palabras o caracteres</td>
</tr>
<tr>
<td>su</td>
<td>cambiar de usuario</td>
<td>host</td>
<td>da la IP de un dns</td>
<td>grep</td>
<td>buscar un texto</td>
</tr>
<tr>
<td>whoami</td>
<td>mostrar nombre de usuario</td>
<td>route</td>
<td>muestra la tabla de rutas</td>
<td>join</td>
<td>cruzar la información de dos archivos</td>
</tr>
<tr>
<td>id</td>
<td>mostrar datos de identificacion del usuario</td>
<td>ifup</td>
<td>habilita interfaz de red</td>
<td>diff</td>
<td>mostrar las diferencias entre dos archivos</td>
</tr>
<tr>
<td>finger</td>
<td>mostrar informacion de usuario</td>
<td>ifdown</td>
<td>deshabilita interfaz de red</td>
<td>fuser</td>
<td>mostrar bloqueao de recurso</td>
</tr>
<tr>
<td>last</td>
<td>información de los últimos usuarios que han usado el sistema</td>
<td>dhclient</td>
<td>pedido DHCP</td>
<td>chmod</td>
<td>cambiar permisos</td>
</tr>
<tr>
<td>set</td>
<td>dar información sobre entorno del usuario</td>
<td>whois</td>
<td>muestra información sobre url</td>
<td>chown</td>
<td>cambiar de propietario</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
<td>chgrp</td>
<td>cambiar de grupo</td>
</tr>
</tbody>
</table>

---
| COMANDOS DEL SISTEMA |  | COMANDOS DE IMPRESORAS |  |
|----|----|----|----|
| Relacionados con el sistema |  | Comandos relacionados con Impresoras |  |
| ssh | se conecta a otra maquina | lpq | spool de impresión |
| ftp | conectar por ftp | lpc | estado de impresora |
| reboot | reinicia la maquina | lprm | elimina trabajo impresión |
| init6 | reinicia la maquina | lpr | imprime archivo |
| halt | apaga el sistema | jobs | muestra trabajos en spool |
| shutdown | apaga el sistema | COMBINACION DE TECLAS |  |
| init0 | apaga la maquina | Combinación de teclas útiles |  |
| uptime | muestra tiempo encendida la maquina | ctrl+L | borra pantalla |
| exit | cierro sesion actual | ctrl+alt+F1 | cambio de consola |
| logout | salgo del sistema | ctrl+z | suspendo proceso |
| dmesg | muestra mensajes del arranque del ordenador | ctrl+c | termina proceso en ejecucion |
| history | muestra todos los comandos digitados por el usuario | tab | completa nombre de carpetas o archivos |
| uname | da informacion de SO | ctrl+backspace | cierra X(modo grafico) |
| hostname | muestra el nombre de red | shift+RePag | scroll hacia arriba |
| free | estado de la memoria | shift+AvPag | scroll abajo |
| date | muestra fecha y hora actual | SÍMBOLOS |  |
| cal | muestra calendario | ~ | ruta al home |
| clear | borro la pantalla | . | directorio actual |
| at | ejecuta un comando mas tarde | .. | directorio superior al actual |
| env | ver variables de entorno |  |  |

|

|                     |
|---------------------|
| redirecciona stdout |
| export              |
| startx              |
|                     |
|                     |

## Referencias

- <http://yisux.wordpress.com>

```
TAB: El tabulador permite autocompletar lo que se está escribiendo o navegar entre posibles opciones.
CTRL + C: Sirve para matar un proceso que se esté ejecutando.
CTRL + Z: Sirve para enviar al segundo plano el proceso que se está ejecutando. Si queremos volver al mismo, bastará con usar el comando fg.
CTRL + D: Es el equivalente al comando exit, que sirve para cerrar la sesión de un usuario.
CTRL + L: Este es el equivalente al comando clear y sirve para limpiar la pantalla de terminal. Es bastante útil, sobre todo cuando se ejecuta algún otro software de línea de comandos que no tiene un comando para limpiar pantalla y no se puede insertar el comando clear, como por ejemplo al utilizar el terminal de MySQL.
CTRL + A: Mueve el cursor al inicio. Es útil si estamos escribiendo un comando largo y queremos corregir algo del inicio.
CTRL + E: Mueve el cursor al final. De utilidad si queremos añadir algo al final del comando de forma rápida.
CTRL + U: Con este atajo se borra la línea entera, independientemente de donde esté el cursor.
CTRL + K: De esta forma conseguimos borrar desde la posición del cursor hasta el final de la línea.
CTRL + W: Sirve para borrar la palabra que esté justo antes del cursor, o bien borra todas las letras desde la posición en la que está el mismo hasta el inicio de la palabra anterior.
CTRL + Y: Permite deshacer el último borrado que se ha realizado.
CTRL + R: Permite buscar dentro del historial de comandos que se han introducido. Es útil en caso de no recordar un comando que se ha introducido o uno que sea excesivamente largo, así se puede recuperar sin tener que volver a escribirlo. Cada vez que se pulsa CTRL + R la búsqueda va un paso más atrás.
CTRL + G: Para salir de la búsqueda.
CTRL + SHIFT + C: Sirve para copiar texto seleccionado en la terminal.
CTRL + SHIFT + V: Permite pegar el texto copiado en la terminal.
CTRL + S: De esta forma podemos pausar lo que se está imprimiendo por pantalla. Es útil para poder leer algo del texto que aparece durante una instalación, por ejemplo.
CTRL + Q: Para reanudar la impresión por pantalla que se había pausado.
```
