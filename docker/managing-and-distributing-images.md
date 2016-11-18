## Managing and distributing images

!SUB
## Objectives

In this section, we will explain:

- How to delete and tag images
- How we can distribute our images
- How we can setup public and private repositories in Docker Hub

!SUB
## Deleting images

You can delete your images in the following ways

    $ docker rmi <image_id>
    $ docker rmi <image[:tag]>
    $ docker rmi <image@digest>

!SUB
## Exercise:<br>
Can you delete an image which is still being used?

    $ docker run -d alpine sleep 30
    $ docker rmi alpine

    $ # how about this?
    $ docker run -d alpine:3.2 sleep 30
    $ docker rmi alpine:3.2

Note:
First scenario only removes the latest tag, second refuses to delete the image

!SUB
## Dangling images
If you build your own images, you might end up with dangling (untagged) images.

    $ docker images -f "dangling=true"
    REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
    <none>              <none>              9a5a7f43a880        9 days ago          1.084 GB

These intermediary layers can be removed by

    $ docker rmi $(docker images -f "dangling=true" -q)


!SUB
## Image tags
Tagging images allows you identify a version of an image


!SUB
## The :latest tag
    What does the 'latest' tag really mean?

    01-01-2014 $ docker pull ubuntu:latest
    18-11-2015 $ docker run ubuntu:latest echo 'Which version am I?'
    19-11-2015 $ docker pull ubuntu:latest

Note:
:latest is not always the latest version of a certain image, default would be a more adequate name. Use explicit versions whenever possible

!SUB
## Pushing to Hub

    $ docker login -u <username> -p <password> -e <email>
    $ # if the repo does not exist, it will create one (public)
    $ docker push <username>/<image>[:<tag>]

Note:
You can create repositories beforehand or have docker push create it automatically. You can change a public repo to a private one on Hub.

!SUB
## Pushing public images to a private repo

    $ docker pull ubuntu
    $ docker tag ubuntu myrepo/mycompany/ubuntu
    $ docker push myrepo/mycompany/ubuntu

