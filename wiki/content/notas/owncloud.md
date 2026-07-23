---
title: "OwnCloud"
date: 2015-04-30T10:32:13-03:00
---

**OwnCloud** es un producto desarrollado para facilitar el acceso remoto a archivos desde cualquier lugar y desde cualquier dispositivo con conexión a Red. Se trata de una aplicación web open source mediante la cual podremos crear nuestro repositorio remoto privado, donde podremos guardar lo que necesitamos siempre que haya espacio suficiente en nuestro disco duro. De este modo, no será necesario utilizar servicios de terceros y tendremos control absoluto sobre quién accede a “nuestra nube”.

##### Interrogantes a los que responde

- ¿Quieres acceder desde la universidad a un documento guardado en tu PC?
- ¿Quieres dar acceso a compañeros y configurar su rol?

<!-- -->

- Una agradable y fácil interfaz web
- Compartición de archivo a usuarios y no usuarios de OwnCloud
- Visor de archivos PDF
- Visor de archivos ODT
- Calendario/Agenda
- Gestión de contactos
- Acceso a tus archivos mediante WebDAV
- Reproductor de música integrado
- Una galería donde podrás visualizar tus imágenes
- Un sencillo editor de textos
- La seguridad de que tus datos están en tu servidor y no en manos de desconocidos.

## Instalación

Esta guía ha sido probada en Debian Squezee y varias versiones de Ubuntu, como prerrequisitos deberemos tener instalado y funcionando el servidor web Apache y el gestor de bases de datos MySQL.

1.- Instalar dependencias

```
apt-get install php-pear php-xml-parser php5-sqlite php5-json sqlite mp3info curl libcurl3-dev zip
```

2.- Crear la base de datos con MySQL

En terminal usamos el siguiente comando:

```
mysql -u root -p
```

pedirá contraseña

entonces aparecerá la línea de comandos de mysql, donde añadiremos las siguientes instrucciones:

```
mysql> create database nombre_de_nuestra_base_de_datos;

Reponderá: Query OK, 1 row affected (0.00 sec)
```

Cerramos mysql con:

```
mysql> quit
```

3.- Descargar y descomprimir Owncloud

Descargamos el paquete owncloud-x.tar.bz2 y posteriormente lo descomprimimos.

```
tar -xvf owncloud-x.tar.bz2
```

4.- Copiar el directorio owncloud a nuestro servidor Apache como root

```
mv owncloud /var/www
```

5.- Le damos permisos del servidor web al directorio owncloud:

```
chown -R www-data:www-data owncloud
```

6.- Reiniciamos nuestro servidor apache:

```
/etc/init.d/apache2 restart
```

7.- Terminar la instalación

Desde la barra de direcciones de un navegador web tecleamos:

ip.de.tu.server/owncloud (si accedes desde otra computadora de la red)

localhost/owncloud (si accedes desde la computadora donde está instalado owncloud)

Entonces se nos mostrará la interfaz web para terminar con la instalación.

Instalar paquetes de sistema

```
apt-get install apache2 php5 php5-json php-xml php-mbstring php5-zip php5-gd
apt-get install php5-sqlite curl libcurl3 libcurl3-dev php5-curl php-pdo
```

Descomprimir paquete descargado de OwnCloud

```
/var/www/# tar xvfzp owncloud-x.x.x.tar.bz2
```

```
chown -R www-data:www-data owncloud
```

### Configuración de autenticación Active Directory

### LDAP

