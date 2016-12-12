# Hands on Lab 1
![helloworld](images/helloworld.png)
## Hello world

!SUB
### Some notes
* All steps assume you have a command line open.
  * Almost all steps formatted as code can be executed.
    *  `\` is a line break and can be copied and pasted.
    * `< ... > ` indicates you should replace the value

!SUB
### Docker basics
* Below some of the basic docker commands

```
docker help

# Partial output
Commands:
    images    List images
    logs      Fetch the logs of a container
    ps        List containers
    pull      Pull an image or a repository from a Docker registry
    rm        Remove one or more containers
    rmi       Remove one or more images
    run       Run a command in a new container
    start     Start a stopped container
    stop      Stop a running container

Run 'docker help' for all commands.
Run 'docker COMMAND --help' for more information on a command.
```

!SUB
### Pull an image
* First we pull a base image. We use ubuntu 14.04 latest as base. See [Ubuntu repo on docker registry](https://registry.hub.docker.com/_/ubuntu/)

```
docker pull ubuntu
```
* Now we have the image of ubuntu in our local repository, verify with the command:

```
docker images
```

!SUB
![Docker Hub](images/docker-hub.png)

!SUB
### Start a docker container
* Time for hello world.
* With the next command you start an ubuntu container and execute the command to echo some famous string.

```
docker run ubuntu echo "hello world"
```
* Running the command above creates, starts and exits the ubuntu container.
- Observe the output with commands below, remember you can get help by executing `docker help` or `docker help ps`

```
docker ps
docker ps -a
```
- Remove the container

```
docker rm <id or name>
```

!SUB
### Kernel & Root user
* The container uses the kernel of the *host*

```
docker run --rm ubuntu uname -a
uname -a
```
* Most of the time application in the container runs as root
* However it will not have all capabilities of system administrator

```
docker run --rm ubuntu capsh --print | grep Current
sudo capsh --print | grep Current
```

!SUB
### Execute docker container interactively
* Start a container with an interactive shell

```
docker run --name nginx-base -it -e TERM=xterm ubuntu
```
* Install a text editor inside th container

```
apt-get install vim nano
```
* ??? No vim or nano in ubuntu repositories ???

```
apt-get update
apt-get install vim nano
```
* Lets install nginx and edit simple web page

```
apt-get install nginx
ln -sf /dev/stdout /var/log/nginx/access.log
ln -sf /dev/stderr /var/log/nginx/error.log
vim /var/www/html/index.html
exit # Finish when satisfied with the results
```

!SUB
### Data persistency

* Changes made in a container are persisted only in that container. At the moment the container is destroyed the change are lost too.
* There are three ways to obtain data persistence:
  * Commit the changes made in a container to an image, persists the change.
  * Store persistent files in host directory mounted inside the container
  * Store persistent files in data container

!SUB
### Create a docker image
* Image naming conventions
  * `<name>:<tag>` - official docker containers
  * `<user>/<name>:<tag>` - public images @ [https://hub.docker.com](https://hub.docker.com)
  * `local/<name>:<tag>` - local images (stored in local image cache)
* Commit your changes in the container to an (new) image.

```
docker commit nginx-base local/nginx
```
* Inspect your changes.

```
docker images                     # list images stored in local cache
docker history local/nginx        # shows the image history
docker diff nginx-base            # shows the added files
```
* Remove the container.

```
docker rm nginx-base
```

!SUB
### Run nginx using your image
* Start a new container in daemon mode
  * Publish port 80 to access the web server
  * Run nginx with logging set to stdout/stderr

```
docker run -d --name nginx1 -p 80:80 local/nginx nginx -g "daemon off;"
docker ps
```
* Access your web page
  * Cluster VMs: `<hostname> -> docker<NR>.cis.gov.pl`
  * VirtualBox: `<hostname> -> localhost`

```
firefox http://<hostname> &
```
* Inspect the logging

```
docker logs nginx1
```
* Check `docker help logs for list of very useful options

!SUB
### Interactive access to running container
* Use docker exec to access a running container

```
docker exec -it nginx1 bash
vim /var/www/html/index.html
exit
```

* Copy files from and to the container

```
docker cp nginx1:/var/www/html/index.html /tmp
cat /tmp/index.html
vim /tmp/index.html
docker cp /tmp/index.html nginx1:/var/www/html
```

!SUB
### Host directories as volumes
![Host folder](images/docker-host-folder.jpg)
http://www.slideshare.net/durdn/be-a-better-developer-with-docker

!SUB
### Host directories as volumes
* Run new nginx instance using host directory as a volume
  * Use a different name
  * Publish port 80 as port 888 this time

```
docker run -d -v /opt/docker-introduction-data/static-site:/var/www/html \
    --name nginx3 -p 888:80 local/nginx nginx -g "daemon off;"
```
* Access your web page

```
firefox http://<hostname>:888 &
```

!SUB
### Data containers
![Data Container](images/docker-data-container.jpg)
http://www.slideshare.net/durdn/be-a-better-developer-with-docker

!SUB
### Data containers
* Create data only container and put inside your web page

```
docker create -v /var/www/html --name nginx-data ubuntu /bin/true
docker cp /tmp/index.html nginx-data:/var/www/html
```
* Run new nginx instance using volume from the data container
  * Use a different name
  * Publish port 80 as port 88 this time

```
docker run -d --volumes-from nginx-data --name nginx2 -p 88:80 \
    local/nginx nginx -g "daemon off;"
```
* Access your web page

```
firefox http://<hostname>:88 &
```
* To edit files in data container we need a runing container

```
docker run -it --rm --volumes-from nginx-data -e TERM=xterm local/nginx
vim /var/www/html/index.html
```

