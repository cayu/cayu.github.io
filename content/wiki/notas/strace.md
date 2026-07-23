---
title: "Strace"
date: 2020-04-16T23:11:48-03:00
---

**strace** es una utilidad que nos puede ayudar en la comprobación de errores y cuellos de botella en procesos en el sistema operativo GNU/Linux. La misma sirve para monitorear las llamadas al sistema usadas por un determinado programa, todas las señales que este recibe y cuando tiempo toma cada llamada. Esto es posible por una característica del núcleo linux llamada **ptrace**.

Se puede arrancar junto al programa al que se le efectúa el trace, imprime la lista de llamadas al sistema que dicho programa ejecuta. Es útil para averiguar la causa del fallo de un programa determinado porque informa de situaciones en las que por ejemplo, el programa está intentando acceder a un fichero que no existe o que no tiene permiso de lectura o tiempos de espera muy altos al intentar obtener un servicio remoto por red.

## Uso

En simples palabras su ejecución más simple es strace -p *pid*

Si ejecutamos un **strace** al número de proceso del squid obtendríamos algo como esto

```
22118289675}}, {EPOLLOUT, {u32=227, u64=227}}, {EPOLLOUT, {u32=287, u64=287}}}, 256, 10) = 5
gettimeofday({1266513451, 218933}, NULL) = 0
write(299, "HTTP/1.0 200 OK\r\nServer: Apache/"..., 558) = 558
msgsnd(65536, {3, "\316\254H\0\245o\257\1\330\300\325\v\0\20\0\0\5\2\0\0\377\377\377\377\0\0\0\0"}, 28, IPC_NOWAIT) = 0
epoll_ctl(4, EPOLL_CTL_MOD, 299, {EPOLLIN|EPOLLERR|EPOLLHUP, {u32=299, u64=299}}) = 0
accept(94, {sa_family=AF_INET, sin_port=htons(40369), sin_addr=inet_addr("192.168.16.2")}, [16]) = 418
getsockname(418, {sa_family=AF_INET, sin_port=htons(8080), sin_addr=inet_addr("192.168.2.100")}, [16]) = 0
fcntl64(418, F_GETFL)                   = 0x2 (flags O_RDWR)
fcntl64(418, F_SETFD, FD_CLOEXEC|0x2)   = 0
fcntl64(418, F_GETFL)                   = 0x2 (flags O_RDWR)
fcntl64(418, F_SETFL, O_RDWR|O_NONBLOCK) = 0
epoll_ctl(4, EPOLL_CTL_ADD, 418, {EPOLLIN|EPOLLERR|EPOLLHUP, {u32=418, u64=578448656441541026}}) = 0
accept(94, 0xbfe9e758, [16])            = -1 EAGAIN (Resource temporarily unavailable)
read(267, "UBX cayu16@hotmail.com 1 "..., 65535) = 442
epoll_ctl(4, EPOLL_CTL_MOD, 229, {EPOLLIN|EPOLLOUT|EPOLLERR|EPOLLHUP, {u32=229, u64=578448763815723237}}) = 0
write(227, "GIF89a\t\0\27\0\263\0\0\0\0\0\377\377\377\377\377\373\376\346\0\376\352+\376\353/\376"..., 111) = 111
write(7, "1266513451.218     19 10.248.2.1"..., 139) = 139
```

Esto es útil por ejemplo si creemos que los helpers de autenticación no se están ejecutando correctamente

**f** = hace un traceo del proceso y de sus forks

**p** = numero de PID (process ID)

```
strace -fp PID
```

```
strace -fp processID -o outputfile
```

**r** = agregar timestamp a cada línea

```
strace -fp processID -o outputfile -r
```

## Ejemplo de ejecución

Podemos tomar este código : <https://gist.github.com/nolim1t/126991>

```c>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>

#include <netinet/tcp.h>
#include <sys/socket.h>
#include <sys/types.h>
#include <netinet/in.h>
#include <netdb.h>

int socket_connect(char *host, in_port_t port){
	struct hostent *hp;
	struct sockaddr_in addr;
	int on = 1, sock;     

	if((hp = gethostbyname(host)) == NULL){
		herror("gethostbyname");
		exit(1);
	}
	bcopy(hp->h_addr, &addr.sin_addr, hp->h_length);
	addr.sin_port = htons(port);
	addr.sin_family = AF_INET;
	sock = socket(PF_INET, SOCK_STREAM, IPPROTO_TCP);
	setsockopt(sock, IPPROTO_TCP, TCP_NODELAY, (const char *)&on, sizeof(int));

	if(sock == -1){
		perror("setsockopt");
		exit(1);
	}
	
	if(connect(sock, (struct sockaddr *)&addr, sizeof(struct sockaddr_in)) == -1){
		perror("connect");
		exit(1);

	}
	return sock;
}
 
#define BUFFER_SIZE 1024

int main(int argc, char *argv[]){
	int fd;
	char buffer[BUFFER_SIZE];

	if(argc < 3){
		fprintf(stderr, "Usage: %s <hostname> <port>\n", argv[0]);
		exit(1); 
	}
       
	fd = socket_connect(argv[1], atoi(argv[2])); 
	write(fd, "GET /\r\n", strlen("GET /\r\n")); // write(fd, char[]*, len);  
	bzero(buffer, BUFFER_SIZE);
	
	while(read(fd, buffer, BUFFER_SIZE - 1) != 0){
		fprintf(stderr, "%s", buffer);
		bzero(buffer, BUFFER_SIZE);
	}

	shutdown(fd, SHUT_RDWR); 
	close(fd); 

	return 0;
}
```

