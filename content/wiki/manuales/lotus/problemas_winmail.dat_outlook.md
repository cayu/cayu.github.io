---
title: "Problemas Winmail.dat Outlook"
date: 2014-01-20T16:04:27-03:00
---

## Problema

Se han reportado casos de mensajes recibidos desde un usuario usando Microsoft® Exchange en donde envía un archivo adjunto y aparece renombrado como winmail.dat en la base de datos del usuario del cliente de Lotus Notes. No es posible abrir esos archivos con el cliente de Lotus Notes. Al examinar el documento revela que el mensaje enviado contiene: Content-Type: application/ms-tnef; name=“winmail.dat”.

## Causa

Este comportamiento es debido a mensajes enviados desde Microsoft® Exchange con la opción de código MIME habilitada. Winmail.dat es adjuntado al mensaje en formato uuencoded. Información de cómo evitar que este archivo sea enviado vía Internet por usuarios que están usando Exchange, está documentado y puede ser accedida desde la página website de Soporte de Microsoft, con el número de artículo Q138053:

- <http://support.microsoft.com/default.aspx?scid=KB;en-us;q138053>

Mas información relacionada a este problema puede ser encontrada en la página de Soporte de Microsoft con el articulo numero Q196784:

- <http://support.microsoft.com/default.aspx?scid=kb;en-us;196784>

## Respuesta

Una solicitud de mejora fue enviada a Ingeniera de Calidad y la funcionalidad para decodificar archivos adjuntos winmail.dat fue agregada en versiones de Domino 6.5.6, 7.0.2 y versión 8.0. Refiérase a la página de Upgrade Central para mas detalles de cómo actualizar la versión de Notes/Domino.

Para habilitar esta opción el siguiente parámetro debe ser agregado en el notes.ini del servidor de Domino: TNEFEnableConversion=1

Nota: Este parámetro está asociado a terminanciones anormales en servidores de Domino con versión 7.0.2 y 7.0.2 FP1. Para mas información, referirse a la nota técnica titulada: “SMTP task crashes on Domino when TNEFEnableConversion=1 is enabled” (# 1252932)

Esta solución también contiene opciones adicionales:

- Mantener el objeto winmail.dat además de extraer el archivo adjunto enviado:

**TNEFKeepAttachment=1**

- Siguiente opción agregará una entrada en formato rich text conteniendo el cuerpo del mensaje:

**TNEFAttachRTF=1**

Alternativas para versiones anteriores El usuario que envía el mensaje puede configurar su aplicación para evitar la codificación TNEF. Para más detalles de cómo configurar, referirse al siguiente página de Microsoft: <http://support.microsoft.com/default.aspx?scid=KB;en-us;q241538> Otra alternativa incluye usar un agente en el cliente de Lotus Notes que pueda extraer al archivo adjunto desde winmail.dat

- <http://www.nsftools.com/tips/NotesTips.htm#extracttnef> (buscar en esta pagina por winmail.dat)
- <http://www.fentun.com/>
- <http://www.biblet.freeserve.co.uk/#WMDecode>

Información relacionada Internet Messages Containing Attachment from Outlook Re

Para aplicar el cambio en caliente, desde la consulta Lotus ejecutar

- **set config TNEFKeepAttachment=1**
- **tell router update config**

# Referencias

Title: Attachments to messages received from the Internet are named as winmail.dat

Doc \#: 1093342

URL: <http://www.ibm.com/support/docview.wss?rs=899&uid=swg21093342>
