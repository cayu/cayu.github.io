---
title: "The GNU Privacy Guard"
date: 2015-03-05T11:54:12-03:00
---

GPG o GNU Privacy Guard es una herramienta para cifrado y firmas digitales, que viene a ser un reemplazo del PGP (Pretty Good Privacy) pero con la principal diferencia que es software libre licenciado bajo la GPL. GPG utiliza el estándar del IETF denominado OpenPGP.

# Usos de GPG

GPG es estable, calificado como un software para el uso en producción y es comúnmente incluido en los sistemas operativos como FreeBSD, OpenBSD, NetBSD y últimamente con todas las distribuciones GNU/Linux.

Aunque básicamente el programa tiene una interfaz textual, actualmente hay varias aplicaciones gráficas que utilizan recursos de GPG. Por ejemplo, ha sido integrado dentro de Kmail y Evolution, también hay un plugin llamado Enigmail que se integra con Mozilla y Thunderbird que trabajan en Windows, GNU/Linux y otros sistemas operativos. Debido a que los plugins no forman parte del mecanismo de GPG y no están especificados en los estándares OpenPGP, ni sus respectivos desarrolladores están vinculados con los proyectos de plugins, se podría pensar que las ventajas de seguridad de GPG puedan estar comprometidas o incluso perdiendo su efectividad como resultado de esta falta de coordinación y apoyo, pero al ser las herramientas de código abierto o scripts interpretados (como en el caso de los plugins en Thunderbird), se garantiza un funcionamiento fiable con la herramienta GPG.

GPG también puede ser compilado en otras plataformas como Mac OS X y Windows. En Mac OS X hay portada una aplicación libre llamada MacGPG, que ha sido adaptada para usar el ambiente del usuario y sus definiciones de clases nativas. La compilación cruzada no es un ejercicio trivial, por lo menos en parte debido a que las provisiones de seguridad cambian con el sistema operativo y su adaptación a menudo se vuelve difícil, pero los compiladores de alta calidad deben producir ejecutables que interactúen correctamente con otras implementaciones GPG.

# Como trabaja GPG

GPG cifra los mensajes usando pares de claves individuales asimétricas generadas por los usuarios. Las claves públicas pueden ser compartidas con otros usuarios de muchas maneras, un ejemplo de ello es depositándolas en los servidores de claves. Siempre deben ser compartidas cuidadosamente para prevenir falsas identidades por la corrupción de las claves públicas. También es posible añadir una firma digital criptográfica a un mensaje, de esta manera la totalidad del mensaje y el remitente pueden ser verificados en caso de que se desconfíe de una correspondencia en particular.

GPG no usa algoritmos de software que están restringidos por patentes, entre estos se encuentra el algoritmo de cifrado IDEA que está presente en PGP casi desde sus inicios. En su lugar usa una serie de algoritmos no patentados como ElGamal, CAST5, Triple DES (3DES), AES y Blowfish. También es posible usar IDEA en GPG descargando un plugin extra, sin embargo este puede requerir una licencia para usuarios de algunos países en donde esté patentada IDEA.

GPG es un software de cifrado híbrido que usa una combinación convencional de criptografía de claves simétricas para la rapidez y criptografía de claves públicas para el fácil compartimiento de claves seguras, típicamente usando recipientes de claves públicas para cifrar una clave de sesión que es usada una vez. Este modo de operación es parte del estándar OpenPGP y ha sido parte del PGP desde su primera versión.

## Introduccion

La **criptografía asimétrica** es el método criptográfico que usa un par de claves para el envío de mensajes. Las dos claves pertenecen a la misma persona a la que se ha enviado el mensaje. Una clave es pública y se puede entregar a cualquier persona, la otra clave es privada y el propietario debe guardarla de modo que nadie tenga acceso a ella. El remitente usa la clave pública del destinatario para cifrar el mensaje, y una vez cifrado, sólo la clave privada del destinatario podrá descifrar este mensaje.

*GPG o GNU Privacy Guard es una herramienta para cifrado y firmas digitales*

##### Pasos para tener nuestro par de llaves secreta/pública

1\. Instale GnuPG <http://www.gnupg.org>

2\. Ejecute desde su usario normal

```
$ gpg --gen-key
```

