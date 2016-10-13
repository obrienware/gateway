Setting up gateway.obrienware.com
===
Digital Ocean application: Docker 1.12.1 based on Ubuntu 16.04 

```
$ mkdir -p /data && cd /data
```

```
$ git clone https://github.com/obrienware/gateway.git
```

```
$ docker run \
  --name my-haproxy \
  -d \
  -v /data/gateway/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg \
  -p 3306:3306 \
  -p 8080:8080
  haproxy:1.6-alpine
```

```
$ nano /etc/systemd/system/haproxy.service
```
contents
```
[Unit]
Description=Gateway proxy
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill my-haproxy
ExecStartPre=-/usr/bin/docker rm my-haproxy
ExecStart=/usr/bin/docker run \
  --name my-haproxy \
  -d \
  -v /data/gateway/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg \
  -p 3306:3306 \
  -p 8080:8080 \
  haproxy:1.6-alpine

[Install]
WantedBy=multi-user.target
```

```
$ systemctl enable /etc/systemd/system/haproxy.service
```
