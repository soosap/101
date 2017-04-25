# Docker Technologies

## Docker Hub

$ docker login -e $DOCKER_HUB_EMAIL -u $DOCKER_HUB_USERNAME -p $DOCKER_HUB_PASSWORD


## Docker Engine

$ docker exec -it soosap-me bash
Error response from daemon: client is newer than server (client API version: 1.24, server API version: 1.22)
$ export DOCKER_API_VERSION=1.22

Stop all containers on a Docker Host
$ docker stop $(docker ps -a -q)

Remove all containers on a Docker Host
$ docker rm $(docker ps -a -q)

Remove all busybox containers
$ docker ps -a | grep 'busybox' | awk '{print $1}' | xargs docker rm

Remove all images from a Docker Host
$ docker images --no-trunc | grep none | awk '{print $3}' | xargs docker rmi

Remove unused Docker Volumes from Host
$ docker volume ls -qf dangling=true | xargs docker volume rm


## Docker Machine

* > $ docker-machine ls

    Lists all docker machines. *default* is the one on your local development laptop.

        NAME      ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER    ERRORS
        default   *        virtualbox   Running   tcp://192.168.99.100:2376           v1.10.3  


* > $ docker-machine ip default

    Determines the IP of a docker machine.

        192.168.99.100


* > $ docker-machine status

    Provides information about the state of a docker-machine. It may be `Running` or `Stopped`.


* > $ docker-machine stop default


* > $ docker-machine start default


* > $ docker-machine env default

    Configures the environment for a machine. When you first pull up a new command-line terminal window you need to tell
    the system to which docker-machine you want to connect to. Initially, you are not connected to either machine.
    Issuing Docker Client commands on a non-connected terminal window will yield in
    `Cannot connect to the Docker daemon. Is the docker daemon running on this host?`


* > $ docker-machine create --driver vmwarefusion --vmwarefusion-cpu-count "4" --vmwarefusion-disk-size "60000" --vmwarefusion-memory-size "8192" seetha

* > $ docker-machine ssh seetha

## Docker Client

```sh
$ docker run -d -p 3000:80 -v /var/www --name mycontainer kitematic/hello-world
```
The `docker run` command spins off a running container out of a Docker Image.
* The `-p` flag exposes port 80 on the **Docker Container** to port 3000 on the **Docker Machine**
* The `-d` flag runs the container in detached mode
* The `-v` flag specifies a volume
* The `--name` flag labels the container

```sh
$ docker ps

$ docker ps -a

$ docker stop mycontainer

$ docker rm mycontainer

$ docker images

$ docker images -a

$ docker rmi 32de
```

```sh
$ docker build -t saronia/saronia-frontend -f Dockerfile.nginx --no-cache .
```
* The `-t` flag specifies tags for the image build
* The `-f` flag specifies a non-standard Dockerfile path and/or name
* The `--no-cache` would build the container from scratch w/o leveraging the cache
* The `.` represents the build context

```sh
$ docker login -e soosap@saronia.io -p secret -u username

$ docker push saronia/saronia-frontend:latest

$ docker pull saronia/saronia-frontend:latest

$ docker exec d6er4 node dbSeeder.js
// Execute a command on a running container
```

## Docker Swarm

1) Create docker machines w/ latest docker engine install
2) Configure Swarm Mode
3) Run services in a docker swarm
4) Update services in a docker swarm

### Create swarm nodes w/ docker-machine

```sh
$ docker-machine create --driver amazonec2 --amazonec2-region eu-central-1 --amazonec2-zone a --engine-install-url https://experimental.docker.com/ sapiras

$ docker-machine create --driver amazonec2 --amazonec2-region eu-central-1 --amazonec2-zone a --engine-install-url https://experimental.docker.com/ seetha

$ docker-machine create --driver amazonec2 --amazonec2-region eu-central-1 --amazonec2-zone a --engine-install-url https://experimental.docker.com/ dugorim

$ docker-machine create --driver vmwarefusion --vmwarefusion-cpu-count "4" --vmwarefusion-disk-size "60000" --vmwarefusion-memory-size "8192" zalia

$ docker-machine create --driver virtualbox --virtualbox-cpu-count "4" --virtualbox-disk-size "60000" --virtualbox-memory "8192" karthik

$ docker-machine upgrade karthik sapiras
```

