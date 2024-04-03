# Docker Workshop

All the commands run on GIT CMD

## Docker Image

```bash
# To show Docker Image on your machine
docker images
# To pull ubuntu image with tag latest from Docker Hub
docker pull ubuntu
# To show your newly pull ubuntu image on your machine
docker images
```

## Docker Image Name and Tag

```bash
# Pull Ubuntu 18.04
docker pull ubuntu:18.04
# To show Docker Image on your machine
docker images
# Pull Ubuntu 20.04
docker pull ubuntu:20.04
# See the Image ID
docker images
```

## Docker Container

```bash
# Run first ubuntu container
docker run ubuntu echo "Hello World"
# Run container with bash command
docker run -i -t ubuntu bash
# Below command are run inside container
whoami
hostname
cat /etc/*release*
exit
```

## Docker Container Basic Operation

```bash
# Show running containers
docker ps
# Show running and stopped containers
docker ps -a
# Run container with specify name
docker run --name ubuntu-universe ubuntu echo "Hello Universe"
docker ps -a
# Delete container by name
docker rm ubuntu-universe
docker ps -a
# Delete container by part of container id
docker rm 07f
docker ps -a
```

## Run Docker as daemon and expose port

```bash
# Run Nginx
docker run nginx:alpine
docker ps -a
# Run Nginx in background
docker run -d nginx:alpine
docker ps
# Export 8080 port from outside forward to port 80 on container
docker run -d -p 8080:80 nginx:alpine
```

* Try to access localhost via port 8080 by browsers.

```bash
# What happen if try to expose same port again
docker run -d -p 8080:80 nginx:alpine
# What happen if we expose difference outside port
docker run -d -p 8888:80 nginx:alpine
# You can try Web preview again
docker ps
```

### Docker Exercise

* Try to run Apache Server 2.4.33 on Alpine Linux and expose to port 8083

> Hint: <https://hub.docker.com> and search for apache

## Docker Utilities Commands

```bash
# Rename container name
docker rename vigorous_sammet nginx
# To go inside running container
docker exec -it nginx sh
ls -l
ps -ef
exit
# Show container processes
docker top nginx
# Show logs
docker logs nginx
# Follow logs
docker logs nginx -f
# Try Web preview to see log running
# Show container resource consumes
docker stats
# Show container all metadatas
docker inspect nginx
```

* Delete all the containers

```bash
docker rm -f $(docker ps -aq)
```

## Create Dockerfile for Ratings Service

* Create `Dockerfile` inside folder `~/ratings` with below content

```Dockerfile
FROM node:16.14.2-alpine3.15

WORKDIR /usr/src/app/

COPY src/ /usr/src/app/
RUN npm install

EXPOSE 8080

CMD ["node", "/usr/src/app/ratings.js", "8080"]
```

* Run below commands to build and run ratings service container

```bash
# Build Docker Image name ratings
docker build -t registry.devops.ntplc.co.th/workshop-[X]/bookinfo/ratings:dev .
# See newly build Docker Image
docker images
# Run ratings service
docker run -d --name ratings -p 8080:8080 registry.devops.ntplc.co.th/workshop-[X]/bookinfo/ratings:dev
docker ps -a
# Try Web Preview with /health or /ratings/1 as path
```

* Try to run with `SERVICE_VERSION=v2` to force ratings read data from databases

```bash
docker rm -f ratings
docker run -d --name ratings -p 8080:8080 -e SERVICE_VERSION=v2 registry.devops.ntplc.co.th/workshop-[X]/bookinfo/ratings:dev
docker logs -f ratings
# Try Web Preview with /ratings/1 as path to see error logs
```

## Run MongoDB Container

```bash
# Run MongoDB Container
docker run -d --name mongodb -p 27017:27017 bitnami/mongodb:5.0.6-debian-10-r46
# Test connect
docker exec -it mongodb mongo
show dbs
exit

# Try to run ratings service with MongoDB URL
docker rm -f ratings
docker run -d --name ratings -p 8080:8080 --link mongodb:mongodb \
  -e SERVICE_VERSION=v2 -e 'MONGO_DB_URL=mongodb://mongodb:27017/ratings' registry.devops.ntplc.co.th/workshop-[X]/bookinfo/ratings:dev
# Try Web Preview with /ratings/1 as path. It still error and causing container exit
```

## Run MongoDB Container with initial database

* Add initial database script

```bash
mkdir ~/ratings/databases
```

* Add [ratings_data.json](../src/ratings/databases/ratings_data.json) and [script.sh](../src/ratings/databases/script.sh) to `databases/` directory
* Run MongoDB Container again

```bash
# Run MongoDB Container
docker rm -f mongodb
docker run -d --name mongodb -p 27017:27017 \
  -v $(pwd)/databases:/docker-entrypoint-initdb.d bitnami/mongodb:5.0.6-debian-10-r46
# Test connect
docker exec -it mongodb mongo
show dbs
use ratings
show collections
db.ratings.find()
exit
```

* Run ratings service again

```bash
docker rm -f ratings
docker run -d --name ratings -p 8080:8080 --link mongodb:mongodb \
  -e SERVICE_VERSION=v2 -e 'MONGO_DB_URL=mongodb://mongodb:27017/ratings' registry.devops.ntplc.co.th/workshop-[X]/bookinfo/ratings:dev
# Try Web Preview with /ratings/1 as path. It should works now
```

* Update README.md document how to run

````markdown
## How to run with Docker

```bash
# Build Docker Image for rating service
docker build -t registry.devops.ntplc.co.th/workshop-[X]/bookinfo/ratings:dev .

# Run MongoDB with initial data in database
docker run -d --name mongodb -p 27017:27017 \
  -v $(pwd)/databases:/docker-entrypoint-initdb.d bitnami/mongodb:5.0.6-debian-10-r46

# Run ratings service on port 8080
docker run -d --name ratings -p 8080:8080 --link mongodb:mongodb \
  -e SERVICE_VERSION=v2 -e 'MONGO_DB_URL=mongodb://mongodb:27017/ratings' registry.devops.ntplc.co.th/workshop-[X]/bookinfo/ratings:dev
```

* Test with path `/ratings/1` and `/health`
````

* Commit and push code

## How to push docker image
* Push your docker image to registry
```bash
# Using 'docker push' to pushing your image
docker push registry.devops.ntplc.co.th/workshop-[X]/bookinfo/ratings:dev
````

## Navigation

* Previous: [Git and GitLab Workshop](01-prerequtites.md)
* [Home](../README.md)
* Next: [Docker Compose](04-docker-compose.md)
