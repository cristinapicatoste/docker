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

### 5. Volumes

Los volumes permiten persistir datos gracias a una carpeta que Docker crea en nuestro local en una ruta que nosotros desconocemos y a la cual no podemos acceder.

#### Comands

` docker volume ls ` lista los volumes.

` docker volume prune ` Elimina todos los volumes anónimos.

` docker volume rm VOL_NAME ` Elimina los volumes anónimos especificados.

` -v CONTAINER_NAME:/PATH_CONTAINER ` Crea un volume con el nombre que le especificamos.

Tipos de volumes:

- Anónimos (instrucción `VOLUME [ "/PATH/PATH" ]` o comando ` -v /PATH`). Docker los nombra con un hash y solo existe mientras el contenedor exista (si ha sido creado con el comando `--rm`), en cuanto este se elimina, el volume también se borra.

- Nombrados (comando `-v CONTAINER_NAME:/PATH_CONTAINER` al crear el container). Con este tipo de volumes los datos persisten tras `-rm` el contenedor, si se crea otro container con el mismo path especificado con el comando `-v` los datos se mantienen, aunque no se pueden editar. 

    Ej.:
    ```docker run -d -p 3000:80 --rm --name feedback-app -v feedback:/app/feedback feedback-node:volume```

### 5. Bind Mounts

El bind mount es una conexión del container a una carpeta de nuestro local.

Soluciona el siguiente problema: los cambios que se realizan en el código no se reflejan automáticamente en el contenedor que está corriendo a no ser que reconstruyamos la imagen.

La principal diferencia con los volumes es que el developer define la ruta local y que los datos almacenados pueden ser editados.

IMPORTANT!
Docker debe tener acceso a la carpeta usada como bind mount.
Se comprueva en la app de ` docker > preferencias > recursos > archivos compartidos `

#### Comands

` -v "ABSOLUTE_PATH:CONTAINER_PATH" ` Se indica la ruta de la carpeta que estamos copiando para construir la imagen (` botón derecho > copy path `). Es aconsejable envolver las rutas entre " " por si contiene algún carácter especial o algún espacio que pueda romper el comando.

El comando monta toda la carpeta como un volume dentro de la carpeta especificada del contenedor.

*Fix error con express:* 

Al crear el bind mount, se borra el código ejecutado por la imagen de Docker y por lo tanto la carpeta de node_modules se elimina y no se puede levantar el proyecto porque le faltan las dependencias.

Para evitar o solucionarlo, creamos otro volume (anónimo en este caso) para que la carpeta node_modules no se elimine.

El comando ` -v /app/node_modules` es equivalente a la instrucción `VOLUME ["/app/node_modules"]`, pero en este caso habría que reconstruir la imagen y el container cada vez.

Gracias a este comando, se le indica a Docker que esta carpeta no debe ser sobreescrita en caso de tener un bind mount.

Ej.:

``` docker run -d -p 3000:80 --rm --name feedback-app -v feedback:/app/feedback -v "/Users/bcome/Desktop/docker-course/5_data-volumes:/app:ro" -v /app/temp -v /app/node_modules feedback-node:volume ```

La extensión `:ro` asegura que esa carpeta solo sea de lectura (read only) y no se pueda sobreescribir. Sin embargo hay otras carpetas que si necesitan ser modificadas (temp y feedback). La carpeta feedback se ha creado como un volume con nombre y su ruta es más específica que /app, por lo tanto no se aplicará el :ro a esa ruta. Para asegurarnos que la ruta /app/temp se puede modificar y sobreescribe el bind mount, tendremos que añadir otro volume, anónimo para que se elimine al parar y borrar el contenedor (gracias al comando --rm).

### 6. `.dockerignore``

.dockerignore nos ayuda a indicarle a docker qué carpetas no debe copiar cuando cree la imagen, por ejemplo la carpeta node_modules si hemos ejecutado `npm i` en nuestro local.

### 7. ENV and ARG

En Docker se pueden utilizar variables de entorno de dos formas: con una instrucción o con comandos.

` ENV name_env value_env ` o `ENV PORT 80` Instrucción que se indica en el Dockerfile para declarar una variable de entorno y su valor. 

` $NAVE_ENV ` o `EXPOSE $PORT` Usar las variables de entorno mediante `$`.

` -env NAME_ENV=VALUE_ENV ` o ` -env PORT=8000 ` Comando que declara el nombre y el valor de una variable de entorno. 

` --env-file PATH_ENV_DOC ` o ` --env-file ./.env ` Comando para 'importar' el archivo .env con todas las variables de entorno. 