Simple setup using Windows dD=====

PORTS Exposed
--------------
| Service                       |Port|Admin|
|:------------------------------|----|-----|
|gogs                           |3000|-    |
|mcsvc-infra-eureka-service     |8761|18761|
|mcsvc-infra-config-service     |8888|18888|
|mcsvc-util-constants-service   |9000|19000|


GOGS
----
docker pull gogs/gogs
docker run --name=gogs -p 10022:22 -p 3000:3000 -v gogs-data:/data gogs/gogs

john/dada

DISCOVERY mcsvc-infra-eureka-service
------------------------------------
export MCSVC_DISCOVERY_HOST=host.docker.internal
export DISCOVERY_PORT=8761
docker run --name mcsvc-infra-eureka-service -d -p8761:8761 -p18761:18761 \
    -e MCSVC_DISCOVERY_HOST -e DISCOVERY_PORT \
    jbprek/mcsvc-infra-eureka-service:latest

CONFIG mcsvc-infra-config-service
----------------------------------

export CONFIG_SERVICE_ENCRYPT_KEY=abracadabra
export MCSVC_DISCOVERY_HOST=host.docker.internal
export DISCOVERY_PORT=8761
export MCSVC_CONFIG_GIT_URL=http://host.docker.internal:3000/john/mcsvc-infra-config-service-repository.git
export MCSVC_CONFIG_GIT_USERNAME=john
export MCSVC_CONFIG_GIT_PASSWORD=dada

docker run --name mcsvc-infra-config-service -d -p8888:8888 -p18888:18888 \
    -e MCSVC_DISCOVERY_HOST -e DISCOVERY_PORT \
    -e CONFIG_SERVICE_ENCRYPT_KEY \
    -e MCSVC_CONFIG_GIT_URL -e MCSVC_CONFIG_GIT_USERNAME -e MCSVC_CONFIG_GIT_PASSWORD jbprek/mcsvc-infra-config-service

EUREKA
------