## What are Containers?

!SUB
## Objectives
In this section, we will explain:
- Learn what containers are (and what they are not)
- Learn the key differences of containers vs VM's
- Learn the benefits of using containers

!SUB
## A quick history overview

!SUB
## Apps on physical servers
![Physical Setup](img/what-is-physical-diagram.png) <!-- .element: class="noborder" -->

!SUB
## to VMs
![Physical Setup](img/what-is-vm-diagram.png) <!-- .element: style="max-height:500px;height:auto" class="noborder" -->

!SUB
## to containers

### ...
Note:
Slide left empty intentionally: Ask for participants view of containers (no discussion), write down on a flipover

!SUB
## to containers
![Docker Setup](img/what-is-docker-diagram.png) <!-- .element: style="max-height:500px;height:auto" class="noborder" -->

!SUB
## Containers vs VMs
- Containers are lightweight
- Less resource usage
- More containers per machine than VMs
- Greater portability

!SUB
## Containers
- wrap up a piece of software in a complete filesystem that contains everything it needs to run
- are lightweight;
  - share the host-OSs kernel; start fast, efficient RAM usage
  - use images which are constructed from layered filesystem; sharing of common files, efficient disk usage and downloads
Note: Review the flipover

!SUB
## Quiz: A Container is ...
1. A small VM containing your application
2. An isolated environment for running your software
3. A way to package your software
