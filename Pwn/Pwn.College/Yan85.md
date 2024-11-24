# Yan85 Instruction Set Architecture

## Registers
- `a`: General purpose register, often used for return values from syscalls
- `b`: General purpose register, often used for memory addressing
- `c`: General purpose register, often used for increments/offsets
- `d`: General purpose register, often used for data values

## Basic Instructions
- `IMM reg = value`: Immediate load - Loads an immediate value into a register
  Example: `IMM b = 0x7f` loads 0x7f into register b

- `ADD dest src`: Addition - Adds the value in src register to dest register
  Example: `ADD b c` adds the value in register c to register b

- `STM *addr = value`: Store Memory - Stores a value into memory at the address specified
  Example: `STM *b = a` stores the value in register a at the memory address specified by register b

## System Calls (SYS)
System calls are invoked using the `SYS code reg` instruction, where code is the syscall number and reg contains arguments.

Known syscalls:
1. `SYS 0x4 reg`: read_memory
   - Returns: Value read in register a
   
2. `SYS 0x10 reg`: write
   - Writes a single character to output
   - Argument: Register should contain 1 for stdout
   - Uses value at memory address 0 as the character to write
   
3. `SYS 0x20 reg`: exit
   - Terminates program execution

## Memory Model
- Byte-addressable memory
- Memory operations use registers for addressing
- The architecture appears to use a simple flat memory model
- Memory addresses seen in trace: 0x7f-0x9f range used for operations

## Program Example Analysis
From the trace, a typical program flow might look like:

1. Setup memory operations:
```
IMM b = 0x7f    # Set base address
IMM c = 0x4     # Set increment value
IMM a = 0       # Clear register a
```

2. Memory operations:
```
STM *b = a      # Store value
ADD b c         # Increment address
```

3. Output operations:
```
IMM a = 0x1     # Set stdout
IMM d = 0x49    # Character to output ('I')
STM *b = d      # Store character
SYS 0x10 a      # Write syscall
```

## Notes
- The architecture uses hexadecimal values extensively
- It appears to be a RISC-like architecture with simple instructions
- Memory access is register-indirect
- System calls handle I/O operations


Things you may want to ask/find

- How many bytes is a yancode instruction?
		1 byte
		` printf("[I] op:%#hhx arg1:%#hhx arg2:%#hhx\n", BYTE2(a2), (unsigned __int8)a2, BYTE1(a2));`
- Where are the instruction opcodes?
- Where are the syscall opcodes?
- What is the order of the instruction bytes?
- Where is the stack?
- Where is memory located?



```c
__int64 __fastcall interpret_instruction(unsigned __int8 *a1, __int64 a2)
{
  __int64 result; // rax

  printf(
    "[V] a:%#hhx b:%#hhx c:%#hhx d:%#hhx s:%#hhx i:%#hhx f:%#hhx\n",
    a1[1024],
    a1[1025],
    a1[1026],
    a1[1027],
    a1[1028],
    a1[1029],
    a1[1030]);
  printf("[I] op:%#hhx arg1:%#hhx arg2:%#hhx\n", BYTE2(a2), (unsigned __int8)a2, BYTE1(a2));
  if ( (a2 & 0x800000) != 0 )
    interpret_imm(a1, a2);
  if ( (a2 & 0x20000) != 0 )
    interpret_add(a1, a2);
  if ( (a2 & 0x200000) != 0 )
    interpret_stk(a1, a2);
  if ( (a2 & 0x100000) != 0 )
    interpret_stm(a1, a2);
  if ( (a2 & 0x80000) != 0 )
    interpret_ldm(a1, a2);
  if ( (a2 & 0x400000) != 0 )
    interpret_cmp(a1, a2);
  if ( (a2 & 0x10000) != 0 )
    interpret_jmp(a1, a2);
  result = BYTE2(a2) & 4;
  if ( (a2 & 0x40000) != 0 )
    return interpret_sys(a1, a2);
  return result;
}
```