Nota: la primera vez que se ejecuta un comando con gpg se crea el directorio /.gnupg y no se hace nada más así que si es su caso tendrá que volver a ejecutar

```
$ gpg --gen-key
```

para confirmar la acción.

3\. en la primera pregunta seleccione la opción 1) (Posibilidad de firmar y encriptar)

- en la segunda pregunta seleccione 1024 bits (robustez de la encriptación)

Nota: cuanto mayor sea el nº de bits, más fuerte será la encriptación pero mayor será el tamaño de la clave pública y los productos derivados (la relación no es lineal)

-  en la tercera pregunta cada uno elegirá lo que le convenga. Si es la primera llave que fabrica, es mejor poner un año de límite, si planea hacerlo en serio, elija 0 (nunca caduca)
-  confirme que todo es correcto
-  Nombre y Apellidos: Sergio Cayuqueo
-  E-mail: cayu@cayu.com.ar
-  Comentario: opcional
-  confirmación
-  la clave es lo más importante. Se recomienda una frase de una seis palabras o más.
-  empieza la construcción de las claves. Se recomienda generar ‘entropía’ mediante el uso del ratón o el teclado. Tras unos segundos se ha generado el par de llaves.

4\. Generación de la llave pública en formato ASCII para su libre distribución:

```
$ gpg -a --export dato > publica.key
```

donde dato es el nombre de pila de usted, un apellido o su dirección de correo electrónico (recuerde lo que escribió mientras fabricaba las claves)

Nota 2: el parámetro -a sirve para que la salida se realice en formato ASCII, si prefiere distribuir su llave pública en binario no añada este parámetro.

5\. Importar una llave pública de otra persona:

Consiga el archivo de la llave pública de esa persona y escriba:

```
$ gpg --import otrapublica.key 
```

**Cifrar y firmar llaves(keys):** Hay 3 posibilidades, cifrar, firmar, cifrar y firmar:

Código:

```
$ gpg --output doc.gpg --encrypt --recipient cayu@cayu.com.ar documento_a_cifrar

$ gpg --output doc.gpg --sign --recipient cayu@cayu.com.ar documento_a_firmar

$ gpg --output doc.gpg --encrypt --sign --recipient cayu@cayu.com.ar documento_a_cifrar_y_firmar
```

Esto creará archivos binarios. Si desea crear archivos ASCII, sólo hay que agregar el parámetro –clearsign al principio del comando.

**Decifrar llaves(keys):**

```
gpg --output document --decrypt documento_cifrado.gpg.
```

```
Generar una llave (key):
Con el comando gpg --gen-key podemos generar nuestra llave primaria, el método es interactivo.
>> En el primer paso nos permite elegir el tipo de llave:
La opción 1 genera dos pares de claves. Un par de claves DSA que es el par de claves primario que se usará sólo para firmar. Un par de claves subordinadas ElGamal que se usará para el cifrado. La opción 2 es parecida a la anterior, pero sólo genera un par de claves DSA. La opción 4 genera un único par de claves ElGamal, que se usará tanto para firmar como para cifrar. En todos los casos existe la posibilidad de añadir subclaves adicionales para cifrar y firmar luego. La más recomendada es la 1
Código:
# gpg --gen-key
gpg (GnuPG) 1.0.7; Copyright (C) 2002 Free Software Foundation, Inc.
This program comes with ABSOLUTELY NO WARRANTY.
This is free software, and you are welcome to redistribute it

under certain conditions. See the file COPYING for details.

Please select what kind of key you want:
  (1) DSA and ElGamal (default)
  (2) DSA (sign only)
  (4) ElGamal (sign and encrypt)
  (5) RSA (sign only)
  Your selection?

>> El segundo paso es para elegir el tamaño de la llave:
Podemos elegir el tamaño de la clave que deseemos, pero mientras más grande => más lento y más segura. Así que un buen tamaño de llave es 2048 que combina seguridad con velocidad
Código:
DSA keypair will have 1024 bits.
About to generate a new ELG-E keypair.
              minimum keysize is  768 bits
              default keysize is 1024 bits
    highest suggested keysize is 2048 bits
    What keysize do you want? (1024) 2048

>> En el tercer paso elegimos la caducidad de la llave:
Mientras menor mejor, pero si el uso es no es crítico, con la opción 0 basta...
Código:
Please specify how long the key should be valid.
          0 = key does not expire
          <n>= key expires in n days
          <n>w = key expires in n weeks
          <n>m = key expires in n months
          <n>y = key expires in n years
          Key is valid for? (0) 0
Key does not expire at all

>> Paso cuatro, información de usuario:
Aca vas a tener que ingresar un par de datos para la conformación de la llave.
Mientas más larga y con más combinaciones de caracteres sea la passphrase mejor (usar numeros, letras, y caracteres especiales)
Código:
Is this correct (y/n)? y

You need a User-ID to identify your key; the software constructs the user id
from Real Name, Comment and Email Address in this form:
"Heinrich Heine (Der Dichter) <heinrichh@duesseldorf.de>"

Real name: Pepe Ruiz
Email address: pepe@ruiz.com
Comment: Un comentario extra
You selected this USER-ID:
"Sergio Cayuqueo (Un comentario extra) <cayu@cayu.com.ar>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
You need a Passphrase to protect your secret key.

Enter passphrase:


Listar llaves (keys):
Con el comando gpg --list-keys podemos ver la lista de las llaves actuales en el sistema.
Código:
# gpg --list-keys
/home/pepe/.gnupg/pubring.gpg
---------------------------------
pub  1024D/75447B14 2006-05-29  Sergio Cayuqueo (Un comentario extra) <cayu@cayu.com.ar>
sub  2048g/96D6CDAD 2006-05-29
```

