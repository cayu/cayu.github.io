---
title: "Tipos de notificaciones"
date: 2019-03-26T17:25:44-03:00
---

### Definiciones previas

- **Indicador**: *Dato o información que sirve para conocer o valorar las características y la intensidad de un hecho o para determinar su evolución futura.*
- **Alerta**: *Señal o aviso con el objetivo de desviar la atención hacia el dato obtenido del indicador.*
- **Notificación**: *Hacer saber una cosa a alguien siguiendo ciertas formalidades.*
- **Criterio**: *Opinión, juicio o decisión que se adopta sobre una cosa.*

## Correo electrónico

Para el envío de notificaciones por parte de Nagios es necesario configurar un MTA (Agente de Transporte de Correos), por lo general suele estar previamente configurado el Sendmail en el caso de que nos basemos en un CentOS, o un Postifx tratándose de un SuSE o Debian.

En caso de que utilizemos el paquete de Postfix disponible en la distribución, y debamos utilizar otro servidor saliente SMTP dentro de la red, deberemos configurar el relayhost apuntando al servidor SMTP de la red local ej 192.168.1.110, quedando el archivo /etc/postfix/main.cf con las siguientes entradas de configuración.

```
queue_directory = /var/spool/postfix
command_directory = /usr/sbin
daemon_directory = /usr/lib/postfix
mail_owner = postfix
myhostname = nagios
mydomain = midominio.net
inet_interfaces = $myhostname
unknown_local_recipient_reject_code = 550
debug_peer_level = 2
debugger_command =
         PATH=/bin:/usr/bin:/usr/local/bin:/usr/X11R6/bin
         xxgdb $daemon_directory/$process_name $process_id & sleep 5
sendmail_path = /usr/sbin/sendmail
newaliases_path = /usr/bin/newaliases
mailq_path = /usr/bin/mailq
setgid_group = maildrop
html_directory = /usr/share/doc/packages/postfix/html
manpage_directory = /usr/share/man
sample_directory = /usr/share/doc/packages/postfix/samples
readme_directory = /usr/share/doc/packages/postfix/README_FILES
mail_spool_directory = /var/mail
canonical_maps = hash:/etc/postfix/canonical
virtual_maps = hash:/etc/postfix/virtual
relocated_maps = hash:/etc/postfix/relocated
transport_maps = hash:/etc/postfix/transport
sender_canonical_maps = hash:/etc/postfix/sender_canonical
masquerade_exceptions = root
masquerade_classes = envelope_sender, header_sender, header_recipient
program_directory = /usr/lib/postfix
inet_interfaces = 192.168.1.4
masquerade_domains = 
mydestination = $myhostname, localhost.$mydomain
defer_transports = 
disable_dns_lookups = no
relayhost = 192.168.1.110
content_filter = 
mailbox_command = 
mailbox_transport = 
smtpd_sender_restrictions = hash:/etc/postfix/access
smtpd_client_restrictions = 
smtpd_helo_required = no
smtpd_helo_restrictions = 
strict_rfc821_envelopes = no
smtpd_recipient_restrictions = permit_mynetworks,reject_unauth_destination
smtp_sasl_auth_enable = no
smtpd_sasl_auth_enable = no
smtpd_use_tls = no
smtp_use_tls = no
alias_maps = hash:/etc/aliases
mailbox_size_limit = 0
message_size_limit = 10240000
```

Siendo los parámetros : **inet_interfaces** *la ip propia por cual salir, si tenemos una sola placa de red, pondremos esa ip*, **relayhost** *es el host que nos realizará el envío SMTP*.

## SMS

Podemos configurar alertas SMS de forma muy simple con el software Gnokii.

Ejemplo de archivo de configuración de Gnokii

```ini>
Modo de uso :

**echo "Hola mundo" | sudo gnokii --sendsms 1155667798**

Definición como comando de notificación, (podemos agregarlo como comando extra al contacto, para que ademas del comando común de notificación por email tambien se ejecute este para el envío de la alerta por sms).

Notemos que lo ejecutamos por ssh, es para una situación hipotética de que el modem USB 3G de nuestro proovedor de telefonía celular este enchufado en otra maquina de nuestra red.

<code>
define command {
 command_name    host-notify-by-sms
 command_line    ssh nagios@10.15.99.11 "echo "Host '$HOSTNAME$' esta $HOSTSTATE$ $HOSTOUTPUT$" | sudo gnokii --sendsms $CONTACTPAGER$"
}
```

