# 04 image 

## 04 image assignment

Complete Dockerfile, build, and test
* source in 04-image dir

### Command
```
docker build -t testnode .

## tagged testnode:latest

```
### Test
* run 
```
 docker container run --rm -p 80:3000 testnode 
```
* point browser to http://localhost  (pic)


# Clean system

## Prune images - clean images
```
docker image porune
```
## Prune System - everything
```
docker system prune
```


