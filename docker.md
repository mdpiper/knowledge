# Docker

Containers are cool!

I got a lot from the [Getting Started][getting-started] tutorial
and the video from Dockercon 2020 that accompanies it.

I also have a [repository of Docker examples](https://github.com/mdpiper/docker-examples).

## Docker login

Get Docker credentials on local machine with `docker login`.
```
docker login --username=mdpiper --password-stdin
```

## Docker information

The `docker info` subcommand displays system information.

Use the `docker version` subcommand (instead of `docker --version`)
to get more detailed version information on the Docker client and server.

Use the `--help` flag on the `docker` command,
as well as its subcommands and subsubcommands.

## Dockerfile syntax

A Dockerfile is the recipe that describes how to 
build and populate a Docker image.
It's a text file that contains a set of instructions.

| Instruction | Description |
| ----------- | ----------- |
| FROM | Base image to use. Must be first command in file. |
| COPY | Copy files from local filesystem to image filesystem. |
| CMD | Run a command, intended as the default for a container. There can be only one CMD instruction per file. Cf. ENTRYPOINT and RUN. |
| ENV | Sets an environment variable. |
| EXPOSE | Open a network port. |
| RUN | Execute a command. |
| WORKDIR | Sets the working directory in the image. |
| # | Comment character |

Instructions are layered in order.
Instructions aren't case sensitive,
but uppercase is the convention.

*References:*

* Dockerfile instructions: https://docs.docker.com/engine/reference/builder/
* Dockerfile examples: https://docs.docker.com/engine/reference/builder/#dockerfile-examples
* Dockerfile best practices: https://docs.docker.com/develop/develop-images/dockerfile_best-practices/


## Images

Build a Docker image:
```
docker build --tag hello-docker:0.1 .
```
The path (here `.`) is to the location of the Dockerfile.

Display a list of local images:
```
docker images
```
There's also an alternate subsubcommand syntax:
```
docker image ls
```

Remove an image from the local environment:
```
docker image rm hello
```

Remove all unnamed images:
```
docker image prune
```

Remove all images, named and unnamed:
```
docker image prune -a
```
This may not be the best action
since it also removes base images that I build on.
These images will have to be downloaded again.

*References:*

* Full CLI documentation: https://docs.docker.com/reference/

### Multiplatform builds

I have an Intel-based iMac and an M1 MacBook Pro.
By default, images built on one won't work on the other.
I need a multiplatform build.

Use the `docker buildx ls` command to see the current list of builders.
I have the default.
Create a new builder with:
```
docker buildx create --name arm-and-amd --bootstrap --use
```
I set up a simple example in mdpiper/docker-cases/multiplatform.
Build it for two platforms, `arm64` and `amd64`,
then push the images to Docker Hub,
with:
```
docker buildx build --platform linux/amd64,linux/arm64 -t mdpiper/multiplatform:latest --push .
```

*References:*

  * https://docs.docker.com/build/building/multi-platform/

### Build logs

Get logs from an image build:
```
docker log hello
```

I've also used a shell method for capturing a build log:
```bash
docker build --tag joss-6079 . 2>&1 | tee build.log
```
The `docker build` output goes to stderr.
The `tee` command writes the output both to the terminal and my log file.

## Containers

A container is an instance of an image.
Launch a container from an image:
```
docker run hello-docker:0.1 
docker run --publish 8080:80 --name hello --detach hello-docker:0.1 
```
Here, `--publish` opens a port on the local machine (and optionally maps it to a port inside the container, as is done here),
`--name` names the running container, overriding the randomly generated name
given to it by Docker,
and `--detach` runs the container in the background.

Images can have many running containers.
What containers are running?
```
docker ps
docker ps -a
```
There's also an alternate subsubcommand syntax:
```
docker container ls
docker container ls -a
```
The `-a` flag shows all containers, even those not running.

Containers can be started, stopped, and removed.
Containers are referenced by the name they're
assigned at launch,
not the tag of the parent image.
```
docker start hello
docker stop hello
docker rm hello
```

Remove all containers that aren't running:
```
docker container prune
```

*References:*

* Full CLI documentation: https://docs.docker.com/reference/


### Run a container interactively

To allow interactive access to a container with a shell prompt, use:
```
docker run -it mdpiper/hello-docker /bin/bash
```
where `-i` keeps stdin open
and `-t` creates a prompt.
The shell is minimal.
I've omitted the shell, and it seems like bash is the default.


## Docker Hub

Docker Hub, https://hub.docker.com, is where you can store images.

Images are stored in repositories on Docker Hub,
although the source code for the image (e.g., the Dockerfile) would be under version control elsewhere (e.g., GitHub).

Login/logout to Docker Hub on a machine with
```
docker login
docker logout
```

After building an image locally, tag it with my Docker id:
```
docker tag hello-docker mdpiper/hello-docker
```
This makes a new local image.

Push an image to a repository on Docker Hub:
```
docker push mpiper/hello-docker
```

Pull an image from Docker Hub:
```
docker pull mdpiper/hello-docker
```

## Base images

Docker images that I frequently use.

### Ubuntu

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


### CentOS

Similarly, run the CentOS image with:
```
$ docker run -it centos

```

### Miniconda

Install an image preloaded with a Miniconda distribution
in */opt/conda*:
```
docker pull continuumio/miniconda3
docker run -i -t continuumio/miniconda3 /bin/bash
```

The OS is Linux/Debian.
The `base` environment is activated.

For more information, see https://hub.docker.com/r/continuumio/miniconda3.

### Miniforge and Mambaforge

Since we build all CSDMS software on conda-forge,
it's best to use the miniforge and mambaforge distributions they create.
Install an image preloaded with a mambaforge distribution
in */opt/conda*:
```
docker pull condaforge/mambaforge
docker run -i -t condaforge/mambaforge /bin/bash
```

The OS is Ubuntu.
The `base` environment is activated.

For more information, see https://hub.docker.com/r/condaforge/mambaforge.


## Install Docker on CentOS

Install with `yum`:
```
sudo yum install docker.x86_64
```

Run Docker without `sudo` by adding `$USER` to the `docker ` group:
```
sudo groupadd docker
sudo usermod -aG docker $USER
```
Logout/login for the docker daemon to recognize the updated group membership.

*References:*

* https://docs.docker.com/engine/install/linux-postinstall/


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


<!-- Links -->

[getting-started]: https://docs.docker.com/get-started/
[docker-desktop]: https://docs.docker.com/docker-for-mac/
[hyperkit]: https://github.com/docker/HyperKit/
[hypervisor]: https://developer.apple.com/documentation/hypervisor
[install]: https://docs.docker.com/docker-for-mac/install/
