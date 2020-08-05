**Nomenclature**

| Name  | Purpose |
| ----- | --------|
| Image | Pretty much your base OS |
|Container | Packaged up software |
|Repository | Image store |
|Layer | Files generated from executing a command to create a container|

P.S. An image and container are different. Example: We can dowload the elastic stack. The website says it is
based off CentOS. In docker we dowload the centos image once and all the containers can reference that image
to do their own runtime functions.
 


**Important Commands**

| Command | Options | Other | Purpose |
| ------- | :-----: | :---: | ------- |
|docker pull| | jenkins/jenkins:lts |Pulls down an image|
|docker stop| | jenkins/jenkins:lts |Stops a container through SIGTERM (do this first)|
|docker kill| | jenkins/jenkins:lts |Stops a container through SIGKILL (if container still doesn't stop do this after)|
|docker ps  | |                     |Lists the running containers |
|docker ps  |-a|                    |Lists all containers (running and not running)| 
|docker rm  |-f|$(docker ps -aq)    |Deletes all running and stopped containers|
|docker rm  |  | jenkins            |Deletes a single container|
|docker logs|  | jenkins            |Shows all the logs for a container by name|  
|docker logs|--tail 10|jenkins      |Returns the last 10 lines of the logs|
|docker exec| -it| jenkins /bin/bash|This will get you a shell inside of the container that is already running (not image)|
|docker run | | jenkins/jenkins:lts | This will run a container with a random name based off the jenkins image
| |      --rm | |remove container automatically after it exits|
| |      -it  | jenkins/jenkins:lts /bin/bash |connect the container to terminal (ssh)|
| |      --name| jenkins            | gives the container a name|
| |      -v | ${hostpath}:${containerpath}  | Creates a host mapped volume inside the container. All container data is immutable so it can't be changed. You will have to create the same directory structure on your local machine or map files1 to 1. Example, if you have a logstash.yml on your host you can make to the logstash.yml on the container with~/path/to/yml:/usr/share/logstash/config/logstash.yml.| 
| |      -p | 5000:80               | This exposes port 5000 on your local machine and maps to port 80 on the container|
| |      -e |                       |Pass environment variables|
| |      -d |                       |Start the container in detached mode|
|docker network| create|            |Creates a network for the docker containers| 
| |      --subnet 10.1.0.0/24|      |Creates the subnet for the containers|
| |      --gateway 10.1.0.1|        |Defines the default gateway
| |      -d |bridge                 |Defines the network driver to use. Bridge is default but overlay and macvlan. More information [here](https://blog.docker.com/2016/12/understanding-docker-networking-drivers-use-cases/)|
      

Note: Not all flags after `docker run` is necessary. They are optional but those are some of the things you can do to run a container