Para compilarlo procederemos a ejecutar **gcc**:

```
gcc socket.c -o socket
```

Strace nos mostrará muchos datos, como llamadas al sistema para imprimir datos en pantalla, files descriptors, llamadas a memoria etc. Si solo nos interesan las llamadas a funciones de red, podemos agregarle los siguientes argumentos *-e trace=network,read,write* o simplemente *-e trace=network*

```
00    strace -e trace=network,read,write ./socket  cayu.com.ar 80
01    read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\360r\2\0\0\0\0\0"..., 832) = 832
02    read(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784) = 784
03    read(3, "\4\0\0\0\20\0\0\0\5\0\0\0GNU\0\2\0\0\300\4\0\0\0\3\0\0\0\0\0\0\0", 32) = 32
04    read(3, "\4\0\0\0\24\0\0\0\3\0\0\0GNU\0q7?\324>\326\250>\n\253\230<:\227\0362"..., 68) = 68
05    read(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784) = 784
06    read(3, "\4\0\0\0\20\0\0\0\5\0\0\0GNU\0\2\0\0\300\4\0\0\0\3\0\0\0\0\0\0\0", 32) = 32
07    read(3, "\4\0\0\0\24\0\0\0\3\0\0\0GNU\0q7?\324>\326\250>\n\253\230<:\227\0362"..., 68) = 68
08    read(3, "# The \"order\" line is only used "..., 4096) = 92
09    read(3, "", 4096)                       = 0
10    read(3, "# Generated by NetworkManager\nna"..., 4096) = 52
11    read(3, "", 4096)                       = 0
12    socket(AF_UNIX, SOCK_STREAM|SOCK_CLOEXEC|SOCK_NONBLOCK, 0) = 3
13    connect(3, {sa_family=AF_UNIX, sun_path="/var/run/nscd/socket"}, 110) = -1 ENOENT (No existe el archivo o el directorio)
14    socket(AF_UNIX, SOCK_STREAM|SOCK_CLOEXEC|SOCK_NONBLOCK, 0) = 3
15    connect(3, {sa_family=AF_UNIX, sun_path="/var/run/nscd/socket"}, 110) = -1 ENOENT (No existe el archivo o el directorio)
16    read(3, "# /etc/nsswitch.conf\n#\n# Example"..., 4096) = 542
17    read(3, "", 4096)                       = 0
18    read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\3005\0\0\0\0\0\0"..., 832) = 832
19    read(3, "127.0.0.1\tlocalhost\n127.0.1.1\tt4"..., 4096) = 303
20    read(3, "", 4096)                       = 0
21    read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0000\16\0\0\0\0\0\0"..., 832) = 832
22    read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0@G\0\0\0\0\0\0"..., 832) = 832
23    read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0 #\0\0\0\0\0\0"..., 832) = 832
24    socket(AF_INET, SOCK_DGRAM|SOCK_CLOEXEC|SOCK_NONBLOCK, IPPROTO_IP) = 3
25    setsockopt(3, SOL_IP, IP_RECVERR, [1], 4) = 0
26    connect(3, {sa_family=AF_INET, sin_port=htons(53), sin_addr=inet_addr("127.0.0.53")}, 16) = 0
27    sendto(3, "\334f\1\0\0\1\0\0\0\0\0\0\4cayu\3com\2ar\0\0\1\0\1", 29, MSG_NOSIGNAL, NULL, 0) = 29
28    recvfrom(3, "\334f\201\200\0\1\0\1\0\0\0\0\4cayu\3com\2ar\0\0\1\0\1\300\f\0"..., 1024, 0, {sa_family=AF_INET, sin_port=htons(53), sin_addr=inet_addr("127.0.0.53")}, [28->16]) = 45
29    socket(AF_INET, SOCK_STREAM, IPPROTO_TCP) = 3
30    setsockopt(3, SOL_TCP, TCP_NODELAY, [1], 4) = 0
31    connect(3, {sa_family=AF_INET, sin_port=htons(80), sin_addr=inet_addr("69.61.93.142")}, 16) = 0
32    write(3, "GET /\r\n", 7)                = 7
33    read(3, "<html>\r\n<head><title>301 Moved P"..., 1023) = 178
34    write(2, "<html>\r\n<head><title>301 Moved P"..., 178<html>
35    <head><title>301 Moved Permanently</title></head>
36    <body bgcolor="white">
37    <center><h1>301 Moved Permanently</h1></center>
38    <hr><center>nginx</center>
39    </body>
40    </html>
41    ) = 178
42    read(3, "", 1023)                       = 0
43    shutdown(3, SHUT_RDWR)                  = 0
44    +++ exited with 0 +++
45    
```

