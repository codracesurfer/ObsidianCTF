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