Firmar clave publica importada

```
desadm@lnlxdyt:~> gpg --sign-key  receptor@demimensaje.com.ar                                           
pub  1024D/9A7DCB72  created: 2002-11-26 expires: never      trust: -/-
sub  2048g/B03DD917  created: 2002-11-26 expires: never
(1). BBVAGIRE <receptor@demimensaje.com.ar>


pub  1024D/9A7DCB72  created: 2002-11-26 expires: never      trust: -/-
 Primary key fingerprint: 3E4D 513C E97B 7BA8 C162  69D9 9BF5 9CBC 9A7D CB72

     BBVAGIRE <receptor@demimensaje.com.ar>

How carefully have you verified the key you are about to sign actually belongs
to the person named above?  If you don't know what to answer, enter "0".

   (0) I will not answer. (default)
   (1) I have not checked at all.
   (2) I have done casual checking.
   (3) I have done very careful checking.

Your selection? (enter '?' for more information): 3
Are you really sure that you want to sign this key
with your key: "Sergio Cayuqueo <receptor@demimensaje.com.ar>" (1E3957A7)

I have checked this key very carefully.

Really sign? y

You need a passphrase to unlock the secret key for
user: "Sergio Cayuqueo <receptor@demimensaje.com.ar>"
1024-bit DSA key, ID 1E3957A7, created 2008-07-22

desadm@lnlxdyt:~>
```

Y luego de que esa llave publica esta en nuestro anillo de confianza podemos ejecutar

```bash>
===== Scripts =====

Ejemplo se me dio la situación de que tuve que armar un script que corra automaticamente desde una aplicacion SAP que encripte archivos y los desencripte, ademas el password para desencriptar esta contenido en un archivo de texto plano, ademas SAP no genera una TTY para poder ejecutar gnupg, encontes hay que agregar el parametro --batch.

<code bash>
pw="/gnupg-script/password"
 
gpg --yes --batch --output "$dst/$f.gpg" --recipient "receptor@demimensaje.com.ar"    --encrypt $src/$f 3< $pw > $HOME/gpg.log  2>&1


gpg --yes --batch  --recipient "cayu@cayu.com.ar"   --passphrase-fd 5  --output $dst/$destfile  --decrypt $gpgdst/$i  5< $pw > $HOME/gpg.log  2>&1
echo $i >> /tmp/gnupg-procesando.tmp
```

## Mas info

<http://es.wikipedia.org/wiki/GNU_Privacy_Guard>

### Infografía de la FSF

<https://emailselfdefense.fsf.org/es/>

<img src="/notas/seguridad/full-infographic.png" class="align-center" alt="full-infographic.png" />
