# Docker Compose Workshop

## Prerequisite

* Install `docker compose` command
* Login Docker to Habor registry

## Change Docker Command to Docker Compose

* Create `docker-compose.yml` file with below content

```yaml
version: "3.9"

services:
  ratings:
    build: .
    image: registry.devops.ntplc.co.th/workshop-[X]/bookinfo/ratings:dev
    ports:
      - "8080:8080"
    environment:
      SERVICE_VERSION: v1
```

* Run Container with Docker Compose command

```bash
# Delete current running container first
docker rm -f ratings mongodb

docker compose up
```

* Update `README.md` by changing how to run rating service with Docker Compose instead

````markdown
## How to run with Docker Compose

```bash
docker compose up
```
````

* Commit and push code

## Docker Compose Utility Commands

```bash
# To only build docker image
docker compose build
# To force build Docker Image everytime
docker compose up --build
# To choose custom Docker Compose file
docker compose -f custom-compose.yml up
# To run Docker Compose in background
docker compose up -d
# To show status of containers
docker compose ps
# To stop all containers after docker compose up -d
docker compose stop
# To start all containers after docker compose stop
docker compose start
# To restart all containers
docker compose restart
# To show stdout from all containers
docker compose logs
docker compose logs -f
# To show all containers processes
docker compose top
# To list all images in Docker Compose file
docker compose images
# To stop and clean all docker compose up resources
docker compose down
```

## Exercise adding MongoDB

* Update `docker-compose.yml` to make ratings service uses and connects to MongoDB as following docker commands

```bash
docker build -t ratings .
docker run -d --name mongodb -p 27017:27017 \
  -v $(pwd)/databases:/docker-entrypoint-initdb.d bitnami/mongodb:5.0.6-debian-10-r46
docker run -d --name ratings -p 8080:8080 --link mongodb:mongodb \
  -e SERVICE_VERSION=v2 -e 'MONGO_DB_URL=mongodb://mongodb:27017/ratings' ratings
```

* Hints
  * `--link` has been linked automatically by service name
  * `volumes` syntax <https://docs.docker.com/compose/compose-file/compose-file-v3/#short-syntax-3>

## Adding MongoDB to Docker Compose

* Update `docker-compose.yml` file with following content and re-run `docker compose up` command

```yaml
version: "3.9"

services:
  ratings:
    build: .
    image: registry.devops.ntplc.co.th/workshop-[X]/bookinfo/ratings:dev
    ports:
      - "8080:8080"
    environment:
      SERVICE_VERSION: v2
      MONGO_DB_URL: mongodb://mongodb:27017/ratings
  mongodb:
    image: bitnami/mongodb:5.0.6-debian-10-r46
    volumes:
      - "./databases:/docker-entrypoint-initdb.d"
```

## Improve security by using MongoDB Authentication

* Update `databases/script.sh` file with following content

```bash
#!/bin/sh

set -e
mongoimport --host localhost --username $MONGODB_EXTRA_USERNAMES --password $MONGODB_EXTRA_PASSWORDS \
  --db $MONGODB_EXTRA_DATABASES --collection ratings --drop --file /docker-entrypoint-initdb.d/ratings_data.json
```

* Update `docker-compose.yml` file with following content and re-run `docker compose up` command

```yaml
version: "3.9"

services:
  ratings:
    build: .
    image: registry.devops.ntplc.co.th/workshop-[X]/bookinfo/ratings:dev
    ports:
      - "8080:8080"
    environment:
      SERVICE_VERSION: v2
      MONGO_DB_URL: mongodb://mongodb:27017/ratings
      MONGO_DB_USERNAME: ratings
      MONGO_DB_PASSWORD: CHANGEME
  mongodb:
    image: bitnami/mongodb:5.0.6-debian-10-r46
    volumes:
      - "./databases:/docker-entrypoint-initdb.d"
    environment:
      MONGODB_ROOT_PASSWORD: CHANGEME
      MONGODB_EXTRA_USERNAMES: ratings
      MONGODB_EXTRA_PASSWORDS: CHANGEME
      MONGODB_EXTRA_DATABASES: ratings
```

* Commit and push code

## Exercises

### Exercise 1

* Create `docker-compose.yml` and test to run details service and update repository

### Exercise 2

* Create `docker-compose.yml` and test to run reviews service and update repository

### Exercise 3

* Create `docker-compose.yml` and test to run productpage service and update repository

### Challenge 1

* with following structure

```tree
.
├── details
├── productpage
├── ratings
└── reviews
```

* Create `docker-compose.yml` on current path to run all services

## Navigation

* Previous: [Docker](03-docker.md)
* [Home](../README.md)
* Next: [Kubernetes Command Line Workshop](05-k8s-cli.md)
