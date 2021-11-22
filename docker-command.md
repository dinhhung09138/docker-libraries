## Install linux kernal on windows:
Video here: https://www.youtube.com/watch?v=vJ2fJm3CoyE&ab_channel=TechSolutionZ
1. Open powershell by administrator

> Enable-WindowsOptionalFeature -online -FeatureName $("VirtualMachinePlatform","Microsoft-Windows-Subsystem-Linux")

2. Download the Linux kernel update package
https://docs.microsoft.com/en-us/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package

3. Restart windows

# Docker best practices
1. One service per container
2. Build context should be small
3. Avoid unnecessary packages
4. Less layers

# Docker common commands

Command-line libraries:
https://docs.docker.com/engine/reference/commandline/image_ls/

Check docker version installed on the current OS
> docker --version

# Docker Images

List of docker images
> docker image ls
> OR
> docker images

Remove an image
> docker image rm <image_name>:<version>
> Or
> docker rmi <image_name>:<version>

**Dangling image**
That are images without name or tag when you show list of images command.
It happens when you create a duplicate image name. The latest image built if the last version, and other ones are became dangling images.
To avoid dangling image, **Please use tag when build an image**

Filter dangling images
> docker images -f dangling=true
> -f = filter.

Only show image id
> docker image -q
> -q: 

To remove every dangling images
> docker rmi $(docker images -f dangling=true -q)



# Docker Container

Show help
> docker run --help

List of docker container
> docker ps
> docker container ls

List of all docker containers including stopped container
> docker ps -all

To show the last created container
> docker ps -l

Create a new container. If you dont assign the name for the container, It will randomly the name for it
> docker run -d <image_name>
> Options:
>> -d: Run container in background and print container ID

Rename a container
> docker rename <current_name> <new_name>

Remove a container
> docker rm <container>

To force delete a container
> docker rm -f <container>
> -f: force

To enter inside the running container
> docker exec -it <container> bash (sh is on linux)

Options:
-t: terminal
-i: interactive

Show log of the container
>docker logs <container> or <container>

Map port from localhost to port in container
> docker run -d -p <localhost_post>:<container_port> <image_id>
> -p: port

To build a container with variables
> docker run -d -e "variable=value" -e "var2=value2 <image_name>
> -e: environment variable

To take a look at the configuration of a container
> docker inspect <container_id>

To returns a live data stream for running containers 
> docker stats
or
> docker stats <container_id>

To limit container uses memory, we use --memory options
> docker run -d --name nginx nginx:latest --memory "200mb"

To limit CPUs use when create docker container, In case your computer has move than 1 CPU available.
> docker run -d --name nginx nginx:latest --cpuset_cpus 0
> option
> 0-3: it means use from CPU 0 to CPU 3
> 0,3: it means use CPU 0 and CPU 3

Copy files from your host to your container.
> docker cp <container_name>:<host_file_path> <container_destination_path>

Destroy the containers automatically after exit. In case you want to view inside the image. For example, you check unzip files, file downloaded from the internet,... You will create a temporary container
> docker run --rm -d nginx:alpine bash



Create mysql container
> docker run -d --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -e MYSQL_DATABASE=DockerDb -e MYSQL_USER=docker -e MYSQL_PASSWORD=123456 mysql:latest
> MYSQL_ROOT_PASSWORD: Set password for root user
> MYSQL_DATABASE: the database created after container run
> MYSQL_USER: Adding new user login to mysql
> MYSQL_PASSWORD: Set password to new user
> -d: detatch, run the container in the background and print out container id
> --name: set container name
> -e: environment variables, adding container variables
> -p: port, mapping port

Create postgres container
> docker run -d --name mysql -p 5432:5432 -e POSTGRES_DB=DockerDb -e POSTGRES_USER=docker -e POSTGRESS_PASSWORD=123456 postgres:latest
> POSTGRES_DB: Intilial database name after container created
> POSTGRES_USER: User name
> POSTGRES_PASSWORD: Password
> -d: detatch, run the container in the background and print out container id
> --name: set container name
> -e: environment variables, adding container variables
> -p: port, mapping port

Create mongo docker 
> docker run -d --name mongo -v mongo:/data/db -p 27018:27017 mongo:latest

# Docker file

Download a file from the internet. If we dont specific the destination file name, it will use the default name from source file.
> ADD <file_source_path> <file_destination_path> 