```sh
$ docker-machine ssh dugorim
$ sudo -i
$ apt-cache policy docker-engine
$ apt-get install docker-engine=1.12.2-0~wily
$ apt-get install docker-engine=1.12.0-0~wily
$ apt-get install docker-engine=1.12.2~rc3-0~wily
```
Install a very specific version of the docker engine on a particular docker machine.

### Configure swarm mode

Expose PORT 2377 on AWS
---------------------------------------------
Make sure that the EC2 instances can communicate with each other over PORT 2377. You may have to add a "docker-swarm" Security Group to these instances exposing PORT 2377 to all other instances in that subnet.



Elect docker-machine as swarm leader
---------------------------------------------
Look up sapiras-EC2-instance's private IP in AWS console >>>> IMPORTANT <<<<<
>> 172.31.12.76

$ docker-machine ssh sapiras
$ sudo -i

$ docker swarm init --advertise-addr 172.31.12.76:2377 --listen-addr 172.31.12.76:2377

Temporarily copy the respective code snippets in a text file
$ docker swarm join-token manager
>> docker swarm join --token SWMTKN-1-5ld4vtza74ff4ig81qtxasmiu8yihxhd9r3iad0v9n8k379v0f-f2ujimcc9fyh7rbvms6118dtq 172.31.12.76:2377


$ docker swarm join-token worker
>> docker swarm join --token SWMTKN-1-2iqgmnrwma84tkpbrnomav1lu9yxe5bt7nibg5kv5h2syvt4ba-4hdzrizahcku15c8qvp30qfny 172.31.12.76:2377


Join a manager to the swarm, i.e. seetha
---------------------------------------------
Look up seetha-EC2-instance's private IP in AWS console >>>> IMPORTANT <<<<<
>> 172.31.15.81

$ docker-machine ssh seetha
$ sudo -i

To the "docker swarm join-token manager" output append the following "--advertise-addr 172.31.15.81:2377 --listen-addr 172.31.15.81:2377" and run the command in the new manager node, i.e. seetha

$ docker swarm join --token SWMTKN-1-5ld4vtza74ff4ig81qtxasmiu8yihxhd9r3iad0v9n8k379v0f-f2ujimcc9fyh7rbvms6118dtq 172.31.12.76:2377 --advertise-addr 172.31.15.81:2377 --listen-addr 172.31.15.81:2377


Join a manager to the swarm, i.e. dugorim
---------------------------------------------
Look up seetha-EC2-instance's private IP in AWS console >>>> IMPORTANT <<<<<
>> 172.31.6.83

$ docker-machine ssh dugorim
$ sudo -i

To the "docker swarm join-token worker" output append the following "--advertise-addr 172.31.6.83:2377 --listen-addr 172.31.6.83:2377" and run the command in the new manager node, i.e. dugorim

$ docker swarm join --token SWMTKN-1-2iqgmnrwma84tkpbrnomav1lu9yxe5bt7nibg5kv5h2syvt4ba-9o2tb9tl1aalvp9hlj67v7uxf 172.31.12.76:2377 --advertise-addr 172.31.6.83:2377 --listen-addr 172.31.6.83:2377



Troubleshooting
---------------------------------------------

It seems to kill a swarm, the only way is to re-init the swarm on the leader specifying the --force-new-cluster flag and to remove the other nodes from the dead swarm using $ docker swarm leave --force
$ docker swarm leave

Dealing with corrupted manager nodes
https://github.com/docker/docker/issues/26477
https://github.com/docker/docker/issues/25432
$ apt-get remove docker-engine
$ rm -rf /var/lib/docker
$ apt-get install docker-engine=1.12.3-0~wily


