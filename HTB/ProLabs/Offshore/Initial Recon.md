
### nmap
```
Nmap scan report for 10.10.110.3
Host is up (0.035s latency).
Not shown: 65534 filtered tcp ports (no-response)
PORT    STATE SERVICE  VERSION
443/tcp open  ssl/http nginx
|_http-title: 400 The plain HTTP request was sent to HTTPS port
| tls-alpn:
|   h2
|_  http/1.1
| ssl-cert: Subject: commonName=pfSense-5ad49c9e3b8b9/organizationName=pfSense webConfigurator Self-Signed Certificate/stateOrProvinceName=State/countryName=US
| Subject Alternative Name: DNS:pfSense-5ad49c9e3b8b9
| Not valid before: 2018-04-16T12:52:46
|_Not valid after:  2023-10-07T12:52:46
|_ssl-date: TLS randomness does not represent time
| tls-nextprotoneg:
|   h2
|_  http/1.1

Nmap scan report for 10.10.110.123
Host is up (0.036s latency).
Not shown: 65530 filtered tcp ports (no-response)
PORT     STATE  SERVICE        VERSION
22/tcp   open   ssh            OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 ed:da:93:ee:2e:2b:7a:02:4d:97:3d:1b:f2:40:ba:f6 (RSA)
|   256 7e:de:fa:0c:9d:4c:6c:01:7c:0a:0c:f1:74:4d:f3:5f (ECDSA)
|_  256 15:ab:fc:b8:a2:fa:f1:57:d7:3f:bc:ab:ad:d0:cc:99 (ED25519)
80/tcp   open   http           Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: ACME Bank
8000/tcp open   http           Splunkd httpd
| http-robots.txt: 1 disallowed entry
|_/
| http-title: Site doesn't have a title (text/html; charset=UTF-8).
|_Requested resource was http://10.10.110.123:8000/en-US/account/login?return_to=%2Fen-US%2F
|_http-server-header: Splunkd
8089/tcp open   ssl/http       Splunkd httpd (free license; remote login disabled)
| http-auth:
| HTTP/1.1 401 Unauthorized\x0D
|_  Server returned status 401 but no WWW-Authenticate header.
| ssl-cert: Subject: commonName=SplunkServerDefaultCert/organizationName=SplunkUser
| Not valid before: 2018-02-02T20:26:16
|_Not valid after:  2021-02-01T20:26:16
|_http-server-header: Splunkd
|_http-title: Site doesn't have a title (text/xml; charset=UTF-8).
8191/tcp closed limnerpressure
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Nmap scan report for 10.10.110.124
Host is up (0.036s latency).
Not shown: 65534 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 10.0
| http-methods:
|_  Potentially risky methods: TRACE
|_http-title: Offshore Dev
|_http-server-header: Microsoft-IIS/10.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```
