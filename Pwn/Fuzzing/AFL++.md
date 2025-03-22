## Install

```
sudo apt-get update
sudo apt-get install -y build-essential python3-dev automake cmake git flex bison libglib2.0-dev libpixman-1-dev python3-setuptools cargo libgtk-3-dev
# try to install llvm 14 and install the distro default if that fails
sudo apt-get install -y lld-14 llvm-14 llvm-14-dev clang-14 || sudo apt-get install -y lld llvm llvm-dev clang
sudo apt-get install -y gcc-$(gcc --version|head -n1|sed 's/\..*//'|sed 's/.* //')-plugin-dev libstdc++-$(gcc --version|head -n1|sed 's/\..*//'|sed 's/.* //')-dev
sudo apt-get install -y ninja-build # for QEMU mode
sudo apt-get install -y cpio libcapstone-dev # for Nyx mode
sudo apt-get install -y wget curl # for Frida mode
sudo apt-get install -y python3-pip # for Unicorn mode
git clone https://github.com/AFLplusplus/AFLplusplus
cd AFLplusplus
make all
sudo make install
```

## Compile project with AFL++
```
export LLVM_CONFIG="llvm-config-14"
CC=/AFLplusplus/afl-clang-fast CXX=/AFLplusplus/afl-clang-fast++ ./configure --prefix="$HOME/fuzzing_xpdf/install/"
make
make install
```

## Run project with AFL++
```
afl-fuzz -i $HOME/fuzzing_xpdf/pdf_examples/ -o $HOME/fuzzing_xpdf/out/ -s 123 -- $HOME/fuzzing_xpdf/install/bin/pdftotext @@ $HOME/fuzzing_xpdf/output
```

A brief explanation of each option:

- _-i_ indicates the directory where we have to put the input cases (a.k.a file examples)
- _-o_ indicates the directory where AFL++ will store the mutated files
- _-s_ indicates the static random seed to use
- _@@_ is the placeholder target's command line that AFL will substitute with each input file name

So, basically the fuzzer will run the command  
`$HOME/fuzzing_xpdf/install/bin/pdftotext <input-file-name> $HOME/fuzzing_xpdf/output` for each different input file.

If you receive a message like _"Hmm, your system is configured to send core dump notifications to an external utility..."_, just do:

```
sudo su
echo core >/proc/sys/kernel/core_pattern
exit
```

