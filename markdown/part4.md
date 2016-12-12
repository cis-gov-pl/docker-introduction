# Hands On Lab 4
![docker-advanced](images/docker-advanced.jpg)
## Advanced Docker

!SUB
### Docker Compose
[Compose](https://docs.docker.com/compose/) is a tool for defining and running multi-container Docker applications

![docker-compose](images/docker-compose-large.png)

!SUB
### Docker Compose Food Trucks
```
version: "2"
services:
  es:
    image: elasticsearch:2.3
    container_name: es
    networks:
      - foodtrucks
  web:
    build: .
    command: python app.py
    ports:
      - "5000:5000"
    volumes:
      - .:/code
    networks:
      - foodtrucks
    depends_on:
      - es
networks:
  foodtrucks:
    ipam:
      config:
        - subnet: 10.0.2.0/24
```

!SUB
### Run
- Build

```
cd /opt/docker-introduction-data/food-trucks
docker-compose build
```
- Start

```
docker-compose up -d
```

!SUB
### Container updates
![updates](images/update.png)

!SUB
### Typical container ...

Let's have a look at a typical container:

[Mongo](https://hub.docker.com/_/mongo/)

Let's have a look at another typical container:

[Redis](https://hub.docker.com/_/redis/)

!SUB
### Updates
| Component            | Update            |
|----------------------|-------------------|
| Kernel               | Host              |
| Userspace libraries  | Image / Container |
| Application from deb | Image / Container |
| Application from tar | Image             |

!SUB
### Update deadtime
Now we'll check the update deadtime

```
docker pull mongo:3.2
docker run -d --name update_test mongo:3.2
docker pull mongo:3.4
/usr/bin/time bash -c "docker rm -f update_test && \
  docker run -d --name update_test mongo:3.4"
```

!SUB
### Resource Limits
![limits](images/docker-limits.jpg)

!SUB
### Memory limits
- Docker container limits are based on cgroups: [Docs](https://docs.docker.com/engine/reference/run/#/runtime-constraints-on-resources)
- We can specify the memory and swap limit. (Default: swap=memory)
  - This container can use 300M memory and 700M swap memory
  - From inside container it is hard to get the limit values

```
docker run -it -m 300M --memory-swap 1G --name mem_test1 ubuntu /bin/bash
free -h
```
- [Second terminal]

```
cat /sys/fs/cgroup/memory/system.slice/docker-$(docker inspect -f '{{.Id}}' mem_test1).scope/memory.limit_in_bytes
```
- [Container]

```
exit
```

!SUB
### Memory limits
- Let's test if that works:

```
cd /opt/docker-introduction-data/stress
docker build --tag local/stress .
docker run --rm -m 128m local/stress --vm 1 --vm-bytes 312M --vm-hang 0
```

!SUB
### CPU limits
| option        | value                | result                                                |
|---------------|----------------------|-------------------------------------------------------|
| --cpu-shares  | 1-1204               | relative process weight used for CPU share allocation |
| --cpuset-cpus | core_ids: (0-1, 0,3) | CPU cores in which to allow execution                 |
| --cpuset-mems | mem_ids: (0-1, 0,3)  | Memory nodes in which to allow execution              |

#### Test

```
docker run -d --name cpu_test1 --cpuset-cpus=0 local/stress --cpu 1
docker run -d --name cpu_test2 --cpuset-cpus=1,2 \
  --cpu-shares 1024 local/stress --cpu 2
docker run -d --name cpu_test3 --cpuset-cpus=1,2 \
  --cpu-shares 512 local/stress --cpu 2
htop
docker stop cpu_test1 cpu_test2 cpu_test3
```

!SUB
### Firewall
![firewall](images/firewall.jpg)

!SUB
### Firewall
- Let's inspect the iptable rules

```
iptables -L -n -v
```
- Can you notice what is wrong with the INPUT chain?

!SUB
### Firewall
#### How do we restrict access to services running in docker?

- Put a firewall in front of docker host
- Insert PRE_DOCKER rules: [PRE_DOCKER](http://rudijs.github.io/2015-07/docker-restricting-container-access-with-iptables)