| Host | 10.1.1.1 | Port | 389 |
|----|----|----|----|
| Name | bind_user@dominio.com | Password | Password123 |
| Base | DC=dominio,DC=com |  |  |
| User Login Filter | (&(sAMAccountName=%uid)(objectClass=person)) |  |  |
| User List Filter | (&(objectclass=person) |  |  |
| Display Name Field | sAMAccountName |  |  |

### Aumentar el limite de peso de archivos a subir

Por defecto, el peso de los archivos a subir es muy pequeño. Podemos corregir esto editando el archivo /etc/php5/apache2/php.ini donde buscaremos las líneas:

“upload_max_filesize”

“post_max_size”

## Apariencia

La elección del tema a se debe establecer en **config/config.php**

```php>
 ‘MyTheme’,
```

Dentro de **/var/www/owncloud/themes/\[nuestro tema\]** vamos a tener todos los archivos y directorios que hacen al tema

| ./core                |     |
|-----------------------|-----|
| ./core/css            |     |
| ./core/css/images     |     |
| ./core/img            |     |
| ./core/img/actions    |     |
| ./core/img/design     |     |
| ./core/img/design/svg |     |
| ./core/img/filetypes  |     |
| ./core/img/places     |     |
| ./core/img/rating     |     |
| ./core/templates      |     |

**/var/www/owncloud/themes/\[nuestro tema\]**

```php
<?php
class OC_Theme {

    private $ThemeName;


    function __construct() {
    	    $this->ThemeName = "Cayu";
    }

    public function getName() {
    	    return $this->ThemeName;
    }

    public function getTitle() {
	    return $this->ThemeName;
    }
    public function getMailHeaderColor() {
	    return "#FFFFFF";
    }
    public function getBaseUrl() {
	    return "https://owncloud.cayu.com.ar";
    }
    public function getSlogan() {
	    return "Cayu Nube privada";
    }
}
?>
```

Si por ejemplo queremos modificar el esquema de la pantalla de login :

**/var/www/owncloud/themes/\[nuestro tema\]/core/templates**

```php
<!DOCTYPE html>
<!--[if lt IE 7]><html class="ng-csp ie ie6 lte9 lte8 lte7"><![endif]-->
<!--[if IE 7]><html class="ng-csp ie ie7 lte9 lte8 lte7"><![endif]-->
<!--[if IE 8]><html class="ng-csp ie ie8 lte9 lte8"><![endif]-->
<!--[if IE 9]><html class="ng-csp ie ie9 lte9"><![endif]-->
<!--[if gt IE 9]><html class="ng-csp ie"><![endif]-->
<!--[if !IE]><!--><html class="ng-csp"><!--<![endif]-->
	<head data-requesttoken="<?php p($_['requesttoken']); ?>">
		<title>
		<?php p($theme->getTitle()); ?>
		</title>
		<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
		<meta http-equiv="X-UA-Compatible" content="IE=9" />
		<meta name="viewport" content="width=device-width, minimum-scale=1.0, maximum-scale=1.0">
		<meta name="apple-itunes-app" content="app-id=543672169">
		<link rel="shortcut icon" href="<?php print_unescaped(image_path('', 'favicon.png')); ?>" />
		<link rel="apple-touch-icon-precomposed" href="<?php print_unescaped(image_path('', 'favicon-touch.png')); ?>" />
		<?php foreach($_['cssfiles'] as $cssfile): ?>
			<link rel="stylesheet" href="<?php print_unescaped($cssfile); ?>" type="text/css" media="screen" />
		<?php endforeach; ?>
		<?php foreach($_['jsfiles'] as $jsfile): ?>
			<script type="text/javascript" src="<?php print_unescaped($jsfile); ?>"></script>
		<?php endforeach; ?>

		<?php foreach($_['headers'] as $header): ?>
			<?php
				print_unescaped('<'.$header['tag'].' ');
				foreach($header['attributes'] as $name=>$value) {
					print_unescaped("$name='$value' ");
				};
				print_unescaped('/>');
			?>
		<?php endforeach; ?>
	</head>
	<body id="body-login">
	    		<img src="<?php print_unescaped(image_path('', 'logo_cayu.png')); ?>" alt="<?php p($theme->getName()); ?>" />
			<?php print_unescaped($_['content']); ?>
			<div class="push"></div><!-- for sticky footer -->
		<footer>
			<p class="info">
				<a href="http://cayu.com.ar" target="_blank">Cayu</a>
			</p>
		</footer>
	</body>
</html>
```

Fragmento ! : **/var/www/owncloud/themes/\[nuestro tema\]/core/css/**

```css
...
#body-login {
	text-align: center;
	background: #FFFFFF;
	margin: 40px;
        background: -moz-linear-gradient(top, #ffffff 0%, #ffffff 0%); /* FF3.6+ */
	background: -webkit-gradient(linear, left top, left bottom, color-stop(0%,#ffffff), color-stop(0%,#ffffff)); /* Chrome,Safari4+ */
        background: -webkit-linear-gradient(top, #ffffff 0%,#ffffff 0%); /* Chrome10+,Safari5.1+ */
        background: -o-linear-gradient(top, #ffffff 0%,#ffffff 0%); /* Opera11.10+ */
        background: -ms-linear-gradient(top, #ffffff 0%,#ffffff 0%); /* IE10+ */
        background: linear-gradient(top, #ffffff 0%,#ffffff 0%); /* W3C */
        filter: progid:DXImageTransform.Microsoft.gradient( startColorstr='#ffffff', endColorstr='#ffffff',GradientType=0 ); /* IE6-9 */
}
#body-login input[type="text"],
#body-login input[type="password"],
#body-login input[type="email"],
#body-login input[type="submit"] {
	border: solid 1px  #949da9;
	border-radius: 5px;
	margin: 2px;
	color: #ddd;
}
#body-login input[type="submit"].primary, input[type="button"].primary, button.primary, .button.primary {
	background-color: #35537a;
	font-size: 10px;
}
#body-login footer .info,#body-login footer .info a:link {
	white-space: nowrap;
	color: #35537a;
}

#body-login input[type=checkbox] {
  margin-left: 10px;
  border-radius: 50%;
  position: relative;
}

#body-login #remember_login:hover+label,
#body-login #remember_login:focus+label  {
        color: #35537a !important;
}

#body-login .wrapper {
	text-align: center;
        min-height: 100%;
        margin: 0 auto -70px;
        width: 300px;
}
#body-login footer, #body-login .push {
        height: 80px;
}
...
```

### Notas

Dentro de **owncloud/core/img** hay un archivo que se llama *logo-mail.gif*, es necesario reemplazarlo por el archivo que sea preciso mostrar dentro del mail de notificación.
