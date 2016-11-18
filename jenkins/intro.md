# Jenkins

!SUB
## Objectives
In this section, we will explain:
- How to install Jenkins (on top of Docker)
- How to install Gradle in Jenkins
- How to create a pipeline
- Call to Docker within Pipeline to:
  - build image
  - deploy image
  - run a test

!SUB
## Install Jenkins

```
docker create --name jenkins-data adejonge/jenkins
docker run -d --name jenkins \
            -p 8080:8080 \
            --volumes-from jenkins-data \
            -v /var/run/docker.sock:/var/run/docker.sock \
            adejonge/jenkins
```

!SUB
## Installer screen

- Take the admin password from the Docker log
- Click "Install suggested plugins"

!SUB
## RC1 Bug Workaround

- Click "Manage Jenkins"
- Click "Configure Global Security"
- Uncheck "Prevent Cross Site Request Forgery exploits"
- Save

!SUB
## Add Gradle installer

- Click "Manage Jenkins"
- Click "Global Tool Configuration"
- Add Gradle installer
  - Name: gradle
  - Version: 2.12 (latest stable version)

!SUB
## Create pipeline

- Go back to Jenkins home (click logo in right top)
- Click "New Item"
- Provide name "First Pipeline"
- Choose "Pipeline"

_see next screen_

!SUB
## Provide details

- Git: https://github.com/adriaandejonge/try-gradle
- Pipeline:

```groovy
node {
   stage 'Checkout'

   git url: 'https://github.com/adriaandejonge/try-gradle/'
   def gradle = tool 'gradle'

   stage 'Build'
   sh "${gradle}/bin/gradle build"
}
```

!SUB

# Exercise 1

Build a Docker image in the pipeline


!SUB

# Exercise 2

Upload the Docker image to the Docker Hub

Hint: Log in to Docker Hub from within the Jenkins container using `docker exec`


!SUB

# Exercise 3

Run a container created from the Docker image

!SUB

# Exercise 4

Create a simple test for the container

Hint: https://github.com/adriaandejonge/test/
Hint: `adejonge/mocha`




!SUB

# Exercise 5

Can you store the pipeline in version control?

Hint: `Jenkinsfile`

Can you now recreate the pipeline from version control directly?
