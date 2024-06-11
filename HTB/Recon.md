### nmap
```sh
sudo nmap -sC -sV -T5 -p- -oA nmap/scanresult IP
```
##### nmap with UDP (takes a long time)
```sh
sudo nmap -sU  IP
```

### gobuster
```sh
gobuster vhost -u .htb -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain -k -r

gobuster dir -u .htb -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt -b 301 -k -r
```

### ffuf 
```sh
ffuf -X POST -H "Content-Type: application/x-www-form-urlencoded" -w /usr/share/wordlists/SecLists/Discovery/Web-Content/api/actions.txt -u http://2million.htb/api/v1/user/vpn/FUZZ

ffuf -u http://akerva.htb:5000/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-small-words.txt
```

### sqlmap
```sh
sqlmap -u URL --data="username=*&password=*" --method POST --dbs --batch --level 5


# use sqlmap with a request and a vulnerable parameter
sqlmap -r request_sqlmap.txt -p q
# dump all
sqlmap -r update_details.txt -p login --dump-all -batch --dbms=mysql > dump_output.txt
```


### samba
```sh
smbclient USER@IP
sudo smbstatus IP

# Find shares (don't enter password)
smbclient -L USER@IP

# Connect to share
smbclient '\\IP\SHARENAME'
```

### mysql
```sh
mysql --host=db --user=root --password=root cacti -e "SELECT * FROM user_auth_realm"

# Login
mysql -u USER -p -h IP

# Once in
> show databases;
> use DATABASENAME;
> SELECT table_name FROM information_schema.tables WHERE table_schema = 'sys';

> SELECT * FROM TABLENAME;
```

### ftp
```sh
ftp IP
name: anonymous
password: ANYTHING


# Download all files
wget -m ftp://user:pass@server.com/

# if passive mode is on, disable it by typing "passive" after login
```

### hydra
```sh
sudo hydra -l admin -P /usr/share/wordlists/rockyou.txt drive.htb http-post-form "/login/:username=admin&password=^PASS^:Please enter a correct username and password. Note that both fields may be case-sensitive."
```

### Wordpress
##### Wpscan
```sh
wpscan --url http://10.13.37.11 --api-token '165yGHW6HijcOg1pFB2D7ojiQdyGLRJJZba61vvdmQc' --wp-content-dir /wp-content/ --enumerate vp --plugins-detection aggressive
```
