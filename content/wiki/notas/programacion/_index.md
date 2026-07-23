---
title: "Programación"
date: 2014-02-07T15:02:28-03:00
---

## Programación

## SCRIPTS

[Introducción a AWK](/notas/programacion/Introducción a AWK)

## IOCTLs

[Console IOCTLs Under Linux](/notas/programacion/Console IOCTLs Under Linux)

[Reproductor de CD](/notas/programacion/Reproductor de CD)

[The Linux CDROM interface](/notas/programacion/The Linux CDROM interface)

[Sockets en Linux](/notas/programacion/Sockets en Linux)

## Notas generales de programación

#### Actualizacion a PHP 5.3.2

**split()** reemplazarla por **explode()**

**How to fix ‘Function eregi() is deprecated’ in PHP 5.3.0**

I used to use eregi for validating email address input that matches to the regular expression.

```php>

```

That would return true if given email address is matches to username@domain.ext pattern. Unfortunately, after upgrading PHP to the earlier version (5.3.0), it wont work properly. This is because eregi is one of several functions that are deprecated in the new version of PHP.

Solution: Use preg_match with the ‘i’ modifier instead. i means that regular expression is case insensitive. So the code become like this:

```php>

```

<http://takien.com/513/how-to-fix-function-eregi-is-deprecated-in-php-5-3-0.php>

#### Obtener el usuario segun su UID

En este caso queremos obtener el usuario que corresponde al UID 506

```c>
#include <pwd.h>
#include <sys/types.h>

int main()
{
  struct passwd *pw;

  pw = getpwuid(506);

  if (pw == NULL) {
    printf("NULL!\n");
  }
  else {
    printf("name: %s\n", pw->pw_name);
  }
}
```

#### Parsear el contenido de un tag XML con sed

A veces necesitamos extraer el contenido de un tag XML dentro de un script, aca un ejemplo simple

```xml>
running</state>
```

```bash>
]*[>]//g'
```

Salida:

```
	running
```

#### Agregar comillas a direcciones IP en archivo CSV

Por ejemplos nos dieron un archivo CSV con todos los campos delimitados por comas, menos las direcciones IP

```
"10.1.1.5","255.255.255.0","Equipo de Red","Red de casa","","DHCP","","","","",""
```

```bash>
=== Agregar texto al final de una linea que matchea con cierto patrón ===

Ejemplo en la linea que se refiere al kernel en el archivo menu.lst quiero cambiar la opción de memoria para el dominio 0.

Entonces necesito cambiar de algo asi:

<code>
kernel /xen.gz-2.6.18-164.10.1.el5
```

A algo asi:

```
kernel /xen.gz-2.6.18-194.8.1.el5 dom0_mem=96
```

Ejecuto esto:

```bash>
=== Redondear un numero en BASH ===

<code bash>
echo "tmp=$variable; tmp /= 1;tmp" | bc
```

#### Pasar mayúculas y minúsculas

```
echo hola | tr '[:lower:]' '[:upper:]'
echo HOLA | tr '[:upper:]' '[:lower:]'
```

#### Ejecutar asyncronicamente en Perl o como emular un nohup desde adentro

```perl>
/dev/null");
open (STDERR,">&STDOUT");
exec "comando a ejecutar";
```

#### can you get cp to give a progress bar like wget

<http://chris-lamb.co.uk/2008/01/24/can-you-get-cp-to-give-a-progress-bar-like-wget/>

```bash>
&1 \
      | awk '{
        count += $NF
            if (count % 10 == 0) {
               percent = count / total_size * 100
               printf "%3d%% [", percent
               for (i=0;i<=percent;i++)
                  printf "="
               printf ">"
               for (i=percent;i<100;i++)
                  printf " "
               printf "]\r"
            }
         }
         END { print "" }' total_size=$(stat -c '%s' "${1}") count=0
}
```

#### Sockets en PHP

**servidor-tcp.php**

```php>
\n";
} else {
  while ($con = stream_socket_accept($socket)) {
    fwrite($con, 'La hora local es ' . date('n/j/Y g:i a') . "\n");
    fclose($con);
  }
  fclose($socket);
}
?>
```

**servidor-udp.php**

```php>

```

**ascii.c**

```c>
int main()
{
   int n;
   for (n=16; n<256;n+=4)
      printf ("%d.- %c  %d.- %c  %d.- %c  %d.- %c  %d.- %c  %d.- %c  %d.- %c\n", 
      n, n, n+1, n+1, n+2, n+2, n+3, n+3, n+4, n+4, n+5, n+5, n+6, n+6);
   return 1;
}
```

## Problema clásicos de programación

#### Peluquero dormilón

```c>
#include <unistd.h>
#include <pthread.h>
 
#define TRUE 1
#define FALSE 0
#define CHAIRS 3 //Sillas Disponibles
#define CANT_CUST 5 //Cantidad de Clientes
#define T_CUST 0 //Tiempo de llegada de Clientes
#define T_CORTE 3 //Tiempo de corte de pelo
 
typedef int semaphore;
 
//Prototipo de funciones
void *customer (void *);
void *barber (void *);
void up (semaphore *);
void down (semaphore *);
 
semaphore sem_barber=0, sem_customer=0,sem_mutex=1;
int waiting=0;
 
//Main function
int main (void) {
 pthread_t barb_t,cust_t[CANT_CUST];
 int i;
 
 pthread_create(&barb_t,NULL,barber,NULL);
 for (i=0;i<CANT_CUST;i++){
    sleep(T_CUST);
    pthread_create(&cust_t[i],NULL,customer,NULL);
 }
    
 pthread_join(barb_t,NULL);
 return(0);
}
 
void *customer (void *n) {
 printf ("Customer:entrando hay %d esperando\n",waiting);
 down (&sem_mutex);
 if (waiting < CHAIRS) {
  waiting++;
  up (&sem_customer);
  up (&sem_mutex);
  down (&sem_barber);
  printf ("Customer:Me estan cortando el pelo.\n");
 }
 else {
  up (&sem_mutex);
  printf ("Customer:Me fui no hay lugar.\n");
 } 
}
 
void *barber (void *j) {
 printf ("Barber:Empiezo a trabajar\n"); 
 while (TRUE) {
  down (&sem_customer);
  down (&sem_mutex);
  waiting--;
  up (&sem_barber);
  up (&sem_mutex);
  printf ("Barber:Comienzo el corte de pelo de un cliente quedan %d esperando.\n",waiting);
  sleep (T_CORTE);
  printf ("Barber:Termine de cortar el pelo de un cliente quedan %d esperando.\n",waiting);
 }
}
 
void up (semaphore *sem) {
 *sem+=1;
}
 
void down (semaphore *sem) {
  while (*sem<=0){};
  *sem-=1;
}
```

#### Uso de la Función div()

**Sintaxis:**

div_t div(int num, int denom);

**Descripcion:** Calcula el cociente y resto de la división del numerador num entre el denominador denom. Si la división es inexacta, el cociente resultante es el entero de menor magnitud que es el más próximo al cociente algebraico. Si el resultado no puede ser representado, el comportamiento no está definido; de lo contrario, quot \* denom + rem igualará num.

```c>
#include <stdio.h>
 
int main()
{
   div_t d;
   int num, denom;
 
   puts( "Escriba el numerador y el denominador (separados por un espacio):" );
   scanf( "%d %d", &num, &denom );
   d = div( num, denom );
   printf( "ldiv( %d, %d ) : cociente = %d, resto = %d\n", num, denom, d.quot, d.rem );
 
   return 0;
}
```
