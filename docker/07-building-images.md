## Building images





!SUB
## BYOI: Build you own image
Images on the Docker Hub can be very basic

How do you create your own image?

As an example, we will build an image that has `wget`.

First, we will do it manually with `docker commit`.

Then, we will use a `Dockerfile` and `docker build`.


!SUB
## Exercise:<br>Building manually
- Start new `ubuntu` container
- Verify that `wget` is not installed
- Update repositories and install `wget`
- Exit container and `commit` changes into new image

See next slide...

!SUB
## Exercise:<br>Building manually

    $ docker run -it ubuntu bash
    root@<container_id>:#/ wget -q -O- http://ifcfg.me/ip
    bash: wget: command not found

    root@<container_id>:#/ apt-get update
    ...

    root@<container_id>:#/ apt-get install -y wget
    ...

    root@<container_id>:#/ wget -q -O- http://ifcfg.me/ip
    12.34.56.78

    root@<container_id>:#/ exit

    $ docker commit <container_id> <new_image_name>


!SUB
## What has been added to our image?

`docker diff` can show what has changed:

    $ docker diff <container_id>
    C /root
    A /root/.bash_history
    C /tmp
    C /usr
    C /usr/bin
    A /usr/bin/wget


!SUB
## Tracking file system changed
As explained before:

