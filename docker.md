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
    
* > $ docker ps

* > $ docker ps -a

* > $ docker run -d -p 3000:80 kitematic/hello-world

    The `docker run` command spins off a running container out of a Docker Image.
    * The `-p` flag exposes port 80 on the Docker Container to port 3000 on the Docker Machine
    * The `-d` flag runs the container in detached mode
