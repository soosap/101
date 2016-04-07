# Docker Technologies

## Docker Machine

* List all docker machines. The *default* one is the one on your local development laptop.

    > $ docker-machine ls
    
        NAME      ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER    ERRORS
        default   *        virtualbox   Running   tcp://192.168.99.100:2376           v1.10.3  

> $ docker-machine stop default


> $ docker-machine start default


* Determine the ip of a docker machine.
> $ docker-machine ip default
    
    192.168.99.100

    
    

