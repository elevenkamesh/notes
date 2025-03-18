## Commands
- Image Commands  
    `docker image`: lists out all the commands for images  
    `docker images or docker image ls`: gives all the images present on machine  
    `docker image pull <image_name>`: pull image from dockerhub  
    `docker image rm <image_id>`: removes the image from machine  
    `docker inspect <image_id>`: gives metadata and other info related to the image  
    `docker image prune -a`: removes all unused images
- Container Commands  
    `docker container`: lists out all the commands for containers  
    `docker container ls`: gives all the running containers on machine  
    `docker container ls --all`: gives all the containers on machine  
    `docker run --name <name_for_container> <image_name>`: run a container with your own name  
    `docker run <image_name> <command_executed_on_start_eg_ls/bash>`: run a container with the specified command  
    `docker container prune`: removes all the inactive containers  
    `docker run -it <image_name>`: runs a container in interactive mode  
    `docker run -it -p <localport:dockerport> <image_name>`: run the container with port mapping  
    `docker run -it -P <image_name>`: automatically maps all the exposed ports  
    `docker run -d <image_name>`: runs a container in detached mode  
    `docker exec <container_name> <command>`: run the specified command in mentioned container  
    `docker stop <container_name>`: stops a container  
    `docker kill <container_name>`: force stops a container  
    `docker container rm <container_name>`: removes a container