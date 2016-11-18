## Docker concepts and terms


!SUB
## Objectives
In this section we will explain all the main Docker concepts:

- Docker Engine
- Docker Client
- Containers
- Images
- Registry and Repositories
- Orchestration


!SUB
## Docker and <br> the Linux Kernel
- Docker Engine is the program that enables containers to be distributed and run
- Docker Engine uses Linux Kernel namespaces and control groups
- Namespaces give us the isolated workspace
- cgroups limits resources (CPU, memory, IO) that can be used


!SUB
## Docker and <br> the Linux Kernel
![Docker Stack](img/docker-stack.jpg) <!-- .element style="max-width: 50%" class="noborder" -->


!SUB
## Docker Architecture
![Docker Architecture](img/docker-architecture.svg) <!-- .element style="max-width: 75%" class="noborder" -->


!SUB
## Docker Client and Daemon
- Client / Server architecture
- Client takes user inputs and sends them to the daemon
- Daemon runs and distributes containers
- Client and daemon can run on the same host or on different hosts
- CLI client and GUI (Kitematic)


!SUB
## Docker Containers and Images
- Images
    - Read only template used to create containers
    - Built by you or other Docker users
    - Stored in Docker Hub, Docker Trusted Registry or your own Registry
- Containers
    - Isolated application platform
    - Contains everything needed to run your application
    - Created from images


!SUB
## Registry and Repository
- Registry
    - Service that stores images
    - Public: Docker Hub
    - Private: Docker Trusted Registry
    - Private: Open Source Registry
- Repository
    - Part of the registry to store one type of images (e.g. ubuntu)
    - Images can have tags for different versions (e.g. 15.04, 14.10)
    - Default tag is `latest`
    - Image naming: `[[<registry>/]<user_namespace>/]<repository>[:<tag>]`


!SUB
## Orchestration
Docker provides 3 tools for orchestrating distributed applications:

- Docker Machine
  <br>Provisions Docker hosts and installs the Docker Engine

- Docker Swarm
  <br>Clusters many Engines and schedules containers

- Docker Compose
  <br>Create and manage multi-container applications
