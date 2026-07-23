---
title: "Configuración del servidor Web"
date: 2020-08-24T20:50:17-03:00
---

## Apache

```
<VirtualHost *:443>
CustomLog ${APACHE_LOG_DIR}/nagios-access.log combined
ErrorLog ${APACHE_LOG_DIR}/nagios-error.log
SSLEngine on
SSLCertificateFile /etc/apache2/ssl/dominio.cer
SSLCertificateKeyFile /etc/apache2/ssl/dominio.key
ScriptAlias /nagios/cgi-bin "/usr/local/nagios/sbin"

ServerName  nagios
ServerAlias  nagios.cayu.com.ar

AccessFileName .htaccess

ScriptAlias /nagios/cgi-bin "/usr/local/nagios/sbin"
<Directory "/usr/local/nagios/sbin">
    SSLRequireSSL
    Options ExecCGI
    AllowOverride None
    AuthBasicProvider ldap-nagios
    AuthType Basic
    AuthName "Acceso Nagios"
    Require valid-user
    Order allow,deny
    Allow from all
</Directory>

Alias /nagios "/usr/local/nagios/share"
<Directory "/usr/local/nagios/share">
    SSLRequireSSL
    AuthBasicProvider ldap-nagios
    AuthType Basic
    AuthName "Acceso Nagios"
    Require valid-user
    Order allow,deny
    Allow from all
</Directory>

# - Graficas de performance

Alias /pnp4nagios "/usr/local/nagios/pnp4nagios/share"

<Directory "/usr/local/nagios/pnp4nagios/share">
    SSLRequireSSL
    AuthBasicProvider ldap-nagios
    AuthType Basic
    AuthName "Acceso Nagios"
    Require valid-user
    Order allow,deny
    Allow from all
        <IfModule mod_rewrite.c>
                # Turn on URL rewriting
                RewriteEngine On
                Options FollowSymLinks
                # Installation directory
                RewriteBase /pnp4nagios/
                # Protect application and system files from being viewed
                RewriteRule ^(application|modules|system) - [F,L]
                # Allow any files or directories that exist to be displayed directly
                RewriteCond %{REQUEST_FILENAME} !-f
                RewriteCond %{REQUEST_FILENAME} !-d
                # Rewrite all other URLs to index.php/URL
                RewriteRule .* index.php/$0 [PT,L]
        </IfModule>
</Directory>
</VirtualHost>
```

## Lighttpd

```
$HTTP["url"] =~ "nagios" {
    auth.backend ="plain"    # The password is stored as plain text as user:password in...
    auth.backend.plain.userfile = "/etc/nagios/passwd"  # this file
    auth.require = ( "" => (
        "method" => "digest",
        "realm" => "nagios",
        "require" => "user=nagiosadmin"
        )
    )
    setenv.add-environment = ( "REMOTE_USER" => "user" )
}
```

## NGINX

```
  location /nagios {
        	alias /usr/local/nagios/share;

		location ~ \.php$ {
		    fastcgi_index index.php;
		    fastcgi_keep_conn on;
		    include /etc/nginx/fastcgi_params;
		    fastcgi_pass unix:/run/php/php7.4-fpm.sock;
		    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                    fastcgi_buffers 16 16k;
            	    fastcgi_buffer_size 32k;
		}
  }

  location /nagios/cgi-bin/ {
                root /usr/lib/;
                include /etc/nginx/fastcgi_params;
                fastcgi_param  AUTH_USER nagiosadmin;
                fastcgi_param  REMOTE_USER nagiosadmin;
                if ($uri ~ "\.cgi$"){
                        fastcgi_pass unix:/run/fcgiwrap.nagios.socket-1;
                }
        }
```
