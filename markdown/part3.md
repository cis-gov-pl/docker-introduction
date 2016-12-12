# Hands On Lab 3
![networking](images/turtles.jpg)
## Networking

!SUB
### Food Trucks
* A simple app to search for food stands in San Francisco
* The app's backend is written in Python ([Flask](http://flask.pocoo.org/))
* For search it uses [Elasticsearch](https://www.elastic.co/)
* Let's try and dockerize it into two containers
  * One container for Elasticsearch
  * One for python backend
  * Containers connected via network

!SUB
### Docker networking topology
![test](images/docker-network-topology.png)
- __none__, no networking
- __bridge__, each container has is own
- __shared__, containers share a single network stack
- __hosts__, use the host networking
- __user defined__, user defined network

!SUB
### Docker networking topology by Example

```
# List docker networks
docker network ls

# Run a container with no network
docker run --rm --net none busybox:latest ifconfig

# Run a container in a bridged network (using default docker0 bridge)
docker run --rm --net bridge busybox:latest ifconfig

# or (bridge is the default)
docker run --rm busybox:latest ifconfig

# joined
docker run --name joined1 -d --net none busybox:latest \
  nc -l 127.0.0.1:3333
docker run --rm -it --net container:joined1 busybox:latest netstat -al

# host
docker run --rm --net host busybox:latest ifconfig
```

!SUB
### Container linking
- Docker has a linking system that allows you to link multiple containers together and send connection information from one to another.

```
docker run --link <container_name>:<hostname_alias>
```
- This feature will be __deprecated__ in future docker versions.
- Use __`docker network`__ instead
  - Provides proper DNS
  - Allows running containers with absent links

!SUB
### User defined networks
- You can create your own user-defined networks that better isolate containers
- You can create multiple networks
- You can add containers to more than one network
- Containers can only communicate within networks but not across networks
- A container attached to two networks can communicate with member containers in either network

!SUB
### User defined networks
* Let's go ahead and create our own network

```
docker network create --subnet=10.0.100.0/24 foodtrucks
docker network ls
```
* Let's move our running flask web app to the `foodtrucks` network

```
docker network connect foodtrucks food-trucks
docker network disconnect bridge food-trucks
docker network inspect foodtrucks
docker network inspect bridge
docker exec food-trucks ip addr
```

!SUB
### Elasticsearch
- Let's see if we can find pre made Elasticsearch image on the hub

```
docker search elasticsearch
```
- There exists an officially supported [image](https://hub.docker.com/_/elasticsearch/) for Elasticsearch
- Let's run it with default settings attached to our `foodtrucks` network

```
docker run -d --net foodtrucks --name es elasticsearch
docker exec food-trucks dig es
docker exec food-trucks curl es:9200
```
- Let's restart food-trucks container to initialize the ES database

```
docker restart food-trucks
```
- Our web app should be fully operational

```
firefox http://<hostname>:5000
```

!SUB
### Food Trucks
![food_trucks](images/food-trucks.png)

!SUB
![selinux_vs_youtube](images/selinux_vs_youtube.png)

!SUB
### SELinux vs YouTube
- Let's think how a YouToube like app would work on one machine in docker containers
- We consider only upload, transcode and download of videos/images

![youtube_diagram](images/youtube_diagram.png)

!SUB
### SELinux and host folder sharing
- Create a shared host folder

```
mkdir /opt/shared
echo HELLO > /opt/shared/hello.txt
```
- Access it from a container

```
docker run --rm -it -v /opt/shared:/data busybox
cd /data
cat hello.txt
echo HELLO2 >> hello.txt
echo HELLO3 > hello3.txt
```

- [Second Terminal]

```
ls -Z /opt
```

- [Container]

```
exit
```

!SUB
### SELinux and write access
- Let's try again
  - Notice the `:Z` parameter for -v option

```
docker run --rm -it -v /opt/shared:/data:Z busybox
cd data
echo HELLO2 >> hello.txt
echo HELLO3 > hello3.txt
```

- [Second Terminal]

```
ls -Z /opt
```

- [Container]

```
exit
```

!SUB
### SELinux and shared access for multiple containers
- This time let's create several containers
  - Notice `:z` parameter instead of `:Z`

```
docker run -d --name selinux1 -v /opt/shared:/data:z busybox
docker run -d --name selinux2 -v /opt/shared:/data:z busybox
docker run -d --name selinux3 -v /opt/shared:/data:z busybox
ls -Z /opt
```
- Let's check if that works

```
docker exec -it selinux3 /bin/sh
cd data
echo HELLO2 >> hello.txt
echo HELLO3 > hello3.txt
exit
```
- Stop the containers:

```
docker stop selinux1 selinux2 selinux3
```

!SUB
### Cleanup containers
* List active and inactive containers

```
docker ps
docker ps -a
```
* Remove containers filtering by name

```
docker rm $(docker ps -a -q -f "name=.*data")
```
* Remove stopped containers

```
docker rm $(docker ps -a -q -f exited=0)
```
* Remove all containers (forcing stop for running ones)

```
docker rm -f $(docker ps -a -q)
```

!SUB
### Cleanup images and volumes
* List images and volumes

```
docker images
docker volume ls
```
* **The volume of the `nginx-data` container still exists!!**
* Remove orphaned images and volumes

```
docker rmi $(docker images -q -f dangling=true)
docker volume rm $(docker volume ls -q -f dangling=true)
```
