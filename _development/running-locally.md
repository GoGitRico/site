---
title: "Running Locally"
order: 200
---

Convox can boot your application locally using Docker in an environment identical to production.

## Installing Docker

#### OS X

Install [Docker for Mac](https://docs.docker.com/engine/installation/mac/#/docker-for-mac) to get a local Docker environment.

#### Linux

Install Docker from your application's default package manager.

## Starting the Application

#### convox start

Use `convox start` to build and run your application locally.

    $ cd ~/myapp
    $ convox start
    RUNNING: docker build -t knexsfvjdc ~/myapp
    Sending build context to Docker daemon 8.192 kB
    Step 0 : FROM ruby:2.2.2
     ---> 9664620d4c2a
    Step 1 : EXPOSE 3000
     ---> Running in d2894bf8d64b
    ...
    web    | docker run -i --name myapp-web -p 5000:3000 procfile/web sh -c ruby web.rb
    web    | [2015-09-18 06:16:53] INFO  WEBrick 1.3.1
    web    | [2015-09-18 06:16:53] INFO  ruby 2.2.2 (2015-04-13) [x86_64-linux]
    web    | == Sinatra (v1.4.6) has taken the stage on 3000 for development with backup from WEBrick
    web    | [2015-09-18 06:16:53] INFO  WEBrick::HTTPServer#start: pid=7 port=3000

This will read your `docker-compose.yml` and use the information found there to boot all of your app's processes and apply configured [links](/docs/linking). Local code changes will be [synced](/docs/code-sync) with your running processes in real time.

To exit `convox start` press `Ctrl+C`.

## Environment

`convox start` will read variables defined in a file called `.env` in the project root directory. For more information, see the [Environment](/docs/environment/#local) documentation.

## Data persistence

If your app uses a database, you'll find it useful to run a database container locally for development. To persist data for that container between starts, you can use Docker volumes to mount a host directory to the container's data directory. For example, if you're using the `convox/postgres` image, you can persist your data like this:

```yaml
database:
  image: convox/postgres
  ports:
    - 5432
  volumes:
    - /var/lib/postgresql/data
```

Convox does not recommend running datastores as containers in your Rack. Instead, you should use a hosted service, such as the [Postgres](/docs/postgresql) resource that Convox configures using Amazon RDS, or externally-hosted resources like [Compose.io's MongoDB](https://www.compose.com/mongodb). For more information, see [Resources](/docs/about-resources/).
