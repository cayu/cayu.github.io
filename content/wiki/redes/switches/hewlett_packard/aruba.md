---
title: "Switches Aruba"
date: 2019-06-10T16:19:00-03:00
---

## Configurar autenticación Microsoft NPS

;Disable the telnet server since we don’t want domain creds flying around in plain text no telnet-server

;Setup the RADIUS server info radius-server host \<NPS Server IP\> radius-server key “\<Your Secret Key from the RADIUS Client in NPS\>”

;Set switch to go immediately to enable mode once authenticated aaa authentication login privilege-mode

;Configure specific interfaces to use the RADIUS server aaa authentication web login peap-mschapv2 aaa authentication web enable peap-mschapv2 aaa authentication ssh login peap-mschapv2 aaa authentication ssh enable peap-mschapv2

;I omitted console since I want a backdoor in case of network issues, and have adequate physical access controls ;aaa authentication console login peap-mschapv2 local ;aaa authentication console enable peap-mschapv2 local

Ref. : <https://ehaskins.com/2016-06-28%20Configuring%20NPS%20for%20authentication%20on%20HP%20switches/>
