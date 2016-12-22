# Hands on Labs
## Prerequisites

!SUB
### Some hints
* You will find the slides here: [http://cis-ncbj.github.io/docker-introduction/](http://cis-ncbj.github.io/docker-introduction/)
- Navigation through the slides is easy, just use your arrow keys. Left and right for going to the previous or next section and up and down for previous or next page. The space bar always give you the next page.
- All code blocks are meant to be executed by your self unless mention otherwise.
- Have fun, take your time, and feel free to ask questions.

!SUB
### Setup
* For the workshop we use an VM instance @ CIS cluster:
  * Connection details on paper sheets you recieved
  * Requires SSH client ( [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) on windows )
* Alternatively you can run the handson on your local machine in VirtualBox
  * Requires VirtualBox [VirtualBox](https://www.virtualbox.org/wiki/Downloads) installation
* Last possibility is to run docker locally on linux machine
  * The titorial was tested with docker 1.10
  * Be aware that part of the tutorial might behave differently
  * Install docker, docker-compose, git and your favourite editor
  * Run as root or add your user to the `docker` group

!SUB
### VirtualBox
* We use a VirtualBox VM to setup a common environment.
* Premade image [https://goo.gl/7zzSos](https://goo.gl/7zzSos)
* The VirtualBox appliance contains
  * CentOS 7 Desktop
  * Tools: Docker, Docker Compose and Git (among others)
  * User: cis, Password: cis

!SUB
### Host setup for elasticsearch
Elasticsearch which will be used in the handson has large memory requirements
* Make sure your VM has at least 4GB of RAM
* Increase the hosts mmap count limit:
  * https://www.elastic.co/guide/en/elasticsearch/reference/2.1/setup-configuration.html#vm-max-map-count

```
sysctl -w vm.max_map_count=262144
```

__*Already set for tutorial VMs*__

!SUB
### Docker @ CIS
* The default docker network clashes with CIS networks
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

__*Already set for tutorial VMs*__

!SUB
#### Debian/Ubuntu with upstream docker
For Debian 8 and later and Ubuntu 15.04 and later the `/etc/default/docker` is ignored when installing directly from docker.com

Create */etc/systemd/system/docker.service.d/network.conf*:

```
[Service]
ExecStart=
ExecStart=/usr/bin/docker daemon -H fd:// --bip=10.0.245.1/24
```

Recreate the docker0 bridge:

```
sudo systemctl daemon-reload
sudo systemctl stop docker
sudo ip link del docker0
sudo systemctl start docker
```

__*Already set for tutorial VMs*__

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

__*Already set for tutorial VMs*__

!SUB
### Login to tutorial VMs

#### VirtualBox
* Import DockerTutorial.ova
* Start DockerTutorial virtual machine
* Login: cis
* Password: cis
* Open terminal

#### Remote VMs
* Login to your VM using ssh
* Hostname and Password are on the paper sheet

```
ssh root@<hostname>
```

!SUB
### Prepare tutorial materials
* Open the tutorial slides (for copy & paste)

[http://cis-ncbj.github.io/docker-introduction/](http://cis-ncbj.github.io/docker-introduction/)
* Download the tutorial materials
```
cd /opt
git clone https://github.com/cis-ncbj/docker-introduction-data
```

#### VirtualBox
- Execute in terminal
```
yum -y install htop time iptables-services && systemctl enable iptables && systemctl start iptables && systemctl restart docker
```
