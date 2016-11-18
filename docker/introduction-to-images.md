## Introduction to Images




!SUB
## Image layers
![Image Layers](img/docker-filesystems-multilayer.png) <!-- .element class="noborder" -->


!SUB
## Image as stencils

Images are like templates or stencils that you can create (multiple) containers from

![stencil](img/stenciling-wall.jpg) <!-- .element class="noborder" -->


!SUB
## Wait a minute...

If an image is read-only, how do you change it?

- You don't
- You create a new container from that image
- Then you make changes to that container
- When you are satisfied with those changes, transform them into a new layer
- A new image is created by stacking the new layer on top of the old image


!SUB
## Images namespaces

There are three namespaces:

* Root-like

        ubuntu

* User (and organizations)

        jpetazzo/clock

* Self-Hosted

        my-registry:5000/my-image

Let's explain each of them.


!SUB
## Root namespace

The root namespace is for official images. They are put there by Docker Inc.,
but they are generally authored and maintained by third parties.

Those images include:

* Small, "swiss-army-knife" images like `busybox`.
* Distro images to be used as bases for your builds, like `ubuntu`, `fedora`...
* Ready-to-use components and services, like `redis`, `postgresql`...


!SUB
## User namespace

The user namespace holds images for Docker Hub users and organizations.

For example:

        jpetazzo/clock

The Docker Hub user is:

        jpetazzo

The image name is:

        clock


!SUB
## Self-Hosted namespace

This namespace holds images which are not hosted on Docker Hub, but on third
party registries.

They contain the hostname (or IP address), and optionally the port, of the
registry server.

For example:

        localhost:5000/wordpress

The remote host and port is:

        localhost:5000

The image name is:

        wordpress


!SUB
## How do you store and manage images?

Images can be stored:

* On your Docker host
* In a Docker registry

You can use the Docker client to download (pull) or upload (push) images.

Or more accurately: use the Docker client to tell a Docker daemon
to push and pull images to and from a registry.


!SUB
## Exercise:<br>Showing current images

Let's look at what images are on our host now.

    $ docker images
    REPOSITORY         TAG     IMAGE ID     CREATED     VIRTUAL SIZE
    ubuntu             13.10   9f676bd305a4 7 weeks ago 178 MB
    ubuntu             saucy   9f676bd305a4 7 weeks ago 178 MB
    ubuntu             raring  eb601b8965b8 7 weeks ago 166.5 MB
    ubuntu             13.04   eb601b8965b8 7 weeks ago 166.5 MB
    ubuntu             12.10   5ac751e8d623 7 weeks ago 161 MB
    ubuntu             quantal 5ac751e8d623 7 weeks ago 161 MB
    ubuntu             10.04   9cc9ea5ea540 7 weeks ago 180.8 MB
    ubuntu             lucid   9cc9ea5ea540 7 weeks ago 180.8 MB
    ubuntu             12.04   9cd978db300e 7 weeks ago 204.4 MB
    ubuntu             latest  9cd978db300e 7 weeks ago 204.4 MB
    ubuntu             precise 9cd978db300e 7 weeks ago 204.4 MB

!SUB
##  Exercise:<br>Searching for images

Searches your registry for images:

    $ docker search zookeeper
    NAME                             DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
    jplock/zookeeper                 Builds a docker image for Zookeeper versio...   77                   [OK]
    mesoscloud/zookeeper             ZooKeeper                                       23                   [OK]
    mbabineau/zookeeper-exhibitor                                                    12                   [OK]
    digitalwonderland/zookeeper      Latest Zookeeper - clusterable                  4                    [OK]
    springxd/zookeeper               A Docker image that can run a ZooKeeper se...   4                    [OK]

* "Stars" indicate the popularity of the image.
* "Official" images are those in the root namespace.
* "Automated" images are built automatically by the Docker Hub.


!SUB
## Downloading images

There are two ways to download images.

* Explicitly, with `docker pull`.
* Implicitly, when executing `docker run` and the image is not found locally.


!SUB
## Exercise:<br>Pull Debian image

    $ docker pull debian
    Using default tag: latest
    latest: Pulling from library/debian
    Digest: sha256:017c4cfd3b10fe77c37a515c38f4327e0668fdb287c0aa1436967fda37010713
    Status: Downloaded newer image for debian:latest

* Note that it downloads the default tag `latest`
* As seen previously, images are made up of layers.
* Docker has downloaded all the necessary layers.


!SUB
## Exercise:<br>Pull Debian Jessie image

    $ docker pull debian:jessie
    jessie: Pulling from library/debian
    Digest: sha256:1179b696ceb85111a6928ba699d38a56a1f5e89fe0eef3277a3a2f51572da37a
    Status: Image is up to date for debian:jessie

* In this example, `:jessie` indicates which exact version of Debian we would like. It is a *version tag*
* Note that it is the same image id as the previous exercise


!SUB
## Image and tags

* Images can have tags.
* Tags define image variants.
* `docker pull ubuntu` will refer to `ubuntu:latest`.
* The `:latest` tag can be updated frequently.
* When using images it is always best to be specific.
