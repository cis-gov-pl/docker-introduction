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
  * Docker drops a lot of root capabilities
* Run as user inside container
* Use SELinux or AppArmor
* Run images only from trusted sources

!SUB
### Docker IO

