### Remember username and password (stores in plaintext in home folder)
```shell
git config --global credential.helper store
```

### Create local git repo and share its url on LAN
```sh
git init -b main

echo "test" > test.txt
git add -A
git commit -m "test"

git-http-server -p 8001 # Create the server

git clone http://10.10.16.9:8001/.git # clone the repo
```

### Create latex graph from git log
```bash
git log --color --graph --oneline | ansifilter --latex > log.tex
```
