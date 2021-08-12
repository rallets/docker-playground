# docker-playground

Based on the Pluralsight course [Getting Started with Docker](https://app.pluralsight.com/library/courses/getting-started-docker) by Nigel Poulton.

## DOCKER commands

build a new image with tag (-t). maurobertoli is the docker.com account
>docker image build -t maurobertoli/gsd:first-ctr .

list of available images
> docker image ls

remove an image from local
> docker image rm maurobertoli/gsd:first-ctr

list of running containers
> docker container ls

get an image from the repo and start it | -d = detached (in background) | --name = the container name
> docker container run -d --name web -p 8000:8080 maurobertoli/gsd:first-ctr

stop a containerized app (an app running inside a container) gracefully
> docker container stop web

start a containerized app
> docker container stop web

remove a containerized app
> docker container rm web

force removing a running container
> docker container rm web -f

run a container in interactive mode (-it) using `alpine` linux dist and run the command `sh` as entrypoint. type `exit` to exit sh and then exit the container.
> docker container run -it --name test alpine sh

press CTRL + P + Q (process quit) to exit the container BUT keeping running it in the background

run a windows container in interactive mode
> docker container run -it mcr.microsoft.com/powershell:nanoserver pwsh.exe

## DOCKER COMPOSE

Docker compose allows to create multi-container app (many images + network as example)

create all the images needed and start the containers | -d = detached (in background)

> docker-compose up -d

shotdown all the containers
> docker-compose down

print your resolved application config to the terminal
> docker-compose config

## DOCKER SWARM

init docker swarm, specify an ip different to the host if needed, and set it as manager. You will get a token and a command to add a service

> docker swarm init --advertise-addr=192.168.0.20

add a manager to the same swarm (from another host/machine/ip)
> docker swarm join-token manager

add a service to the swarm
> docker swarm join --token SWMTKN-1-xxxx...xxx 192.168.0.20:2377

list all nodes in the swarm (managers and workers)
> docker node ls

add a worker to a swarm (running from a manager host), this we give a command with a token
> docker swarm join-token worker

then run this from the machines that you have to join as a worker
> docker swarm join --token SWMTKN-1-xxxx...xxx 192.168.0.20:2377

### DOCKER SWARM - manage apps

pre-requisite: swarm initialized

create a service from an existing image. Only existing images can be used.
> docker service create --name web -p 8080:8080 --replicas 3 maurobertoli/gsd:first-ctr

list all services in the swarm
> docker service ls

list the containers (in this case 3 equal rows for the image) for this host (note: only this machine)
> docker container ls

list the containers (in this case 3 equal rows for the image) for this swarm (you will get info about hte node too)
> docker service ps web

scale the service called "web" to 10 instances
> docker service scale web=4

if you try to force remove some containers, they will be restarted asap
> docker container rm xxx-ID-1 xxx-ID-2 -f

remove the service and related containers
> docker service rm web

### DOCKER SWARM - yml file

we can obtain a `stack` the same scenario using the declarative way with a yml file (look at the `swarm-stack` folder).

Note that only existing images in a registry can be used, so you need to specify an image name (instead of `build: .`)

So just add to an existing `docker-compose.yml`

``` yml
image: maurobertoli/gsd:first-ctr
deploy:
    replicas: 10
```

Then deploy with (stackname is your stack/application name)
> docker stack deploy -c docker-compose.yml stackname

check the stack
> docker stack ls

check the services deployed in the stack
> docker stack services stackname

check the containers and more info on the stack
> docker stack ps stackname

you can edit the yml file and apply the changes (ex. replicas: 5) and re-run the same command
> docker stack deploy -c docker-compose.yml stackname

remove the stack and related containers
> docker stack rm stackname
