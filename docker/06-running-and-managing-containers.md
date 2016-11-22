## Running and managing containers


!SUB
## Creating and running a container
- Use the ```docker run``` command
- The ```docker run``` command actually does two things
  - Creates the docker container using the image we specify
  - Runs the container
- Syntax
```
docker run [options] [image] [command] [args]
```
- Image is specified with ```repository:tag```

!SUB
## Container with terminal
- Use ```-i``` and ```-t``` flags with ```docker run```
- The ```-i``` flag tells Docker to connect to STDIN on the container
- The ```-t``` flag specifies to get a pseudo-terminal
- **Note**: You need to run a terminal process as your command (e.g. ```bash```)
```bash
docker run -i -t ubuntu:latest bash
```
Because of defaults, this will give the same result:
```bash
docker run -it ubuntu
```

!SUB
## Exit the terminal
- Type ```exit``` to quit the terminal and return to your host terminal
- Exiting the terminal will shutdown the container
- To exit the terminal without a shutdown, hit ```CTRL + P + Q``` together

!SUB
## Find your containers
- Use ```docker ps``` to list running containers
- The ```-a``` flag to list all containers (includes containers that are stopped)
```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
cd73db25f897        shipyard/shipyard   "/bin/controller"        8 hours ago         Exited (0) 8 hours ago                         admiring_visvesvaraya
8aa4a6df4ba5        ubuntu              "/bin/sh -c 'while tr"   8 hours ago         Up 8 hours                                     furious_archimedes
88c91c013ac7        redis               "/entrypoint.sh redis"   9 hours ago         Exited (137) 8 hours ago                       evil_knuth
97b26be554ec        nginx               "nginx -g 'daemon off"   9 hours ago         Exited (0) 9 hours ago                         sad_leavitt
```


!SUB
## Container processes
- A container only runs as long as the process from your specified ```docker run``` command is running
- Your command's process is always PID 1 inside the container

!SUB
## Container processes
<img src="img/docker-pid1.jpg">

!SUB
## See for yourself

Compare the results:

    docker run ubuntu ps

and:

    docker run -ti ubuntu bash
    ps

Which process has PID 1?


!SUB
## Container ID
- Containers can be specified using their ID or name
- Long ID and short ID
- Short ID and name can be obtained using docker ps command to list
containers
- Long ID obtained by inspecting a container
- Long IDs will be returned when you start a daemon process

!SUB

## One way to obtain a long ID:

    $ export MYID=$(docker run -d ubuntu sleep 100)
    $ echo $MYID
    4d964fb02e4b2c370b4402af9b6457384b7677eb3586943b6c51ed33b65c3b2b

But obviously you should prefer the `--name` parameter over this.

!SUB
## Docker ps
- To view only the container ID’s (displays short ID)
```
docker ps -q
```
- The view the last container that was started
```
docker ps -l
```

!SUB
## Docker ps
- Combining flags to list all containers with only their short ID
```
docker ps –aq
```
- Combining flags to list the short ID of the last container started
```
docker ps -lq
```

!SUB
## Docker ps filtering
- Use the ```--filter``` flag to specify filtering conditions
- Currently you can filter based on the container’s exit code and status
- Status can be one of
  - Restarting
  - Running
  - Exited
  - Paused
- To specify multiple conditions, pass multiple ```--filter``` flags

!SUB
## Docker ps filtering
```
$ docker ps -a --filter 'exited=0'
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
248694720ae2        ubuntu              "bash"              13 minutes ago      Exited (0) 13 minutes ago                       romantic_euclid
```

!SUB
## Detached mode
- Also known as running in the background or as a daemon
- Use ```-d``` flag
- To observe output use ```docker logs [container id]```
```
docker run -d centos:7 ping 127.0.0.1 -c 50
```

!SUB
## Attaching to a container
- Attaching a client to a container will bring a container which is running in the background into the foreground
- The containers PID 1 process output will be displayed on your terminal
- Use ```docker attach``` command and specify the container ID or name
- **Warning**: Attaching to containers is error prone because if you hit
CTRL + C by accident, you will stop the process and therefore stop the container

!SUB
## Detaching from a container
- Hit ```CTRL + P + Q``` together on your terminal
- Only works if the following two conditions are met
  - The container standard input is connected
  - The container has been started with a terminal
  - For example: ```docker run -i -t ubuntu```
- Hitting CTRL + C will terminate the process, thus shutting down the container
Note:
docker run -t -i → can be detached with ^P^Q and reattached with docker attach
docker run -i → cannot be detached with ^P^Q; will disrupt stdin
docker run → cannot be detached with ^P^Q; can SIGKILL client; can reattach with docker attach


!SUB
## Exercise:<br>Run an interactive ubuntu container and detach (container stays running), check ```docker ps``` and attach to it

!SUB
## Docker exec
- ```docker exec``` command allows us to execute additional processes inside a container
- Typically used to gain command line access
```
docker exec -i -t [containerID] bash
```
- Exiting from the terminal will not terminate the container

!SUB
## Exercise:<br>Run an interactive ubuntu container echoing "hello world" every 10 seconds, detach, and exec into this container to check the process table

!SUB
## Inspecting container logs
- Container PID 1 process output can be viewed with ```docker logs``` command
- Will show whatever PID 1 writes to stdout or stderr
- Displays the entire log output from the time the container was created

!SUB
## Following container logs
- Use ```docker logs``` command and specify the ```-f``` option
- Similar to Linux ```tail -f``` command
- ```CTRL + C``` to exit
- Will still follow the log output from the very beginning

!SUB
## Tailing container logs
- We can specify to only show the last “x” number of lines from the logs
- Use ```--tail``` option and specify the number
```
docker logs --tail 5 <container ID>
docker logs --tail 5 -f <container ID>
```

!SUB
## Stopping a container
- Two commands we can use
  - ```docker stop```
  - ```docker kill```
- ```docker stop``` sends a SIGTERM to the main container process
  - Process then receives a SIGKILL after a grace period
  - Grace period can be specified with -t flag (default is 10 seconds)
- ```docker kill``` sends a SIGKILL immediately to the main container process

!SUB
## Restarting a container
- Use ```docker start``` to restart a container that has been stopped
- Container will start using the same options and command specified
previously
- Can attach to the container with ```-a``` flag

!SUB
## Inspecting a container
- ```docker inspect``` command displays all the details about a container
- Outputs details in JSON array
<img src="img/docker-inspect.jpg">

!SUB
## Finding a specific property
- You can pipe the output of ```docker inspect``` to grep and use it to search a particular container property.
```
docker inspect <container name> | grep IPAddress
```

!SUB
## Formatting the docker inspect output
- Piping the output to ```grep``` is simple, but not the most effective method of formatting
- Use the ```--format``` option of the ```docker inspect``` command
- Format option uses Go’s text/template package
http://golang.org/pkg/text/template/

!SUB
## Formatting the docker inspect output
- Field names are case sensitive
```
docker inspect --format=‘{{.<field1>.<field2>}}’ <container id>
```
```
docker inspect --format='{{.Config.Cmd}}' <container id>
```

!SUB
## Exercise:<br>Use ```docker inspect``` to show only the IPAddress property of a container

!SUB
## Deleting containers
- Can only delete containers that have been stopped
- Use ```docker rm``` command
- Specify the container ID or name

!SUB
## Delete all containers
- Use ```docker ps -aq``` to list the id’s of all containers
- Feed the output into ```docker rm``` command
```
docker rm $(docker ps –aq)
```

!SUB
# NUCLEAR!
## If you also want to stop any running containers

```
docker rm $(docker stop $(docker ps -q))
```