example for downloading zip and unzip file.
> ADD http://github.com/dinhhung09138/code.zip /usr/code.zip
> RUN cd /urs && unzip code.zip <file unzip name - the file(folder inside the zip file)>/* /src<destination path>
> 

Create variable in Dockerfile using ENV
> ENV HTML html_value

using the variable
> $HTML

To specify the working directory folder by using the WKDIR
> WKDIR <folder_path>

Adding meta data inside Dockerfile by using LABEL
Label is an instruction inside the Dockerfile, which is not a command
> LABEL author=hung.tran
> LABEL company=sts

Default, the user working in the container is root user. To create a new user and handle user working in Dockerfile
For some example, we want to change user working in the container and create new file or delete files with the created folder.
> RUN useradd <user_name> //Create new user
> USER <user_name> // Switch to created user

Create a new user and change the ownership of the folders or file (user_name:user_group_name)
> RUN useradd <user_name> && chown <user_name>:<user_name> . - R

Using ARGs to build an image dynamically

1. In the Dockerfile
   > ARG user=application
2. Using argument
   > RUN useradd $user
3. build an image
   > docker build -t <image_name>:<version> -f <Dockerfile> --build-arg user=hungtran .

build with multiple argument
> build build -t <image_name>:<version> -f <Dockerfile> --build-arg <arg1>=<value> --build-arg <arg2>=<value>

How to ignore files or folders in the context when build a docker image. You should create the .dockerignore file,
When the Docker cli build an image, it will lock for .dockerignore to check does has any ignore in the process
1. Create a .dockerignore

Example
> #.dockerignore file
> 
> <folder_name>
> <file_name>
> <file_path>

# Docker volumes
for example your container died, you find a way to store your container data into your local machine.
To find a folder the container data install and use. Please check the image information on docker hub

**Bind volume**
This type of volume doest not manage by Docker. It use to map the data from container to the host directly
To map the folder inside the container to the host. The syntax when create a container. Use absolute path for the host directory
> docker run -d -v <host_path>:<container_path> <image_name>

To map back the data inside the host folder to the container. Please create the command the same when create new container. It will map automatically.
> docker run -d -v <host_path>:<container_path> <image_name>

Create mysql container with volume
> docker -d --name mysql -e MYSQL_ROOT_PASSWORD=123456 -v <host_path>:/var/lib/mysql mysql:latest

**volume**
Manage by docker and we can create new volumes by docker command.
By default, the command create a new folder inside the root directory with the name you provided
> docker volume create <create_name>

Create mysql container with volume
> docker -d --name mysql -e MYSQL_ROOT_PASSWORD=123456 -v <volume_name>:/var/lib/mysql mysql:latest

To show list of volumes
> docker volume ls

To map the docker volumn with the path of the container when it create,  please change the path to the volume name.
> docker run -v <volume_name>:<container_path> <image_name>

**Anonymous volume**
Docker provide a random folder where you can find your information. It is temporary folder so when you delete a container, the volume will be deleted without noticing.

Delete volume
> docker volume rm <id_1> <id_2> <id_3> <id_...>

Create mysql container with anonymous volume
> docker -d --name mysql -e MYSQL_ROOT_PASSWORD=123456 -v /var/lib/mysql mysql:latest

To remove the anonymous volume created when start a new container. Please use -v option
> docker rm -f -v <container_id>

**The different between volume and anonymous volume**
1. The name is easy to understand
2. Anonymous volume will be deleted when delete the container with **-v** option
3. Volume folder still there when delete the container with **-v** option

**When we use docker, We should not use anonymous volume**

Using inspect command to check the docker volume
> docker inspect <container_id>
> View "Amounts" key

To find a root folder from docker
> docker info | findstr -i root (command in windows)

**Dangling volumes**
These volumes are not associated with any container. To figure out dangling volume
> docker volume ls -f dangling=true

To remove all dangling volumes
> docker volume rm $(docker volume ls -f dangling=true -q)

**Share docker volumes**
We can create **bind-volume** or **volume** and can map with many containers

# Docker Network
**Bridge Network** is the default network, When we create a new container, it will auto assign bridge network to that container.

To show all network in docker
> docker network ls

To show network details
> docker network inspect <network_name>
> docker network inspect bridge

To view network of the container, run the command bellow and view the "Networks" node.
> docker inspect <container_id>

To view all containers are using network. Run the command bellow and view "Containers" node
> docker network inspect <network_name>

To ping from the container a to container b
> docker exec sh -c "pi

To create a new newwork
> docker network create -d <driver> --subnet 172.18.0.0/16 --gateway 172.18.0.1 <network_name>
> <driver>: default is bridge

To connect a network with a container in another network.
It will add container inside the network with new IP address.
> docker network connect <network_name> <container_name>

To disconnect the network
> docker network disconnect <network_name> <container_name>

To remove the custom network
> docker network rm <network_name>

To assign IP address to a container. You can assign an IP with your network created, cannot assign to default network
> docker run -dti --name <container_name> --network <network_name> --ip 172.19.0.10 <image>

**Host** network
When you create a container using host network, That means it share the network attributes of the docker host (current machine).

**None** network
When we create a new container using none network, It will create a container without any ipaddress or gateway.

# Docker compose

- To combine multiple commands into a files
- We can add add more services, or adding more container
- We can manage more easyer.

**docker-compose.yml** is the default name

The contain of the docker-compose file:
There are 4 main section inside the docker compose file.
**version**
This is the section to specific the version of the docker.
Please use access this [**link**](https://docs.docker.com/compose/compose-file/) to check the version you want.
**services**
This is the section where you can setup your services and build a image, container,
Please access to this [**link**](https://docs.docker.com/compose/compose-file/compose-file-v3/) to see some examples
> #This is the mandatory
>
> version: '3'
>
> #This is the mandatory
>
> services:
>
> #This is the optional
>
> volumes:
>
> #This is the optional
>
> networks:


**To use the docker-compose. We should navigate to the current directory you are using the docker-compose file.**

To build a container from docker-compose file. By default, Docker compose will read the default file **docker-compose.yml** at the current directory
> docker-compose up -d

To specify the docker compose file
> docker-compose -f <file_name>.yml up/down

To remove docker compose
> docker-compose down 


# Basic Linux commands


Read the file, writing them to standard output
> cat <file_name>

> grep <keyword>

Searching plain-text data sets for lines that match a regular expression.

List computer fiels in Unix and Unix-like operating systems
> ls

Show all folders and files size
> ls -sh

Show list files and folder with permission
> ls -l

Create new dictionary
> mkdir

Show all files and folder 
> free -h

Show number of CPUs available
> grep "model name /proc/cpuinfo wc -l

To read a file without using -it
> docker exec <container_name> bash -c "command"