* An image is read-only
* When we make changes, they happen in a copy of the image
* Docker can show the difference between the image, and its copy
* For performance, Docker uses copy-on-write systems
  <br/>(i.e. starting a container based on a big image
  doesn't incur a huge copy.)


!SUB
## Tagging images

Referring to an image by its ID is not convenient. Let's tag it instead.

We can use the `tag` command:

    $ docker tag <image_id_or_name> <new_name>

Note: The above is FYI - there is not much use in the current exercise.

!SUB
## Tag your image

Tag newly created image with new name

And then run it using its name

    $ docker run -it <new_name>

We can also specify the tag as an extra argument to `commit`:

    $ docker commit <containerId> <new_name>


!SUB
## But we don't like to do things by hand...
So let's automate :)


!SUB
## Dockerfiles
* A `Dockerfile` is a build recipe for a Docker image
* It contains a series of instructions telling Docker how an image is constructed
* The `docker build` command builds an image from a ``Dockerfile``


!SUB
## A minimal Dockerfile

    FROM ubuntu
    RUN apt-get update
    RUN apt-get install -y wget

* `FROM` indicates the base image for our build
* Each `RUN` line will be executed during the build
* `RUN` commands must be *non-interactive*

Note to Docker experts: Yes, this Dockerfile is flawed; we'll get to that later.

!SUB
## Building Dockerfiles

    $ docker build -t myimage .

* `-t` indicates the tag to apply to the image
* `.` indicates the location of the *build context* (the directory containing the ``Dockerfile``)


!SUB
## Build output

    $ docker build -t myimage .
    Sending build context to Docker daemon 2.048 kB
    Step 1 : FROM ubuntu
     ---> 1d073211c498
    Step 2 : RUN apt-get update
     ---> Running in 2796360ae1cf
     ---> c861680d12d6
    Removing intermediate container 2796360ae1cf
    Step 3 : RUN apt-get install -y wget
     ---> Running in 490f94973cb2
     ---> 1e6801726ad2
    Removing intermediate container 490f94973cb2
    Successfully built 1e6801726ad2


!SUB
## Caching builds

If you run the same build again, it will be instantaneous. Why?

* After each build step, Docker takes a snapshot of the resulting image.
* Before executing a step, Docker checks if it has already built the
  same sequence.
* Docker uses the exact strings defined in your Dockerfile, so:

  * `RUN apt-get install -y wget curl` is different from
    <br/> `RUN apt-get install -y curl wget`
  * `RUN apt-get update` is not re-executed when the mirrors are updated

You can force a rebuild with `docker build --no-cache ...`.


!SUB
## Image history

The `history` sub-command lists *all* the layers composing an image.

    $ docker history myimage
    IMAGE         CREATED        CREATED BY                                     SIZE      COMMENT
    1e6801726ad2  4 minutes ago  /bin/sh -c apt-get install -y wget             6.052 MB
    c861680d12d6  5 minutes ago  /bin/sh -c apt-get update                      21.36 MB
    1d073211c498  3 weeks ago    /bin/sh -c #(nop) CMD ["/bin/bash"]            0 B
    5a4526e952f0  3 weeks ago    /bin/sh -c sed -i 's/^#\s*\(deb.*universe\)$/  1.895 kB
    99fcaefe76ef  3 weeks ago    /bin/sh -c echo '#!/bin/sh' > /usr/sbin/polic  194.5 kB
    c63fb41c2213  3 weeks ago    /bin/sh -c #(nop) ADD file:e97fe9bddafcfac4ca  187.7 MB


!SUB
## Exercise:<br>Automate your build

1. Create a Dockerfile to install `wget` on `ubuntu`
2. Build and tag your image
3. Look at the image history
4. Run container and verify wget is installed


!SUB
## Executing wget

When starting you container, you'll end up in a `bash` shell in your container

We can tell Docker to execute a different command when starting the container, by adding a line to our `Dockerfile`:

    CMD wget -O- -q http://ifcfg.me/ip


!SUB
## The `CMD` instruction
* `CMD` defines a default command to run when none is given.
* It can appear at any point in the file.
* Each `CMD` will replace and override the previous one.
* As a result, while you can have multiple `CMD` lines, it is useless.


!SUB
## Exercise: Default `CMD`
Add a `CMD` to you Dockerfile to retrieve your IP address, then build and run it:

    $ docker build -t ifconfigme .
    Successfully built 042dff3b4a8d

    $ docker run ifconfigme
    64.134.229.24


!SUB
## Overriding `CMD`

If we want to get a shell into our container (instead of running
`wget`), we just have to specify a different program to run:

    $ docker run -it ifconfigme bash
    root@7ac86a641116:/#


!SUB
## Fixed executable, variable parameters

We want to be able to only specify a different URL on the command line, without explicitly calling `wget`.

In other words, we would like to be able to do this:

    $ docker run ifconfigme http://ifcfg.me/ua
    Wget/1.12 (linux-gnu)


!SUB
## The `ENRTYPOINT` instruction

An `ENTRYPOINT` sets the executable that will be run when starting a container.

Replace the `CMD` in the Dockerfile with:

    ENTRYPOINT ["wget", "-O-", "-q"]


!SUB
## Exercise:<br>Fixed executable
Modify your Dockerfile so it always executes `wget`, then build and run it:

    $ docker build -t ifconfigme .
    Successfully built 042dff3b4a8d

    $ docker run ifconfigme http://ifcfg.me/ua
    Wget/1.12 (linux-gnu)


!SUB
## Overriding `ENTRYPOINT`
Similar like `CMD` , the `ENTRYPOINT` can be overriden. To do so, use the `--entrypoint` flag of the `docker run` command:

    $ docker run -it --entrypoint bash ifconfigme
    root@7ac86a641116:/#


!SUB
## Combining<br>`CMD` and `ENTRYPOINT`
By using `ENTRYPOINT` and `CMD` together, we can set a fixed executable that has a default argument

* `ENTRYPOINT` will define the base executable for our container
* `CMD` will define the default parameter(s) for this command


!SUB
## Exercise: Fixed executable, default param
Modify your Dockerfile so you get your IP by default, but you can also specify other parameters:

    $ docker build -t ifconfigme .
    Successfully built 042dff3b4a8d

    $ docker run ifconfigme
    64.134.229.24

    $ docker run ifconfigme http://ifcfg.me/ua
    Wget/1.12 (linux-gnu)


!SUB
## Exec vs Shell form

Both `CMD` and `ENTRYPOINT` have two forms:

* Exec form:

      CMD ["executable", "param1", "param2"]

* Shell form:

      CMD command param1 param2


!SUB
## Shell form caveats

Shell form gets wrapped in a `/bin/sh -c`. This means your command will not be `PID 1`, and it will not receive `SIGTERM` signals when executing a `docker stop`

Shell form also prevent overriding `CMD` or command line `docker run` parameters
