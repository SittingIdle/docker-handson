## Advanced Dockerfiles





!SUB
## `Dockerfile` summary

* `Dockerfile` instructions are executed in order.
* Each instruction creates a new layer in the image.
* Instructions are cached. If no changes are detected then the instruction is skipped and the cached layer used.
* The `FROM` instruction MUST be the first non-comment instruction.
* Lines starting with `#` are treated as comments.


!SUB
## What else can we do?

* Specifying a `MAINTAINER`
* `COPY`ing and `ADD`ing files
* `EXPOSE`ing ports
* Setting the `WORKDIR`
* Adding `VOLUME`s
* Setting `ENV` variables
* Changing `USER`


!SUB
## Who built that?
The `MAINTAINER` instruction tells you who wrote the `Dockerfile`.

    MAINTAINER Me <me@example.org>

It's optional but recommended.


!SUB
## Add our own files to the image
Docker images are not completely built from external packages. So need to be able to add local files as well.

Docker provides two instructions for that:

* `COPY <src>... <dest>`
* `ADD <src>... <dest>`

Both can add files from local `<src>` to `<dest>` in our image.


!SUB
## `COPY` and `ADD`?
`COPY` does exactly what you expect.

`ADD` does two more things:
* Allows `<src>` to be a URL
* Extracts files if `<src>` is an (compressed) tar
* (but not both)


!SUB
## Exercise:<br>Build a C application

Here is the program, `hello.c`:

    #include <stdio.h>
    int main () {
        puts("Hello, world!");
        return 0;
    }
Hints:
 * We can compile it by running `make hello`
 * `make` is part of the `build-essential` package on Debian and Ubuntu.


!SUB
## Exercise:<br>Build a C application

* Create `hello.c` and `Dockerfile` in the same directory
* Run `docker build -t hello .`
* Run `docker run hello`, and you should see `Hello, world!`


!SUB
## Exposing ports
The `EXPOSE` instruction tells Docker what ports are to be published
in this image.

    EXPOSE 8080

* All ports are private by default.
* The `Dockerfile` doesn't control if a port is publicly available.
* When you `docker run -p <port> ...`, that port becomes public.
* When you `docker run -P ...`, all ports declared with `EXPOSE` become public.

(More on this later)


!SUB
## Setting `WORKDIR`
The `WORKDIR` instruction sets the working directory for subsequent
instructions.

It affects:
* `RUN`
* `COPY` and `ADD`, when `<dest>` is a relative path
* `CMD` and `ENTRYPOINT`


!SUB
## Adding a `VOLUME`

The `VOLUME` instruction will create a data volume mount point at the
specified path.

    VOLUME [ "/opt/webapp/data" ]

* Data volumes bypass the union file system.
* Data volumes can be shared and reused between containers.
* It is possible to share a volume with a stopped container.
* Data volumes persist until all containers referencing them are destroyed.

(More on this later)


!SUB
## Setting the environment
The `ENV` instruction specifies environment variables that should be
set in any container launched from the image.

    ENV WEBAPP_PORT 8080

You can also specify environment variables when you use `docker run`.

    $ docker run -e WEBAPP_PORT=8000 -e WEBAPP_HOST=www.example.com ...


!SUB
## Change user
The `USER` instruction sets the userid to switch to when running the image.
Specify either a user name or uid.

* It can be used multiple times to change back to root or to another user.
* The user name must exist in the container for it to be able to run.
* The `USER` instruction does affect `RUN`, but does *not* affect `COPY` and `ADD`.


!SUB
## Collapsing layers

Each instruction in a `Dockerfile` creates a new layer:

    RUN apt-get update
    RUN apt-get install -y wget
    RUN apt-get clean

It is possible to chain multiple commands in a single step:

    RUN apt-get update && apt-get install -y wget && apt-get clean

It is also possible to break a command on multiple lines:

    RUN apt-get update && \
        apt-get install -y wget && \
        apt-get clean


!SUB
## Best practices

* Build your `Dockerfile` to take advantage of Docker's caching system
* Combine multiple similar commands into one by using `&&` to continue commands and `\` to wrap lines
* `COPY` dependency lists (`package.json`,`requirements.txt`, ...) by themselves to avoid reinstalling unchanged dependencies every time
* Be explicit when specifying `FROM` image
* Try to minimize layer and image size
* Prefer Exec over Shell form
* Change the `USER`, don't run as root

There's more: https://docs.docker.com/engine/articles/dockerfile_best-practices/

!SUB
## Exercise:<br>Fix "bad" `Dockerfile`

    FROM ubuntu
    RUN apt-get update
    RUN apt-get install -y -q python-all
    RUN apt-get install -y -q python-pip
    COPY ./webapp /opt/webapp/
    WORKDIR /opt/webapp
    RUN pip install -qr requirements.txt
    EXPOSE 5000
    CMD python app.py


!SUB
## Fixed `Dockerfile`

    FROM ubuntu:15.04
    MAINTAINER Me <me@example.com>

    # Install packages
    RUN apt-get update && \
        apt-get install -y -q python-all python-pip && \
        apt-get clean

    # Install python dependencies
    COPY ./webapp/requirements.txt /tmp/requirements.txt
    RUN pip install -qr /tmp/requirements.txt

    # Copy app code
    COPY ./webapp /opt/webapp/
    WORKDIR /opt/webapp

    EXPOSE 5000
    CMD ["python", "app.py"]
