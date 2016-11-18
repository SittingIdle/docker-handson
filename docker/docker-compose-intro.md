## Intro to Docker Compose

!SUB
## Objectives
In this section, we will explain:
- What Docker Compose is
- How you can use it to define and run multi-container applications

!SUB
## What is Compose
- Defining and running multi-container applications
- Using a Compose file (docker-compose.yml)

<img src="img/composelogo-label.png">

!SUB
## Compose overview

<img src="img/composeoverview.png">

!SUB
## Compose capabilities
- Multiple isolated environments on single host
- Preserve volume data when containers are created
- Only recreate containers that have changed
- Using variables and extending Compose files

!SUB
## Compose file
- docker-compose.yml
- Run it using ```docker-compose up```

```
web:
  build: .
  ports:
   - "5000:5000"
  volumes:
   - .:/code
  links:
   - redis
redis:
  image: redis
```

!SUB
## Exercise
- Create a docker-compose.yml to launch a nodejs container and a redis backend
