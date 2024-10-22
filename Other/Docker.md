```sh
docker build -t name .
docker run -p local:container name
```

### docker-cp
```sh
docker cp container-id:/path/filename.txt .
```



CTF-pwn-Dockerfile
```dockerfile
FROM ubuntu:22.04

RUN apt-get update && \
apt-get install -y gdbserver python3 python3-pip python3-dev git libssl-dev libffi-dev build-essential
RUN apt-get install -y gdb tmux wget
RUN apt-get install -y ruby-dev
RUN gem install seccomp-tools

RUN python3 -m pip install --upgrade pip && \
python3 -m pip install --upgrade pwntools

RUN apt-get install -y curl file
USER root
RUN git clone https://github.com/pwndbg/pwndbg
RUN pwndbg/setup.sh

CMD /bin/bash
```
