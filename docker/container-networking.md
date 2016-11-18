## Container Networking


!SUB
## Objectives
In this section we will explain:

* How to expose ports
* Docker network models
* How to connect containers


!SUB
## Accessing applications
* To access our application from the outside, we need to explicitly open up a port
* Opening ports can only be done at runtime
* The Dockerfile can give a hint which port(s) to `EXPOSE`:

      EXPOSE 8080

!SUB
## Public vs private ports

A *public port* is reachable from other containers and from outside the host.

A *private port* is not reachable from outside.

* All ports are private by default.
* The `Dockerfile` doesn't control if a port is publicly available.
* When you `docker run -p <port> ...`, that port becomes public. (Even if it was not declared with `EXPOSE`.)
* When you `docker run -P ...` (without port number), all ports declared with `EXPOSE` become public.


!SUB
## Publishing ports
There are two ways to open ('publish') a port:

* `docker run -P`

    which opens all `EXPOSE`d ports on an ephemeral port (usually 32768 and up)

* `docker run -p <format>`

    where `format` is `ip:hostPort:containerPort | ip::containerPort | hostPort:containerPort | containerPort`

When opening on ephemeral ports, use `docker ps` or `docker port <containerID>` to find out which port was opened


!SUB
## Exercise:<br>Running a simple web server

1. Run the Docker Hub image `jpetazzo/web`
    * Run it in the background
    * Open all `EXPOSE`d ports
2. Find out the port it is exposed on
3. Access the web server with your browser


!SUB
## Exercise:<br>Define host port
1. Run the same app again
    * Map the container port 8000 to 80 on the host
2. Access the webserver on port 80


!SUB
## Docker network models
Using the `--net <mode>` flag we can specify the network model. Options are:

* `bridge` - (default) private ip, connects via `docker0` bridge on host
* `none` - no networking at all
* `host` - opening ports directly on the host
* `container:<container_id>` - join other container's network, access them on localhost and their local port


!SUB
## Connecting containers

One container doesn't make an application

* Imagine an application that consist of a web front-end and a redis backend
* Both components run in a separate container
* We need a way to link them to each other


!SUB
## Exercise:<br>Two containers
1. Start a `redis` container in background and give it a name

       $ docker run -d --name mycache redis

2. Start `nathanleclaire/redisonrails` ([Dockerfile](https://hub.docker.com/r/nathanleclaire/redisonrails/~/dockerfile/))

       $ docker run -it -p 80:3000 nathanleclaire/redisonrails
  * Map container port 3000 to host port 80
  * Run it in the foreground to see the log output
3. Try to open the application
4. What happened?


!SUB
## Docker links

    $ docker run --link <name>:<alias> ...

* Rely on named containers
* Allows the recipient container to read data about and connect to the source container
* Links work by adding the following to the recipient container
  * Name, alias and IP of the source container to the `/etc/hosts` file
  * Environment variables for the source container's ports
    - For example `<NAME>_PORT_<PORT#>_<PROTOCOL>`
  * Any defined environment variables in the source container


!SUB
## Exercise:<br>Linked containers
1. Stop the `nathanleclaire/redisonrails`
2. Restart it, and add a `--link mycache:redis` flag

       $ docker run -it p 80:3000 --link mycache:redis nathanleclaire/redisonrails
3. Try to open the application
4. What happens now?


!SUB
## Exercise:<br>Linked env
1. Stop the `nathanleclaire/redisonrails`
2. Restart it, and now override the command to start bash

       $ docker run -it --link mycache:redis nathanleclaire/redisonrails bash

3. Inspect the `/etc/hosts` file
4. Run `env` to see the available environment variables


!SUB
## Docker links caveats

* The target of a link must be running before starting the second container
* Links are not updated when restarting the target container
* Links only work on a single host
* Docker links are deprecated since Docker 1.9 in favor of the new [Docker networks feature](http://docs.docker.com/engine/userguide/networking/dockernetworks/)