Congratulations! Docker Swarm is up and running with 2 Manager and 1 Worker Nodes.



---------------------------------------------
Run Services in a Docker Swarm
---------------------------------------------
$ docker service create --name dockercloud -p 80:80 --replicas 2 dockercloud/hello-world

$ docker service ps dockercloud

$ docker service tasks <service_name>

$ docker service inspect dockercloud

$ docker service ls

$ docker service rm <id>

$ docker service scale dockercloud=3
$ docker service update --replicas 3 dockercloud

$ docker network ls
$ docker network create --driver overlay psight
$ docker service create --name psight1 --network psight -p 80:8080 --replicas 2 nigelpoulton/pluralsight-docker-ci

---------------------------------------------
Run Services for SARONIA Docker Swarm
---------------------------------------------
$ docker network create saronia_haproxy --driver overlay
$ docker network create saronia_jenkins --driver overlay
$ docker network create soosap_me --driver overlay

# Add the saronia_jenkins network to all services that shall obtain automated rolling updates
# Add the saronia_haproxy network to all services that shall be accessible through the Internet/through a domain
# mode global makes sure that one "task (container)" is run on every single "node (Docker Engine)"

$ docker service create \
  --name saronia-haproxy
  --publish 80:80 \
  --network saronia_haproxy \
  --network saronia_jenkins \
  --mode=global \
  saronia/haproxy:latest

$ docker service create --name saronia-haproxy --publish 80:80 --network saronia_haproxy --network saronia_jenkins --mode=global saronia/haproxy:latest

Debug errors through reading haproxy_log logs
$ docker logs saronia-haproxy

$ docker service create \
  --name saronia-jenkins \
  --publish 8000:8080 \
  --network saronia_haproxy \
  --network saronia_jenkins \
  --replicas 1 \
  --mount type=volume,source=jenkins_home,target=/var/jenkins_home,volume-driver=local \
  --mount type=volume,source=jenkins_log,target=/var/log/jenkins,volume-driver=local \
  --mount type=bind,source=/var/run/docker.sock,target=/var/run/docker.sock \
  saronia/jenkins:latest

docker service create --name saronia-jenkins --publish 8000:8080 --network saronia_haproxy --network saronia_jenkins --replicas 1 --mount type=volume,source=jenkins_home,target=/var/jenkins_home,volume-driver=local --mount type=volume,source=jenkins_log,target=/var/log/jenkins,volume-driver=local --mount type=bind,source=/var/run/docker.sock,target=/var/run/docker.sock saronia/jenkins:latest


Debug errors through reading jenkins_log logs
You also have to read this to obtain the Jenkins initial setup key
$ docker run --rm -v jenkins_log:/var/log/jenkins busybox cat /var/log/jenkins/jenkins.log

$ docker service create \
  --name soosap-me \
  --publish 8010:8080 \
  --network saronia_haproxy \
  --network saronia_jenkins \
  --network soosap_me \
  --replicas 2 \
  --env $
  soosap/me:latest

$ docker service create --name soosap-me --publish 8010:8080 --network saronia_haproxy --network saronia_jenkins --network soosap_me --replicas 2 nigelpoulton/pluralsight-docker-ci

---------------------------------------------
Update Services in a Docker Swarm
---------------------------------------------

List all nodes(manager/worker) that are part of the swarm. This command can only be run from a manager.
$ docker node ls

Promote a worker to a manager (optional)
$ docker node ls
$ docker node promote <id>



#### How to get source code into a container?

