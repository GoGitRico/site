---
title: Services
permalink: /guide/services/
phase: build
---

A Service is a long-running component of your app, defined as a command run against an image. An app is composed of one or more services, e.g. web and worker.

Defining an app as a collection of Services enables independent horizontal scaling of each service.

A Service is defined in the `services:` section of `docker-compose.yml`.

<pre class="file yaml" title="docker-compose.yml">
version: '2'
services:
  web:
    build: .
    command: ["node", "web.js"]
  worker:
    build: .
    command: ["node", "worker.js"]
</pre>

This `docker-compose.yml` specifies two services. The `build` directives indicate that both services will use the image you just built with the `Dockerfile` in the current directory. The `command` directives specify two different programs to run against that image.

For a simple Node.js app these programs are an HTTP server (`node`) with a Redis producer (`web.js`) and a long-running Redis consumer (`worker.js`):

<pre class="file js" title="web.js">
var http = require("http");
var redis = require("redis");

var client = redis.createClient(process.env.REDIS_URL);

var server = http.createServer(function(request, response) {
  console.log(request.method, request.url)

  client.rpush("queue", JSON.stringify(request.headers));

  response.writeHead(200, {
    "Content-Type": "text/plain"
  });
  response.end("Hello World!\n");
});

server.listen(8000);

console.log("web running at http://127.0.0.1:8000/");
</pre>

<pre class="file js" title="worker.js">
var redis = require("redis");

var client = redis.createClient(process.env.REDIS_URL);

var dequeue = function() {
  client.blpop("queue", 0, function(err, data) {
    console.log(data)
    dequeue()
  })
};

client.on('connect', dequeue);

console.log("worker running");
</pre>

Write a `docker-compose.yml` that specifies the services that define your app, as in the example above.

Then, run `convox doctor` to validate your service definitions:

<pre class="terminal">
<span class="command">convox doctor</span>

### Build: Service

[<span class="pass">✓</span>] docker-compose.yml found
[<span class="pass">✓</span>] docker-compose.yml valid
[<span class="pass">✓</span>] docker-compose.yml version 2
[<span class="pass">✓</span>] Dockerfiles found
[<span class="pass">✓</span>] Service <span class="service">web</span> is valid
[<span class="pass">✓</span>] Service <span class="service">worker</span> is valid
</pre>

Now that you have defined your app's services, you can [define your environment to configure how the services run](/guide/environment/).
