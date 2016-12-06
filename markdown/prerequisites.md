# Hands on Labs
## Prerequisites

!SUB
### Some hints
* You will find the slides here: [http://cis-gov-pl.github.io/docker-introduction/](http://cis-gov-pl.github.io/docker-introduction/)
- Navigation through the slides is easy, just use your arrow keys. Left and right for going to the previous or next section and up and down for previous or next page. The space bar always give you the next page.
- All code blocks are meant to be executed by your self unless mention otherwise.
- Have fun, take your time, and feel free to ask questions.

!SUB
### Setup
* For the workshop we use an VM instance @ CIŚ cluster:
  * Connection details on paper sheets you recieved
  * Requires SSH client ( [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) on windows )
* Alternatively you can run the handson on your local machine
  * Linux : Install docker, docker-compose, git and your favourite editor
  * VM : Install VirtualBox [VirtualBox](https://www.virtualbox.org/wiki/Downloads)

!SUB
### VirtualBox
* We use a VirtualBox VM to setup a common environment.
* Premade image [docker-tutoprial.img](http://)
* The VirtualBox appliance contains
  * CentOS 7 Desktop
  * Tools: Docker, Docker Compose and Git (among others)
  * User: cis, Password: cis

!SUB
### Docker @ CIŚ
* The default docker network clashes with CIŚ networks
* Change it after the installation

#### Debian
Edit */etc/default/docker*:

```
DOCKER_OPTS="--bip=10.0.254.1/24"
```

Recreate the docker0 bridge:

```
sudo service docker stop
sudo ip link del docker0
sudo service docker start
```

!SUB
#### CentOS

Edit */etc/sysconfig/docker-network*:

```
DOCKER_NETWORK_OPTIONS="--bip=10.0.254.1/24"
```

Recreate the docker0 bridge:

```
sudo systemctl stop docker
sudo ip link del docker0
sudo systemctl start docker
```

