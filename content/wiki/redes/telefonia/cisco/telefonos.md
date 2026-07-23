---
title: "Teléfonos CISCO"
date: 2016-06-21T12:40:05-03:00
---

## 7911G

To perform a factory reset of a 7911G phone, follow these steps:

Procedure

- Step 1 Unplug the power cable from the phone and then plug it back in.
  - The phone begins its power up cycle.
- Step 2 While the phone is powering up, and before the Applications Menu button flashes on and off, press and hold \#.
  - Continue to hold \# until the message LED on the handset flashes on and off in sequence in red.
- Step 3 Release \# and press 123456789\*0#.

You can press a key twice in a row, but if you press the keys out of sequence, the factory reset will not take place.

After you press these keys, the message LED on the handset flashes faster in red and the phone goes through the factory reset process.

Do not power down the phone until it completes the factory reset process and the main screen appears.

<http://www.cisco.com/en/US/products/hw/phones/ps379/products_administration_guide_chapter09186a00805adaec.html#wp1085546>

There is also an alternate method in this Tac document which was kindly provided by Paolo (Thanks Paolo!)

<http://www.ciscotaccc.com/kaidara-advisor/voice/showcase?case=K43691258>

**Referencia :** <https://supportforums.cisco.com/discussion/10182766/factory-reset-7911>

## 7941G

If there is only one 7941 phone which is not booting properly and other phones are working fine

then we can try factory resetting of that particular phone

To initiate the factory reset:

Start with the phone in a powered off state. While holding the “\#” key down on the keypad, apply power to the phone. Keep the “\#” key depressed until you see the LEDs on the phone begin a ‘walking’ sequence, where they walk down from top to bottom. After you see the LED walk sequence begin, you can release the “\#” key and enter one of the reset sequences below. You have to enter the sequence as is, any deviation from the given sequence will abort the factory reset and resume a normal boot sequence.

Soft Reset

The code for the soft factory reset is: 123456789\*0#

A soft reset does the following:

- deletes key files on the phone including /etc/init.tab
- reboots
- phone recovers default loads file from TFTP server

Total Hard Reset

The code for the total hard reset is: 3491672850\*#

A total hard reset formats /flash0 blowing away everything except CNU. All settings and personalization of the phone is removed.

Hard Reset with Network Setting Preservation

The code for the network preservation hard reset is: \*7412369#085

And it also does a complete format of /flash0 - yet it preserves the network settings so that after the format, any static IP and other network settings are preserved. The settings are stored in volatile RAM, so it is important to not remove power to the phone during this process

For CSCsi60439 reference please see document EDCS-609633.

Prerequisites

There are a number of prerequsites that are necessary for a successful factory reset.

You must have a network environment in which DHCP is used to provide the phone with an IP address and a TFTP server (i.e. DHCP option 150). There must be an appropriate default load for your phone model on that TFTP server: The default load name is: termxx.default.loads (where xx is part of the model number, 41 for 7941, 62 for 7962, 70 for 7970, etc.) The version of the default load must be the same signing type (dev or release) as the phone on which you wish to do a factory reset. This file (termxx.default.loads) is packaged with every phoneload that is installed on the callmanager, but since the name is not unique per phoneload version, the copy of termxx.default.loads is always the one from the most recent load that was installed on the callmanager. It may not be the type (dev or release) that you want.

**Referencia :** <https://supportforums.cisco.com/discussion/10801871/cisco-ip-phone-7941>