```
define command {
 command_name    service-notify-by-sms
 command_line    ssh nagios@10.15.99.11 "echo 'Servicio $SERVICEDESC$ en Host $HOSTNAME$ IP $HOSTADDRESS$ State $SERVICESTATE$ Info $SERVICEOUTPUT$' | sudo gnokii --sendsms $CONTACTPAGER$"
}
```

<img src="/manuales/nagios/nagios_sms.png" class="align-center" alt="nagios_sms.png" />

[nagios_sms.svg.gz](/manuales/nagios/nagios_sms.svg.gz)

## WhatsApp (Dependencia externa sin garantía)

- <https://github.com/tgalal/yowsup>

## Telegram

```python>
  * https://github.com/pommi/telegram_nagios
<code php>
#!/usr/bin/php -q
<?php
// Example: php /usr/local/nagios/sbin/telegram-notify/telegram-bot.php "370022700" "83633434:kewkekekekdkkfiirrjririrjr" 1 "Notify: NOTIFICATIONTYPE 0XzXZ0 Host: HOSTNAME 0XzXZ0 Service: SERVICEDESC 0XzXZ0 Date: SHORTDATETIME 0XzXZ0 Info: SERVICEOUTPUT"
// Using to send messages about new threads in moderated status.
// where 370022700 - chat_id.
//
/*
Bot: nagiosnotifybot
API:83633434:kewkekekekdkkfiirrjririrjr
-----
How to get Chat_ID or Username of Telegram channel/chat:
1. Add bot to chat/channel as admin with post messages permitions.
2. Type command /my_id in chat.
3. See output here: https://api.telegram.org/bot83633434:kewkekekekdkkfiirrjririrjr/getUpdates
----
*/
//date_default_timezone_set('Europe/Minsk');
ini_set('display_errors', 1);
ini_set('display_startup_errors', 1);
error_reporting(E_ALL ^ E_NOTICE);
set_time_limit(30);
define('THIS_SCRIPT', 'telegram-bot');

$chat_id = $argv[1];
$botToken = $argv[2];
$flag = $argv[3];
$message = $argv[4];

//############## LOGS ####################//
$log_file= __DIR__ . '/log/'.date('dmY').'_telegram.log';

if(!file_exists ($log_file))
	{
$fh = fopen ($log_file, "w+");
  fwrite ($fh, "");
  fclose ($fh);
	}
//##################################//

if (isset($flag))
{
	if ($flag != 1)
	{
	//$msg = 'Flag is NOT valid: ' .$flag.' Exit.';
	//echo $msg."\n";
	//file_put_contents($log_file,date('d.m.Y H:i:s').'  :    '.$msg."\r\n",FILE_APPEND );
	/**** Flag must be = 1. ****/
	die;
	}
} else{
	//$msg = 'Not input flag: ' .$flag.' Exit.';
	//echo $msg."\n";
	//file_put_contents($log_file,date('d.m.Y H:i:s').'  :    '.$msg."\r\n",FILE_APPEND );
	die;
}

$msg = "-------------------------------------------------------------------";
file_put_contents($log_file,$msg."\r\n",FILE_APPEND );

/* Debug
$msg = "----- DEBUG:\nChat_id: ".$chat_id. ".\nBot_token: ".$botToken.".\nFlag: ".$flag.".\nMessage: ".$message."\n    ----- DEBUG.";
file_put_contents($log_file,date('d.m.Y H:i:s').'  :    '.$msg."\r\n",FILE_APPEND );
Debug */

if (!$chat_id) 
	{
	$msg = 'Not input chat_id. Exit.'; 
	echo $msg."\n";
	file_put_contents($log_file,date('d.m.Y H:i:s').'  :    '.$msg."\r\n",FILE_APPEND );
	die;
	}

if (!$botToken) 
	{
	$msg = 'Not input botToken. Exit.'; 
	echo $msg."\n";
	file_put_contents($log_file,date('d.m.Y H:i:s').'  :    '.$msg."\r\n",FILE_APPEND );
	die;
	}

//Fix line break
// 0XzXZ0 - it's fake line break character defined in Nagios command definition.
function replace_linebreak($message)
{
	global $message;
	$linebreak = "\n";
	$message = preg_replace('/0XzXZ0?/m', $linebreak, $message);
	$message = urlencode($message);
    return $message;
}

function Send_msg_to_Telegram ($botToken, $telegram_chat_id, $message){
	global $url, $log_file;
	$bot_url    = "https://api.telegram.org/bot$botToken/";
	//Fix new break
	$message = replace_linebreak($message);
	$url = $bot_url."sendMessage?chat_id=".$telegram_chat_id."&text=".$message;
	if ($content = json_decode(file_get_contents($url),true)) {
		return $content['ok'];
	}else{
	$msg = "ERROR with Telegram chat: ".$telegram_chat_id.".";
	echo $msg."\n";
	file_put_contents($log_file,date('d.m.Y H:i:s').'  :    '.$msg."\r\n",FILE_APPEND );
	}
}

$TelegramGROUPS=explode(',',$chat_id);

foreach ($TelegramGROUPS as $telegram_chat_id)
{
	echo "\n";
	$msg = '------------ START posting to '.$telegram_chat_id.' ------------';
	echo $msg."\n";
	file_put_contents($log_file,date('d.m.Y H:i:s').'  :    '.$msg."\r\n",FILE_APPEND );
	$telegram_data = Send_msg_to_Telegram($botToken, $telegram_chat_id, $message);
	if (isset($telegram_data) AND $telegram_data == 1) {
		$msg = "Message was SENT to telegram ".$telegram_chat_id."\r\nText: ".$message;
		echo $msg."\n";
		file_put_contents($log_file,date('d.m.Y H:i:s').'  :    '.$msg."\r\n",FILE_APPEND );
		} else {
		$msg = "Message was NOT sent to telegram ".$telegram_chat_id."\r\nText: ".$message;
		echo $msg."\n";
		file_put_contents($log_file,date('d.m.Y H:i:s').'  :    '.$msg."\r\n",FILE_APPEND );
		continue;
	}
$msg = "------------ END posting to ".$telegram_chat_id." ------------";
echo $msg."\n";
file_put_contents($log_file,date('d.m.Y H:i:s').'  :    '.$msg."\r\n",FILE_APPEND );
}

//Clean OLD logs every 30 days
	$files = glob(realpath(dirname(__FILE__)).'/log/*.log');
	if (empty($files)) {
	exit;
	}
	foreach($files as $file){
		if(time() - filectime($file) > 2592000){
			unlink($file);
		}
	} 
//Clean OLD logs every 30 days
?>
```

