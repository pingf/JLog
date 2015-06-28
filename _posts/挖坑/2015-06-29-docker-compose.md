---
layout: post
author: Makefile君
title: docker compose的使用
category: 挖坑
tags: docker node
date: 2015-06-28 23:44:53
keywords:
description:
---

docker-compose可以将不同的dockerfile放到一个yaml文件中,它还可以指定不同的dockerfile这一点是很方便的.

比如有两个dockerfile,

下面这个是Dockerfile

~~~
FROM python:3.4
ENV PYTHONUNBUFFERED 1
RUN mkdir /code
WORKDIR /code
ADD ./test1.txt /code/
~~~

下面这个是Dockerfile2

~~~
FROM python:3.4
ENV PYTHONUNBUFFERED 1
RUN mkdir /code
WORKDIR /code
ADD ./test2.txt /code/
ADD ./test22.txt /code/
~~~

然后可以通过docker-compose.yml进行管理,

下面这个是docker-compose.yml

~~~
weba :
  build: .
  command: python -m http.server 8001
  ports:
    - "8001:8001"
webb :
  build: .
  dockerfile: Dockerfile2
  command: python -m http.server 8002
  ports:
    - "8002:8002"
~~~


然后执行`docker-compose build`会构建两个镜像,执行`docker-compose up`会启动两个container.

-------------------------------

下面再看这个更加复杂的例子

~~~
├── docker-compose.yml
├── Dockerfile
├── haproxy
│   └── haproxy.cfg
└── src
    ├── index.js
    └── package.json
~~~

Dockerfile中定义了每个web节点的功能, 在其中mount进了src目录,它使用nodejs的express库构建了一个简单的服务.

而除了代码外,还使用了haproxy实现类似elb的功能.

下面是docker-compose.yml

~~~
eba:
  build: .
  expose:
    - 80

webb:
  build: .
  expose:
    - 80

webc:
  build: .
  expose:
    - 80

haproxy:
  image: haproxy
  volumes:
   - haproxy:/usr/local/etc/haproxy
  links:
   - weba
   - webb
   - webc
  ports:
   - "80:80"
   - "70:70"

  expose:
   - "80"
   - "70"
~~~

下面是Dockerfile

~~~
FROM node:0.12
ADD src/ /src
WORKDIR /src
RUN npm install
EXPOSE 80
CMD ["node", "index.js"]
~~~

nodejs

~~~nodejs
ar express = require('express');
var os = require("os");

var app = express();
var hostname = os.hostname();

app.get('/', function (req, res) {
  res.send('<html><body>Hello from Node.js container ' + hostname + '</body></html>');
});

app.listen(80);
console.log('Running on http://localhost');
~~~

package.json

~~~
{
  "name": "node-hello-world",
  "private": true,
  "version": "0.0.1",
  "description": "Node.js Hello world app on docker",
  "author": "jesse",
  "dependencies": {
    "express": "4.12.0"
  }
}
~~~

haproxy配置haproxy.cfg
~~~
global
  log 127.0.0.1 local0
  log 127.0.0.1 local1 notice

defaults
  log global
  mode http
  option httplog
  option dontlognull
  timeout connect 5000
  timeout client 10000
  timeout server 10000

listen stats :70
  stats enable
  stats uri /

frontend balancer
  bind 0.0.0.0:80
  mode http
  default_backend aj_backends

backend aj_backends
  mode http
  option forwardfor
  # http-request set-header X-Forwarded-Port %[dst_port]
  balance roundrobin
  server weba weba:80 check
  server webb webb:80 check
  server webb webc:80 check
  # option httpchk OPTIONS * HTTP/1.1\r\nHost:\ localhost
  option httpchk GET /
  http-check expect status 200
~~~

配好之后使用之前介绍的启动方式启动即可
