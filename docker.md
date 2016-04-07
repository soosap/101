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
    <br><br/>

* > $ docker ps


* > $ docker ps -a


* > $ docker stop mycontainer


* > $ docker rm mycontainer


* > $ docker images


* > $ docker images -a


* > $ docker rmi 32de


### How to get source code into a container?

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
    
    Moreover, instead of leaving it to the Docker Host to decide, we could explicitly specify that storage location. 
    
    > $ docker run -p 8080:3000 -v $(pwd):/var/www node

2. Add your source code into a custom image that is used to create a container (staging/production)