<https://exchange.nagios.org/directory/Addons/Notifications/Telegram-Notifications/details>

```ini
# Host notification via Telegram bot
define command{
        command_name    notify-host-by-telegram
        command_line     curl -k -L --data chat_id=REPLACEME --data-urlencode "text=***** Nagios ***** Notification Type: $NOTIFICATIONTYPE$ Host: $HOSTNAME$ State: $HOSTSTATE$ Address: $HOSTADDRESS$ Info: $HOSTOUTPUT$ Date/Time: $LONGDATETIME$" "https://api.telegram.org/botREPLACEME/sendMessage"
        }

# Service notification via Telegram bot
define command{
        command_name    notify-service-by-telegram
        command_line   curl -k -L --data chat_id=-REPLACEME --data-urlencode "text=***** Nagios ***** Notification Type: $NOTIFICATIONTYPE$ Service: $SERVICEDESC$ Host: $HOSTALIAS$ Address: $HOSTADDRESS$ State: $SERVICESTATE$ Date/Time: $LONGDATETIME$ Additional Info: $SERVICEOUTPUT$" "https://api.telegram.org/botREPLACEME/sendMessage"
        }
```

<https://github.com/mglantz/nagios-telegram-notification>

## Visual general

En determinadas situaciones necesitamos visualizar información de forma simple, para poder comunicar a personal no técnico y que dicha información sea exacta, clara y veraz. La finalidad principal de esto, es que todos el que visualice dicha información se de por notificado de la situación que se presenta.

Los datos suministrados deben ser fáciles de identificar y preferentemente deben estar asociados a un servicio que puedan individualizar todos o la mayoria de los miembros de la organización.

La mayoría de las organizaciones optan por una pantalla gigante en la cual, quien pase frente a ella se de por anoticiado de la situación actual.

Para cumplir eficazmente con el objetivo de notificar la información a todos los miembros de la organización, el lenguaje visual debe ser claro y preciso. Se deben omitir todos los detalles irrelevantes para el usuario no ténico y se deben proveer informaciones relevantes, como ubicación geográfica, servicios afectados, y datos de contacto en sitio.

<img src="/manuales/nagios/screen_nagios.png" class="align-center" data-query="?680" width="680" alt="screen_nagios.png" /> <img src="/manuales/nagios/screen_nagvis.png" class="align-center" data-query="?680" width="680" alt="screen_nagvis.png" />
