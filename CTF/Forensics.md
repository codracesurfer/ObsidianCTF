#### Analyze exe files
https://www.hybrid-analysis.com/

### Volatility
https://github.com/volatilityfoundation/volatility/wiki/Command-Reference

#### Mount memory dump to filesystem with MemProcFS
```sh
.\MemProcFS_files_and_binaries_v5.9.12-win_x64-20240504\MemProcFS.exe -device '.\vol3(1)\mem.raw' -forensic 1 
```

### Dump file vol2
```sh
Inode Number                  Inode File Path
---------------- ------------------ ---------
          278796 0xffffa09078032328 /home/user/Downloads/notevil

python2 volatility/vol.py --profile=LinuxUbuntu1804x64 -f mem.lime linux_find_file -i 0xffffa09078032328 -O dump/notevil
```

##### Identify profile
https://heisenberk.github.io/Profile-Memory-Dump/
```shell
volatility -f dump.mem imageinfo
```

##### Using plugins
```
volatility -f victim.raw --profile=Win7SP1x64 PLUGIN
```

##### List processes
```shell
volatility --profile=PROFILE pstree -f file.dmp # Get process tree (not hidden)
volatility --profile=PROFILE pslist -f file.dmp # Get process list (EPROCESS)
volatility --profile=PROFILE psscan -f file.dmp # Get hidden process list(malware)
volatility --profile=PROFILE psxview -f file.dmp # Get hidden process list
```

##### Commandline
```sh
volatility --profile=PROFILE cmdline -f file.dmp #Display process command-line arguments
volatility --profile=PROFILE consoles -f file.dmp #command history by scanning for _CONSOLE_INFORMATION
```

##### Environment
```sh
volatility --profile=PROFILE envars -f file.dmp [--pid <pid>] #Display process environment variables

volatility --profile=PROFILE -f file.dmp linux_psenv [-p <pid>] #Get env of process. runlevel var means the runlevel where the proc is initated 
```

##### List files
```sh
volatility -f flounder-pc-memdump.elf --profile=Win7SP0x64 filescan > files.txt
```

##### Download files 
```sh
volatility --profile=Win7SP1x86_23418 filescan -f file.dmp #Scan for files inside the dump
volatility --profile=Win7SP1x86_23418 dumpfiles -n --dump-dir=/tmp -f file.dmp #Dump all files
volatility --profile=Win7SP1x86_23418 dumpfiles -n --dump-dir=/tmp -Q 0x000000007dcaa620 -f file.dmp

volatility --profile=SomeLinux -f file.dmp linux_enumerate_files
volatility --profile=SomeLinux -f file.dmp linux_find_file -F /path/to/file
volatility --profile=SomeLinux -f file.dmp linux_find_file -i 0xINODENUMBER -O /path/to/dump/file
```

##### Internet history
```sh
volatility -f flounder-pc-memdump.elf --profile=Win2008R2SP0x64 iehistory
```