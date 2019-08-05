


# References

https://github.com/gogs/gogs/tree/master/docker



## List of IPs
 - MariaDB              : 172.19.0.2 ( Ports 22 & 3000)
 - Gogs                 : 172.19.0.3 ( Ports 22 & 3000)

 - Eureka               : 172.19.0.10 (8761)
 --  Rest of 1x reserved for other Eureka instances
 
 - Config Service       : 172.19.0.20 (8888)
 
 - Admin Service        : 172.19.0.30q
 - Gateway(Zuul)        : 172.19.0.40
 
 -- Rest of IPs for backend services
 - Constants Service     : 172.19.0.50
 
 ### Environment variables
export MCSVC_EUREKA_HOST_1=172.19.0.10
export MCSVC_EUREKA_HOST_1_URL="http://$MCSVC_EUREKA_HOST_1:8761/eureka"
export MCSVC_CONFIG_GIT_URL=http://172.19.0.3:3000/mcsvc/mcsvc-infra-config-service-repository.git
export MCSVC_CONFIG_GIT_USERNAME=mcsvc
export MCSVC_CONFIG_GIT_PASSWORD=mcsvc



### Instructions

## Network setup
### Create network 172.19.0.0 dev

```
# Create network 172.19.0.0 dev
docker network create --subnet 172.19.0.0/16 --gateway  172.19.0.1  mcsvc
```
###Install Gogs
```
# Pull image from Docker Hub.
$ docker pull gogs/gogs

# Create volume for Gogs
$ docker volume create gogs-volume

# Use `docker run` for the first time.
$ docker run -d --name=gogs \
    --network mcsvc --ip 172.19.0.3 \
    -v gogs-data:/data gogs/gogs

# First time configuration - select SQLLite database 
    http://172.19.0.3:3000/install
    
# Create user and repository 
    http://172.19.0.3:3000/user/login   
    
    
 
```
###Install MySQL
```
$ docker pull mysql:latest
$ docker create volume mysql-data

docker run --name mysql -d \
    -v mysql_data:/var/lib/mysql \
    --network mcsvc --ip 172.19.0.2 \
    -e MYSQL_ROOT_PASSWORD=supersecret mysql


```

### MySQL DDL
create user 'gogs'@'%' identified by 'gogs';
create user 'constants'@'%' identified by 'constants';
create database gogs;
create database constants;
grant all on gogs.* to 'gogs'@'%';
grant all on constants.* to 'constants'@'%';

### EUREKA

docker run --name mcsvc-infra-eureka-service -d \
    --network mcsvc --ip $MCSVC_EUREKA_HOST_1\
    jbprek/mcsvc-infra-eureka-service:latest
    
### Config Service
docker run --name mcsvc-infra-config-service  -d \
    -e MCSVC_EUREKA_HOST_1_URL \
    -e MCSVC_CONFIG_GIT_URL -e MCSVC_CONFIG_GIT_USERNAME -e MCSVC_CONFIG_GIT_PASSWORD \
    --network mcsvc --ip 172.19.0.20 \
    jbprek/mcsvc-infra-config-service:latest


### Constants Service     : 172.19.0.50
docker run --name mcsvc-util-constants-service  -d \
    -e MCSVC_EUREKA_HOST_1_URL \
    --network mcsvc --ip 172.19.0.50 \
    jbprek/mcsvc-util-constants-service :latest
