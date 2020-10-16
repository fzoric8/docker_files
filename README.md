# Dockerfile repo

This repository contains all Dockerfiles which are used for software containerization that implies easier 
integration and development. 

## How to build Dockerfile? 

Enter corresponding directory and run following command to build docker image: 
```
docker build -t <image_name>:<image_path> <dockerfile_path> 
```

For concrete example (ros-kinetic with gazebo) run following: 
```
docker build -t ros_gazebo_img:melodic_11
```

## How to create contaier out of the Dockerfile? 

In order to create container from Dockerfile run following commmand:
```
docker run -it --network host --privileged -v /dev:/dev --name <container_name> <img_name>:<tag_name> 
```

Run docker container with GUI support 
```
docker run -it --network host --gpus all --privileged -e DISPLAY=$DISPLAY -v /dev:/dev -v /tmp/.X11-unix:/tmp/.X11-unix \
       --name <container_name> <img_name>:<tag_name> 
```

## How to start existing docker container after stopping it? 
```
docker start -i <container_name> 
```

## How to open running container in new bash? 
```
docker exec -it <container_name> bash 
```

## How to kill, stop, remove container? 

Kill container: 
```
docker kill  <container_name> 
```

Stop container: 
```
docker  stop <container_name> 
```

Remove container: 
```
docker rm <container_name> 
```

Attach existing detached cotainer: 
```
docker attach <container_name> 
```

Clean dangling images and stopped containers: 
```
docker system prune 
```

### How to create configuration file that runs multiple docker files at once? 

In order to run multiple docker containers which are build from docker images in certain configuration 
use [docker-compose](https://docs.docker.com/compose/) 




