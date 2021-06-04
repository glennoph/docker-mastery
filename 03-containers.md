# docker mastery - container assignment

## Create container for nginx port 80, detach (-d), --name nginx

docker run --name nginx -p 80:80 -d nginx

## Create container for httpd (apache) port 8080:80

docker run -d --name apache -p 8080:80 httpd

## Create container for mysql --name db -p 3306:3306

-note: -env MYSQL_RANDOM_ROOT_PASSWORD=yes

docker run -d --name db -e MYSQL_RANDOM_ROOT_PASSWORD=yes mysql

## check running containers

docker container ls

## docker stats

docker container stats

## stop

docker container stop nginx apache db

## shell inside containers

docker container run -it # start new cont. interactively

docker container exec -it # run command in containers

# assignment CLI app testing

* -it option to start shell
* -rm option to remove container when done

## centos

docker container run --rm -it centos:7

* commands: 
  ```
  yum update curl
  curl --version
  ```
  
* result - curl 7.29.0

## ubuntu

docker container run --rm -it ubuntu:14.04

* commands: 
  ```
  apt update && apt install curl
  curl --version
  ```
  
* result - curl 7.35.0


# Assignment DNS Round Robin 

## Instructions
* create virtual network
* create 2 containers from `elasticsearch:2` image
  * use `--network-alias search`
* run `alpine nslookup search`  with `--net` to see the containers list for the same dns name
* run `centos curl -s search:9200`  with `--net`  multiple times

## Commands
```
docker network create rrnet
docker container run -d --net rrnet --net-alias search elasticsearch:2
docker container run -d --net rrnet --net-alias search elasticsearch:2
docker container ls ## list containers, ports include 9200

docker container run --rm --net rrnet  alpine nslookup search
## response:
## Non-authoritative answer:
## Name:	search.Home
## Address: 198.105.244.23
## Name:	search.Home
## Address: 198.105.254.23

docker container run --rm --net rrnet centos curl -s search:9200

## response:
## "name" : "Ororo Munroe",
## ...
## "name" : "Firebrand",

```

## Cleanup
```
docker container ls
docker container rm -f distracted_nobel crazy_lehmann
docker network prune
```
