```
riley@mail:~$ ./nmap 192.168.110.0/24

Starting Nmap 6.49BETA1 ( http://nmap.org ) at 2024-03-22 13:07 UTC
Unable to find nmap-services!  Resorting to /etc/services
Cannot find nmap-payloads. UDP payloads are disabled.
Nmap scan report for _gateway (192.168.110.1)
Host is up (0.00069s latency).
Not shown: 1179 filtered ports
PORT    STATE SERVICE
53/tcp  open  domain
80/tcp  open  http
443/tcp open  https

Nmap scan report for mail (192.168.110.51)
Host is up (0.000054s latency).
Not shown: 1173 closed ports
PORT    STATE SERVICE
22/tcp  open  ssh
25/tcp  open  smtp
80/tcp  open  http
110/tcp open  pop3
143/tcp open  imap2
443/tcp open  https
587/tcp open  submission
993/tcp open  imaps
995/tcp open  pop3s

Nmap scan report for 192.168.110.56
Host is up (0.00017s latency).
Not shown: 1178 closed ports
PORT     STATE SERVICE
135/tcp  open  epmap
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Nmap done: 256 IP addresses (3 hosts up) scanned in 8.15 seconds
riley@mail:~$
```