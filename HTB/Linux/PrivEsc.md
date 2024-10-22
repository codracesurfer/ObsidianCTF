
##### Check what you can run as root
```sh
sudo -l
```
##### Check running processes
```sh
ps -ef --forest
```
##### Find all suid binaries
```sh
find / -perm -4000 2>/dev/null
```

##### Find writable directories
```sh
find / -writable -type d 2>/dev/null
```

##### List running network services
```sh
netstat -atup
```

#### Proxy through box to view local webpages (CHISEL)
##### On box
```sh
./chisel client LOCALIP:PORT R:1080:socks
```
##### On local
```sh
./chisel server -p PORT --reverse
```


#### Static binaries
https://github.com/andrew-d/static-binaries/tree/master


##### Check mail
```sh
ls /var/mail
```

https://exploit-notes.hdks.org/

### Linpeas
https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS


