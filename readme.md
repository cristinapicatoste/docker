# DOCKER & KUBERNETES

## DOCKER

### 1. Complete the Dockerfile

### 2. Build the image 

``` docker build . ```

### 3. Copy the docker image id generated when building the image

### 4. Create a container based on the image created. 
The ` -p machinePort:containerPort ` command must be specified if a port is EXPOSED 

` docker run ` + ` IMAGE_ID `

``` docker run -p 3000:80 652348246475 ``` 

Se debe indicar el puerto de nuestra máquina y el que hemos especificado en el dockerfile.

### 5. See the running containers

` docker ps ` running containers

` docker ps -a ` all containers

### 6. Stop running container

` docker stop ` + ` name of the container `

### 7. Name a container

``` docker run --name NAME CONTAINER_ID ```

### 8. Delete containers by name

``` docker rm NAME ```

Se pueden eliminar varios containers a la vez con el comando ` docker rm ` y los nombres separados por espacios:

``` docker rm pedantic_cray naughty_gates youthful_khayyam epic_rubin nifty_jemison awesome_beaver ```

Eliminar un contenedor tras pararlo y configurarlo al levantarlo:

``` docker run -it --rm --name pythonrng eeb2c1fc3 ```

### 9. Delete images by ID

``` docker rmi IMAGE_ID ```

Eliminar todas las images sin usar en container:

``` docker image prune ```

### 10. List images

``` docker images ```

### 11. Share images

    - Docker Hub

    - Private Registry

### 12. Volume

Para mantener el volume tras eliminar un container,
hay que correr el siguiente comando `-v CONTAINER_NAME:/PATH_CONTAINER`

Ej.:

```docker run -d -p 3000:80 --rm --name feedback-app -v feedback:/app/feedback feedback-node:volume```

Fix error con express:

``` docker run -d -p 3000:80 --rm --name feedback-app -v feedback:/app/feedback -v "/Users/bcome/Desktop/docker-course/5_data-volumes:/app" -v /app/node_modules feedback-node:volume ```