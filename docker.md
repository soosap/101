# Docker Technologies

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
    

## Docker Client

* > $ docker run -d -p 3000:80 -v /var/www --name mycontainer kitematic/hello-world

    The `docker run` command spins off a running container out of a Docker Image.
    * The `-p` flag exposes port 80 on the **Docker Container** to port 3000 on the **Docker Machine**
    * The `-d` flag runs the container in detached mode
    * The `-v` flag specifies a volume
    * The `--name` flag labels the container
    <br/><br/>

* > $ docker ps


* > $ docker ps -a


* > $ docker stop mycontainer


* > $ docker rm mycontainer


* > $ docker images


* > $ docker images -a


* > $ docker rmi 32de


* > $ docker build -t saronia/saronia-frontend -f Dockerfile.nginx --no-cache .

    * The `-t` flag specifies tags for the image build
    * The `-f` flag specifies a non-standard Dockerfile path and/or name
    * The `--no-cache` would build the container from scratch w/o leveraging the cache
    * The `.` represents the build context
    <br/><br/>


* > $ docker login -e soosap@saronia.io -p secret -u username


* > $ docker push saronia/saronia-frontend:latest


* > $ docker pull saronia/saronia-frontend:latest


* > $ docker exec d6er4 node dbSeeder.js

    Execute a command on a running container



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





```


* > $ docker-compose build

* > $ docker-compose up -d


## Docker Swarm