1. Create a container volume that points to the source code (development)

    A volume is just a special type of directory in a container typically referred to as a *data volume*. It stores its
    containing data on the container's Docker Host. Volumes can be shared and reused among multiple containers.
    Likewise, we could just have a single container associated with one or more volumes.
    Importantly, updates to an image won't affect a data volume.
    Data volumes are persisted even after the container is deleted.
    We could use `$ docker inspect mycontainer` and look under "Mounts" to learn about where on the Docker Host the
    directory is being mounted (or stored).

    > $ docker run -p 8080:3000 node

    > $ docker run -p 8080:3000 -v /var/www

    Moreover, instead of leaving it to the Docker Host to decide, we could explicitly specify that storage location:

    > $ docker run -p 8080:3000 -v $(pwd):/var/www node

    When executing commands on container initialization they will be run from the container's home directory.
    We can change the working directory from where a command shall be executed using the `-w` flag.

    > $ docker run -p 8080:3000 -v $(pwd):/var/www -w "/var/www" node npm start

2. Add your source code into a custom image that is used to create a container (staging/production)

    Build an image from a Dockerfile.

    * FROM
    * MAINTAINER
    * ENV
    * RUN
    * ENTRYPOINT


#### How do containers communicate w/ each other?

1. Legacy linking (old)

    * Simple technique by name referencing
    * Step 1: Run a container with a name
    * Step 2: Link to running container by name
    * Step 3: Repeat for additional container
    <br/>


    Linking **node.js** container and **mongodb** container

    > $ docker build -f node.dockerfile -t danwahlin/node .
    <br/>
    > $ docker run -d --name my-mongodb mongo
    <br/>
    > $ docker run -d -p 3000:3000 --link my-mongodb:mongodb danwahlin/node


    Linking **ASP.NET** container and **postgres** container

    > $ docker build -f aspnetcore.dockerfile -t danwahlin/aspnetcore .
    <br/>
    > $ docker run -d --name my-postgres -e POSTGRES_PASSWORD=password postgres
    <br/>
    > $ docker run -d -p 5000:5000 --link my-postgres:postgres danwahlin/aspnetcore


2. Bridge networking (new)

    * Only containers in a particular bridge can communicate with each other
    * Containers can be grouped into an isolated network so that container communication is restricted

    * Step 1: Create custom bridge network

        > $ docker network create --driver bridge isolated_network_name
        <br/>
        > $ docker network ls
        <br/>
        > $ docker network inspect isolated_network_name

    * Step 2: Run containers in the network (no more --link)

        > $ docker run -d --net=isolated_network_name --name mongodb mongo
        <br/>
        > $ docker run -d --net=isolated_network_name --name nodeapp -p 3000:3000 danwahlin/node


## Docker Compose

#### docker-compose.yml

```yaml
---
version: '2'

services:
    node:
        build:
            context: .
            dockerfile: node.dockerfile
        networks:
            - nodeapp-network
    mongodb:
        image: mongo
        networks:
            - nodeapp-network

networks:
    nodeapp-network:
        driver: bridge
```


* > $ docker-compose build

* > $ docker-compose build mongo

* > $ docker-compose up -d
* > $ docker-compose up --no-deps node

* > $ docker-compose down
* > $ docker-compose down --rmi all --volumes

* > $ docker-compose logs

* > $ docker-compose ps

* > $ docker-compose stop

* > $ docker-compose start

* > $ docker-compose rm



Starting a Jenkins Docker Container
$ eval $(docker-machine env zalia)
$ docker run \
-p 4343:8080 \
-p 50000:50000 \
-d \
--name=jenkins-master \
-v jenkins:/var/jenkins_home \
--env JAVA_OPTS="-Xmx8192m" \
--env JENKINS_OPTS="--handlerCountStartup=100 --handlerCountMax=300" \
jenkins:2.7.4

# JAVA_OPTS environment variable is set to give Jenkins a nice 8GB memory pool and room to handle garbage collection.
# JENKINS_OPTS environment variable is set to give Jenkins a nice base pool of handlers and a cap

Copy the admin code from the logs which the previous commands produces
>> 52e491da3baa45a0a285b3b35e21f1ee

Access Jenkins "Named Volume" contents from the Docker Host:
$ docker-machine ssh zalia
$ sudo -i
$ docker volume ls
$ docker volume inspect jenkins
$ cd /mnt/sda1/var/lib/docker/volumes/jenkins/_data


- Each job that we will set up will have its own separate workspace
