## Container Volumes

!SUB
## Objectives
In this section, we will explain:
* What volumes are
* How to run containers holding volumes
* How to share volumes across containers
* How to share a host directory with one or many containers


!SUB
## What are volumes?
When starting a container, it creates a copy-on-write filesystem layer on top of the image you defined. This is used to create new layers.

Volumes bypass the copy-on-write layer, and act as passthroughs to the host filesystem.

Note: This means the contents of a volume is not recorded in a `docker commit`.


!SUB
## But why?

Docker volumes can be used to achieve many things, including:

* Bypassing the copy-on-write system to obtain native disk I/O performance
* Bypassing copy-on-write to leave some files out of `docker commit`
* Sharing a directory between multiple containers
* Sharing a directory between the host and a container
* Sharing a *single file* between the host and a container


!SUB
## Creating volumes
Two ways:

* Within a `Dockerfile`, with a `VOLUME` instruction

      VOLUME /var/lib/postgresql

* On the command-line, with the `-v` flag for `docker run`

      $ docker run -d -v /var/lib/postgresql \
        training/postgresql

In both cases, `/var/lib/postgresql` (inside the container) will be a volume.


!SUB
## Sharing volumes
Containers can share volumes. This is done using the `--volumes-from` flag for `docker run`.

Start a container with a volume:

    $ docker run -it --name alpha -v /var/log ubuntu bash
    root@99020f87e695:/# date >/var/log/now

Start another container in a second terminal:

    $ docker run --volumes-from alpha ubuntu cat /var/log/now
    Fri May 30 05:06:27 UTC 2014


!SUB
## Volume lifecycle

* As long as a volume is referenced by at least one container, you will be able to access it
* When you remove the last container referencing a volume, that volume will be orphaned
* Orphaned volumes are not deleted
* The data is not lost, but you will not be able to access it<br>(Unless you do some serious archeology in `/var/lib/docker`)


!SUB
## Inspecting volumes

To look which paths are actually volumes, and to what they are bound,
use `docker inspect`:

     $ docker inspect <yourContainerID>
     [{
       "ID": "<yourContainerID>",
       ...
       "Volumes": {
          "/var/webapp": "/var/lib/docker/vfs/dir/f4280c5b6207ed531efd4cc673ff620cef2a7980f747dbbcca001db61de04468"
       },
       "VolumesRW": {
          "/var/webapp": true
       },
     }]


!SUB
## `docker volume` sub-command
Docker 1.9 adds a `docker volume` subcommand to manage volumes

    $ docker volume --help

    Usage:  docker volume [OPTIONS] [COMMAND]

    Manage Docker volumes

    Commands:
      create                   Create a volume
      inspect                  Return low-level information on a volume
      ls                       List volumes
      rm                       Remove a volume

    Run 'docker volume COMMAND --help' for more information on a command

      --help=false       Print usage


!SUB
## Sharing between host and container
The `-v` flag can also be used to share data from the host to the guest.

    $ echo 4815162342 > /tmp/numbers
    $ docker run -it -v /tmp/numbers:/numbers ubuntu bash
    root@<yourContainerId>:/# cat /numbers
    4815162342

All modification in the container are also visible on the host

Can also be used to share directories and even *sockets* and *devices* (remember: on Linux everything is a file)


!SUB
## Exercise:<br>Chaining containers

1. Create an initial container with a volume
2. Create some data in the volume
3. Create a new container and mount volume from initial container
4. View and modify data
5. Create a third container, mounting the volume from the second
6. View the data
7. Cleanup all containers


!SUB
## Exercise:<br>Sharing sockets
Share the Docker socket and binary with your container, and you can run Docker from Docker

    $ docker run -it \
        -v /var/run/docker.sock:/var/run/docker.sock \
        -v $(which docker):$(which docker) \
        ubuntu

    root@98c072119679:/# docker ps
    CONTAINER ID  IMAGE   COMMAND      CREATED        STATUS        PORTS  NAMES
    98c072119679  ubuntu  "/bin/bash"  4 seconds ago  Up 4 seconds         determined_northcutt

But be careful: if a container can access `/var/run/docker.sock`, it will be able to do *anything it wants* on the host!

