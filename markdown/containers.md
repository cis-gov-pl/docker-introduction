# Containers
![lcx](images/linux-container.jpg)

!SUB
### The challenge
![challenge](images/00_challenge.jpg)

!SUB
### Cargo transportation Pre 1960 ...
![cargo1960](images/02_cargo-pre-1960.jpg)

!SUB
### Solution: Containers
![solution](images/04_solution.jpg)

!SUB
### Docker is a Container System for Code ...
![solution2](images/06_container-system-as-code.jpg)

!SUB
### Use cases
![multitool](images/multi-tool.jpg)

!SUB
### DevOps
![devops](images/devops-cycle.png)

!SUB
### Isolation
![isolation](images/cubicles.jpg)

!SUB
### Service upgrades
![upgrades](images/coffee-van.png)

!SUB
### Testing
* Lightweight
* Fast
* Disposable
  * Run
  * Test
  * Remove

!SUB
### Caveats
![caveat](images/caveat.jpg)

!SUB
### Security
![selinux](images/docker-security.png)

#### Containers do not contain

Dan Walsh (Mr SELinux)

!SUB
### Security

Major kernel subsystems are not namespaced like:
* SELinux
* Cgroups
* file systems under /sys
* /proc/sys, /proc/sysrq-trigger, /proc/irq, /proc/bus

Devices are not namespaced:
* /dev/mem
* /dev/sd\* file system devices
* Kernel Modules

!SUB
### Security
* Drop privileges

  *Docker drops a lot of root capabilities*
* Run as user inside container
* Use SELinux or AppArmor
* Run images only from trusted sources

!SUB
### Docker IO
* Docker supports several storage drivers for the layered FS
  * __AUFS__ - Ubuntu: *slow for big files, not mainline*
  * __Device Mapper__ - Red Hat: *very slow or slow, complex and troublesome*
  * __ZFS__: *not mainline, SELinux??*
  * __Btrfs__: *slow??, no page cache, no SELinux*
  * __Overlayfs__: *fast, very new, not mainline, no SELinux*
* **Use volumes for all data written by the container**

!SUB
### Further reading
* [http://www.projectatomic.io/blog/2015/06/notes-on-fedora-centos-and-docker-storage-drivers](http://www.projectatomic.io/blog/2015/06/notes-on-fedora-centos-and-docker-storage-drivers)
* [https://opensource.com/business/14/7/docker-security-selinux](https://opensource.com/business/14/7/docker-security-selinux)
