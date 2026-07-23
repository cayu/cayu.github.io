---
title: "Servidores VPS"
date: 2011-01-14T10:51:40-03:00
---

Servidor Debian base instalado

### Servicio Web

```
apt-get install apache2 php5 php5-mysql mysql-server mysql-client
```

/etc/apache2/apache2.conf

```
ExtendedStatus On
```

/etc/apache2/sites-enabled/sitio

```
<VirtualHost *:80>
	ServerAdmin webmaster@localhost
	ServerName  misitio.com.ar
	ServerAlias www.misitio.com.ar
	DocumentRoot /var/www/misitio
	AccessFileName .htaccess
        CustomLog /var/log/apache2/misitio-access.log combined
        ErrorLog  /var/log/apache2/misitio-error.log
        LogLevel warn
        ServerSignature Off
	<Directory />
	    <IfModule mod_rewrite.c>
	    RewriteEngine On
	    RewriteBase /
	    RewriteCond %{REQUEST_FILENAME} !-f
	    RewriteCond %{REQUEST_FILENAME} !-d
	    RewriteRule . /index.php [L]
	    </IfModule>
	    SetEnvIfNoCase User-Agent ^$ bad_bot
	    SetEnvIf User-Agent ^MaMa$ bad_bot
	    SetEnvIfNoCase Referer (totalh) bad_bot
	    SetEnvIfNoCase Referer (unic77) bad_bot
	    SetEnvIfNoCase Referer (http://unic77.blogspot.com/2010/08/parah-gan-pendeta-tertangkap-punya-240.html) bad_bot
	    SetEnvIfNoCase Referer (http://unic77.blogspot.com) bad_bot
	    SetEnvIfNoCase Referer (sitemap.html) bad_bot
	    SetEnvIfNoCase Referer (poker) bad_bot
            SetEnvIfNoCase Referer sitemap.html bad_bot
	    SetEnvIfNoCase Request_URI "contact.php$" bad_bot
	    SetEnvIfNoCase Request_URI "stats.html$" bad_bot
	    SetEnvIfNoCase request_uri ^contact\.php$ bad_bot
	    
	    <Limit GET POST HEAD>
	        Order Allow,Deny
	        Allow from all
	        Deny from env=bad_bot
	    </Limit>

	    Options FollowSymLinks Indexes MultiViews
	    AllowOverride None
	        Deny from env=bad_bot
	</Directory>
        <Directory /var/www/misitio/files>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride None
                Order allow,deny
                allow from all
        </Directory>
</VirtualHost>
```

/etc/apache2/mods-available/status.conf

```
<IfModule mod_status.c>
# http://misitio.com.ar/server-status
<Location /server-status>
    AuthType Basic
    AuthName "Status Access"
    SetHandler server-status
    Order allow,deny
    Allow from all
    AuthUserFile  /etc/apache2/status.htpasswd
    require valid-user
</Location>
</IfModule>
```

### Servicio SSH

```
Port 2222
#ListenAddress 0.0.0.0
Protocol 2
LoginGraceTime 20
PermitRootLogin no
PermitEmptyPasswords no
MaxAuthTries 2
MaxStartups 2
AllowUsers administrador
```

### Servicio de Backup

- Funciona sobre Rsync
- Centralizado, todas las Imágenes se almacenan en un servidor central
- Actualiza los cambios realizados dentro de la Imagen no la Imagen completa, evitando así comprometer el ancho de banda de la red
- Automatizado, las actualizaciones de los datos de los servidores es automática y su intervención manual para su posterior recuperación es miníma
- System Imager <http://wiki.systemimager.org>

### Servidor MySQL

```
[mysqld]
bind-address		= 127.0.0.1
key_buffer		= 28M
max_allowed_packet	= 1M
thread_stack		= 128K
thread_cache_size	= 8
max_connections         = 40
table_cache             = 192
interactive_timeout     = 8
wait_timeout	        = 8
thread_concurrency      = 2
query_cache_limit       = 30M
query_cache_size        = 30M
tmp_table_size 		= 48M
max_heap_table_size 	= 48M
skip-bdb
skip-innodb
```

### Servidor FTP

/etc/proftpd/proftpd.conf

```
<Limit LOGIN>
AllowUser usuario1
AllowUser usuario2
DenyALL
</Limit>
```

### Iptables

Bloquear ip’s a mano

```
iptables -A INPUT -s {ip o subnet}	-j DROP
```

#### Fail2ban

Fail2ban lee los logs (por ejemplo) /var/log/pwdfail o /var/log/apache/error_log y veta todas aquellas ips que fallan un determinado número de veces. Este veto se realiza actualizando el firewall (tipicamente iptables).

```
apt-get install fail2ban
```

**/etc/fail2ban/jail.local**

```
[DEFAULT]
ignoreip = 127.0.0.1 192.168.0.99
bantime  = 600
maxretry = 3
destemail = root@localhost
# Default action to take: ban only
action = iptables[name=%(__name__)s, port=%(port)s]

[ssh]
enabled = true
port    = ssh
filter  = sshd
logpath  = /var/log/auth.log
maxretry = 5

[apache]
enabled = true
port    = http
filter  = apache-auth
logpath = /var/log/apache*/*error.log
maxretry = 5

[apache-noscript]
enabled = false
port    = http
filter  = apache-noscript
logpath = /var/log/apache*/*error.log
maxretry = 5

[vsftpd]
enabled  = false
port     = ftp
filter   = vsftpd
logpath  = /var/log/auth.log
maxretry = 5

[proftpd]
enabled  = true
port     = ftp
filter   = proftpd
logpath  = /var/log/auth.log
failregex = proftpd: \(pam_unix\) authentication failure; .* rhost=<HOST>
maxretry = 5

[postfix]
enabled  = false
port     = smtp
filter   = postfix
logpath  = /var/log/mail.log
maxretry = 5

[courierpop3]
enabled  = true
port     = pop3
filter   = courierlogin
failregex = courierpop3login: LOGIN FAILED.*ip=\[.*:<HOST>\]
logpath  = /var/log/mail.log
maxretry = 5

[courierimap]
enabled  = true
port     = imap2
filter   = courierlogin
failregex = imapd: LOGIN FAILED.*ip=\[.*:<HOST>\]
logpath  = /var/log/mail.log
maxretry = 5

[sasl]
enabled  = true
port     = smtp
filter   = sasl
failregex = warning: [-._\w]+\[<HOST>\]: SASL (?:LOGIN|PLAIN|(?:CRAM|DIGEST)-MD5) authentication failed
logpath  = /var/log/mail.log
maxretry = 5
```
