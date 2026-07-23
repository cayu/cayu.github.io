---
title: "Scanner De Puertos Simple"
date: 2014-02-07T15:04:02-03:00
---

```c>
#include <sys/socket.h>
#include <netinet/in.h>
#include <netdb.h>
#include <stdio.h>
#include <errno.h>
#include <fcntl.h>


main(int argc, char *argv[]) {
   int sock,rval,puerto;
   struct sockaddr_in server;
   struct hostent *hp, *gethostbyname();

   if (argc < 2) {
      printf("Uso: %s nombre_host \n", argv[0]);
      exit (1);
   }
   printf("\nEscaner de puertos (^C para salir)\n");
  
   hp = gethostbyname(argv[1]);
   if (hp == 0) {
      fprintf(stderr, "%s: No se puede resolver el nombre\n",
         argv[1]);
      exit(2);
   }

   for(puerto=0;1;puerto++){

   /* Creamos un socket orientado a conexion */
   sock = socket(PF_INET, SOCK_STREAM, 0);
   if (sock < 0) {
      perror("No se ha podido conseguir un socket");
      exit(1);
   }

   server.sin_family = AF_INET;

   memcpy((char *)&server.sin_addr, (char *)hp->h_addr, 
      hp->h_length);
   server.sin_port = htons(puerto);

   if (connect(sock, (struct sockaddr *)&server, 
   sizeof server)>=0)printf("Puerto abierto =>%d\n",puerto);

   close(sock);     

   }               

   exit(0);
}
```
