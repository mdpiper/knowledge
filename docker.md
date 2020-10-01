# Docker

Containers are cool!


## Install Docker Desktop for Mac

[Docker Desktop for Mac][docker-desktop] is a native application
installed to **/Applications**.
It creates symlinks in **/usr/local/bin**
for `docker`, `docker-compose`, and others.
Docker Desktop for Mac uses [HyperKit][hyperkit] instead of VirtualBox.

Does my laptop support [Hypervisor][hypervisor]? Check:
```
$ sysctl kern.hv_support
kern.hv_support: 1
```

Followed the Docker Desktop [install instructions][install].
I had to sign in with my Docker ID (I'd made one some time ago)
to download the installer.
Had to sign in again to the Docker app once it was installed.

Check the install from a terminal
(recall it makes links that should already be in `$PATH)`:
```
$ docker --version
Docker version 18.09.2, build 6247962
$ docker-compose --version
docker-compose version 1.23.2, build 1110ad01
$ docker-machine --version
docker-machine version 0.16.1, build cce350d7
```

Ran the Docker hello-world image successfully:
```
$ docker run hello-world
```

There's more at https://docs.docker.com/docker-for-mac/.


## Ubuntu

Run the Ubuntu bash image (which is very cool) with:
```
$ docker run -it ubuntu bash
root@a509b8f276d4:/# uname -a
Linux a509b8f276d4 4.9.125-linuxkit #1 SMP Fri Sep 7 08:20:28 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
```

It drops you in **/** as root with no users defined.

Use `apt-get` to install packages.
First, update package information:
```
$ apt-get update
```
then install any needed packages; e.g.:
```
apt-get install wget
```


## CentOS

Similarly, run the CentOS image with:
```
$ docker run -it centos

```

## Miniconda

Install an image preloaded with a Miniconda distribution
in */usr/local*:
```
docker pull conda/miniconda3
docker run -i -t conda/miniconda3 /bin/bash
```

The OS is Linux/Debian:
```
$ cat /etc/os-release
PRETTY_NAME="Debian GNU/Linux 9 (stretch)"
NAME="Debian GNU/Linux"
VERSION_ID="9"
VERSION="9 (stretch)"
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"
```

<!-- Links -->

[docker-desktop]: https://docs.docker.com/docker-for-mac/
[hyperkit]: https://github.com/docker/HyperKit/
[hypervisor]: https://developer.apple.com/documentation/hypervisor
[install]: https://docs.docker.com/docker-for-mac/install/
