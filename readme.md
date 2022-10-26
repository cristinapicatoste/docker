# DOCKER & KUBERNETES

## DOCKER

### 1. Images

La image se crea a partir del contenido del Dockerfile.
En el Dockerfile se indican los lenguajes y los comandos necesarios para levantar el proyecto. De este modo no hace falta instalarse las dependencias en la máquina local, sino que se corren en el contenedor de Docker.

#### Principales instrucciones

` FROM ` indica la imagen padre sobre la que construiremos el proyecto (Node, Python, Java...).

` WORKDIR FOLDER_PATH ` creación de una carpeta en la que Docker ejecutará las instrucciones que indiquemos a posteriori.

` COPY FILE_NAME FOLDER_PATH ` instrucción para copiar archivos o carpetas.

` RUN ` intrucción para ejecutar comandos.

` EXPOSE ` para indicar el puerto en que corre el proyecto.

` CMD [ "node", "app.mjs" ] ` instrucción para levantar el proyecto. Última instrucción, solo debe haber un CMD por Dockerfile

#### Commands

` docker build . ` Crea una imagen basada en el Dockerfile sin nombre.

` docker build -t PROJECT_NAME:VERSION . ` Crea una imagen basada en el Dockerfile con el nombre del proyecto / imagen y la versión gracias al comando -t (tag) .

` docker images ` lista todas las imágenes que tenemos creadas.

` docker rmi IMAGE_ID_OR_IMAGE_NAME ` Elimina imágenes por Id o por Nombre. Se pueden borrar varias a la vez separando por un espacio los ids o los nombres. Las imágenes solo se pueden borrar si el contenedor en la que corre está parado.

` docker image prune ` Eliminar todas las images huérfanas, es decir que no están siendo ejecutadas por ningún container.

### 2. Containers

#### Commands

` docker run IMAGE_ID ` o ` docker run 652348246475 ` Crea el contenedor basado en la imagen indicada (id o nombre). En este caso no se indica el puerto, solo usar para proyectos que no lo necesiten o no estén usando puerto.

` docker run -p machinePort:containerPort IMAGE_ID_OR_NAME ` o  ` docker run -p 3000:80 652348246475 ` Crea el contenedor basado en la imagen indicada (id o nombre) y se indican los puertos (local_port:container_port). Si no se especifica el puerto de nuestro local, no se podrá acceder al proyecto. El puerto del contenedor debe coincidir con el EXPUESTO en el Dockerfile.

` docker run -p 3000:80 --name CONTAINER_NAME 652348246475` El comando ` --name ` nos permite poner nombre al contenedor. Si no se usa, Docker genera uno automáticamente y se puede ver accediendo al listado de contenedores.

` docker run -it --rm --name pythonrng 652348246475 ` El comando ` --rm ` elimina el contenedor cuando este se para.

` docker ps ` muestra solo los contenedores que están corriendo.

` docker ps -a ` muestra todos los contenedores creados.

` docker stop CONTAINER_NAME_OR_ID ` para el contenedor especificado.

` docker start CONTAINER_NAME_OR_ID ` corre el contenedor especificado.

` docker rm CONTAINER_NAME_OR_ID ` elimina el contenedor especificado, debe haber sido parado previamente. Se pueden eliminar varios contenedores a la vez separandolos por un espacio.

### 3. Share images

    - Docker Hub

    - Private Registry

### 4. Volumes & Bind Mount

Docker tiene varios sistemas de almacenaje de datos: volumes (gestionados por docker) and bind mounts (gestionados por developer).

#### Volumes

Pueden ser anónimos (instrucción `VOLUME [ "/path/path" ]`) o nombrados (comando -v al crear el container).
Con los volumes, Docker crea una carpeta en nuestro local, pero no sabemos dónde, ya que solo hemos especificado la ruta en la que estará la carpeta dentro del container.

Los volumes permiten persistir datos.
Es una carpeta que docker genera dentro de nuestro local.
Cuando se elimina el contenedor, se elimina dicha carpeta (si se ha creado con el comando --rm).

Los volumes pueden ser anónimos o se pueden nombrar `-v CONTAINER_NAME:/PATH_CONTAINER`

Para mantener el volume tras eliminar un container,
hay que correr el siguiente comando `-v CONTAINER_NAME:/PATH_CONTAINER`

Ej.:

```docker run -d -p 3000:80 --rm --name feedback-app -v feedback:/app/feedback feedback-node:volume```

Fix error con express:

``` docker run -d -p 3000:80 --rm --name feedback-app -v feedback:/app/feedback -v "/Users/bcome/Desktop/docker-course/5_data-volumes:/app" -v /app/node_modules feedback-node:volume ```