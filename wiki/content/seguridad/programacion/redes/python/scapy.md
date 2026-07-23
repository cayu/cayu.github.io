---
title: "Scapy"
date: 2019-08-29T16:08:07-03:00
---

Scapy es una herramienta de manipulación de paquetes de red, escrita en Python. Puede falsear o decodificar paquetes, enviarlos, capturarlos y hacer coincidir solicitudes y respuestas.

## Ejemplos

### Sniffer

```python>
", prn = lambda x: x.show(), filter="tcp", store=0)
```

Python/Scapy FTP Password Sniffer :

```python>
 ' + str(pkt[IP].src).strip() + ':'
            print '\t   [*] Username: ' + username
            print '\t   [*] Password: ' + password + '\n'
            return
        else:
            return
    except Exception:
        return 
 
def check_for_ftp(pkt):
    if pkt.haslayer(TCP) and pkt.haslayer(Raw):
        if pkt[TCP].dport == 21 or pkt[TCP].sport == 21:
            return True
        else:
            return False
    else:
        return False
 
def check_pkt(pkt):
    if check_for_ftp(pkt):
        pass
    else:
        return
    data = pkt[Raw].load
    if 'USER ' in data:
        usernames.append(data.split('USER ')[1].strip())
    elif 'PASS ' in data:
        passwords.append(data.split('PASS ')[1].strip())
    else:
        check_login(pkt, usernames[-1], passwords[-1])
    return
 
print '[*] Sniffing Started on %s... \n' % interface
try:
    sniff(iface=interface, prn=check_pkt, store=0)
except Exception:
    print '[!] Error: Failed to Initialize Sniffing'
    sys.exit(1)
print '\n[*] Sniffing Stopped'
```

### Ping

```python>
==== Ejemplos de 0x90 ====
Ejemplos de [[https://github.com/0x90/uberscapy|0x90]].

Sniffing on eth0:
<code python>
    sniff(iface="eth0", prn=lambda x: x.summary())
    sniff(iface="eth0", prn=lambda x: x.show())
```

Formated sniff output

```python>
 %IP.dst%\n}{Raw:%Raw.load%\n}"))
```

Traceroute:

```python>
 grafico.svg")
```

Port scanner:

```python
	res.unans = sr( IP(dst="target")/TCP(flags="S", dport=(1,1024)) )
	res.nsummary( lfilter=lambda (s,r): (r.haslayer(TCP) and (r.getlayer(TCP).flags & 2)) )
```

## Referencias

<https://github.com/0x90/uberscapy>
