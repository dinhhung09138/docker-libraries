## Install linux kernal on windows:
Video here: https://www.youtube.com/watch?v=vJ2fJm3CoyE&ab_channel=TechSolutionZ
1. Open powershell by administrator

> Enable-WindowsOptionalFeature -online -FeatureName $("VirtualMachinePlatform","Microsoft-Windows-Subsystem-Linux")

2. Download the Linux kernel update package
https://docs.microsoft.com/en-us/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package

3. Restart windows


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

# Docker Container

List of docker container
> docker ps

List of all docker containers including stopped container
> docker ps -all

Create a new container
> docker run -d <image_name>
> Options:
>> -d: Run container in background and print container ID

Remove a container
> docker rm <container>

To force delete a container
> docker rm -f <container>

To enter inside the running container
> docker exec -it <container> sh (bash is on linux)

Options:
-t: terminal
-i: interactive

Show log of the container
>docker logs <container> or <container>

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
> 
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
>build build -t <image_name>:<version> -f <Dockerfile> --build-arg <arg1>=<value> --build-arg <arg2>=<value>

