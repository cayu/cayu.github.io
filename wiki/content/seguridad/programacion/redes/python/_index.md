---
title: "Scripts simples en python"
date: 2019-01-03T12:06:11-03:00
---

Scanner simple de puertos :

```python>
\t", port
			#print desc[port]
		sock.close()
except KeyboardInterrupt:
	print "You stop this "
	sys.exit()
except socket.gaierror:
	print "Hostname could not be resolved"
	sys.exit()
except socket.error:
	print "could not connect to server"
	sys.exit()
t2=datetime.now()
total=t2-t1
print "scanning complete in " , total
```

Ref.: <https://medium.com/ediblesec/building-a-port-scanner-in-16-lines-of-code-26793f53f0b5>

```python
import socket
import argparse
import sys
from datetime import datetime

parser = argparse.ArgumentParser()
parser.add_argument('host')
args = parser.parse_args()

t1 = datetime.now()

try:
    for port in range(1, 1025):
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.settimeout(1)
        result = sock.connect_ex((args.host, port))
        if result == 0:
            print("Port: {} Open".format(port))
        sock.close()
except KeyboardInterrupt:
    sys.exit()
    
t2 = datetime.now()
print("Scanning completed in: {}".format(t2-t1))
```