En la línea 00 podemos ver el comando completo, vemos llamadas al DNS en varios read y un connect en la línea **26** para efectivamente realizar la consulta, la resolución del nombre en la línea **28** y efectivamente el connect de lo que queremos obtener en la línea **31**, el GET que enviamos en la línea **32**, su lectura y su escritura en pantalla.

Si por ejemplo solo queremos ver los system calls *open, close, read, y write* podemos proceder de la siguiente forma :

```
strace -e trace=open,close,read,write  ./socket  cayu.com.ar 80
close(3)                                = 0
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\360r\2\0\0\0\0\0"..., 832) = 832
read(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784) = 784
read(3, "\4\0\0\0\20\0\0\0\5\0\0\0GNU\0\2\0\0\300\4\0\0\0\3\0\0\0\0\0\0\0", 32) = 32
read(3, "\4\0\0\0\24\0\0\0\3\0\0\0GNU\0q7?\324>\326\250>\n\253\230<:\227\0362"..., 68) = 68
read(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784) = 784
read(3, "\4\0\0\0\20\0\0\0\5\0\0\0GNU\0\2\0\0\300\4\0\0\0\3\0\0\0\0\0\0\0", 32) = 32
read(3, "\4\0\0\0\24\0\0\0\3\0\0\0GNU\0q7?\324>\326\250>\n\253\230<:\227\0362"..., 68) = 68
close(3)                                = 0
read(3, "# The \"order\" line is only used "..., 4096) = 92
read(3, "", 4096)                       = 0
close(3)                                = 0
read(3, "# Generated by NetworkManager\nna"..., 4096) = 52
read(3, "", 4096)                       = 0
close(3)                                = 0
close(3)                                = 0
close(3)                                = 0
read(3, "# /etc/nsswitch.conf\n#\n# Example"..., 4096) = 542
read(3, "", 4096)                       = 0
close(3)                                = 0
close(3)                                = 0
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\3005\0\0\0\0\0\0"..., 832) = 832
close(3)                                = 0
read(3, "127.0.0.1\tlocalhost\n127.0.1.1\tt4"..., 4096) = 303
read(3, "", 4096)                       = 0
close(3)                                = 0
close(3)                                = 0
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0000\16\0\0\0\0\0\0"..., 832) = 832
close(3)                                = 0
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0@G\0\0\0\0\0\0"..., 832) = 832
close(3)                                = 0
close(3)                                = 0
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0 #\0\0\0\0\0\0"..., 832) = 832
close(3)                                = 0
close(3)                                = 0
write(3, "GET /\r\n", 7)                = 7
read(3, "<html>\r\n<head><title>301 Moved P"..., 1023) = 178
write(2, "<html>\r\n<head><title>301 Moved P"..., 178<html>
<head><title>301 Moved Permanently</title></head>
<body bgcolor="white">
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
</body>
</html>
) = 178
read(3, "", 1023)                       = 0
close(3)                                = 0
+++ exited with 0 +++
```

Estadísticas de las llamadas :

```
strace -c  ./socket  cayu.com.ar 80
<html>
<head><title>301 Moved Permanently</title></head>
<body bgcolor="white">
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
</body>
</html>
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 24,08    0,000164          82         2           write
 22,76    0,000155           7        21           read
 16,45    0,000112         112         1           shutdown
 12,92    0,000088          22         4         2 connect
  8,37    0,000057           3        17           close
  4,55    0,000031          31         1           sendto
  2,79    0,000019           4         4           socket
  2,35    0,000016           8         2           poll
  2,06    0,000014           3         4           munmap
  1,91    0,000013           6         2           setsockopt
  0,88    0,000006           6         1           ioctl
  0,88    0,000006           6         1           recvfrom
  0,00    0,000000           0         1           stat
  0,00    0,000000           0        13           fstat
  0,00    0,000000           0         8           lseek
  0,00    0,000000           0        26           mmap
  0,00    0,000000           0         9           mprotect
  0,00    0,000000           0         3           brk
  0,00    0,000000           0         1         1 access
  0,00    0,000000           0         1           execve
  0,00    0,000000           0         1           uname
  0,00    0,000000           0         2         1 arch_prctl
  0,00    0,000000           0        13           openat
------ ----------- ----------- --------- --------- ----------------
100.00    0,000681                   138         4 total
```

## Extras y referencias útiles

A veces también puede servirnos **ltrace**, que es una utilidad de depuración, que se usa para mostrar las llamadas que una aplicación de espacio de usuario hace a las bibliotecas compartidas. Pero eso es para otra nota.

- <https://www.linkedin.com/learning/faster-python-services/strace>
- <https://www.amazon.es/Linux-Security-Cookbook-Daniel-Barrett/dp/0596003919>
- <https://www.mcanan.uy/blog/strace/>
- <https://strace.io/>
