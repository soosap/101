# Docker Technologies

## Docker Machine

* > $ docker-machine ls

    Lists all docker machines. The *default* one is the one on your local development laptop.
    
        NAME      ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER    ERRORS
        default   *        virtualbox   Running   tcp://192.168.99.100:2376           v1.10.3  

* > $ docker-machine ip default

    Determines the IP of a docker machine.
    
        192.168.99.100

* > $ docker-machine status

    Provides information about the state of docker-machine. It may be `Running` or `Stopped`. 
    
* > $ docker-machine stop default

* > $ docker-machine start default

    
