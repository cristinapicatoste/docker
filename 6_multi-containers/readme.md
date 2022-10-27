# MULTICONTAINER ASSESSMENT

## Instructions

`docker network create multicontainers-net`

`docker run -d --rm --name mongodb -v data:/data/db --network multicontainers-net -e MONGO_INITDB_ROOT_USERNAME=crispi -e MONGO_INITDB_ROOT_PASSWORD=pinguino mongo`

`docker build -t multicontainer-back-node -f backend/Dockerfile ./backend`

`docker run -d --rm --name multicontainer-back -p 80:80 --network multicontainers-net -v "/Users/bcome/Desktop/docker-course/6_multi-containers/backend:/app" -v logs:/app/logs -v /app/node_modules -e MONGODB_USERNAME=crispi -e MONGODB_PASSWORD=pinguino multicontainer-back-node`

`docker build -t multicontainer-front-react -f frontend/Dockerfile ./frontend`

`docker run -d -it -p 3000:3000 --rm --name multicontainer-front -v "/Users/bcome/Desktop/docker-course/6_multi-containers/frontend/src:/app/src" multicontainer-front-react`


## 0. Crear Network 

`docker network create multicontainers-net`

## 1. Container mongo 

Mientras el back y el front no estén dockerizados, se debe exponer el puerto para conectar a mongo. Por defecto es el 27017, pero se debe coinsultar en el dominio ('mongodb://localhost:27017/course-goals'). 

sin network ni autentificación `docker run -d --rm --name mongodb -v data:/data/db mongo` + actualizar domino a 'mongodb://host.docker.internal:27017/course-goals'

con network `docker run -d --rm --name mongodb -v data:/data/db --network multicontainers-net MONGO_INITDB_ROOT_USERNAME=crispi MONGO_INITDB_ROOT_PASSWORD=pinguino mongo` + 'mongodb://mongodb:27017/course-goals' (mongodb porque es el nombre que hemos dado al contenedor de mongo). También hay que incluir el user y el pass en el dominio para poder establecer la conexción y pasar la autentificación (`username:password@host:port` + query `?authSource=admin` osea 'mongodb://crispi:pinguino@mongodb:27017/course-goals?authSource=admin').

*Persistencia de datos* 

Creamos un volume con nombre para persistir los datos y que no se pierdan. Utilizamos una ruta que proporciona la imagen de mongo (ver documentación) donde se almacenan los datos.

*Autentificación* 

Usar las variables de entorno que proporciona mongo: `MONGO_INITDB_ROOT_USERNAME` y `MONGO_INITDB_ROOT_PASSWORD`.


## 2. Imagen back 

`docker build -t multicontainer-back-node .`

## 3. Container back 

sin network `docker run -d -p 80:80 --rm --name multicontainer-back -v "/Users/bcome/Desktop/docker-course/6_multi-containers:/app" -v /app/node_modules multicontainer-back-node`

con network `docker run -d --rm --name multicontainer-back -p 80:80 --network multicontainers-net -v "/Users/bcome/Desktop/docker-course/6_multi-containers/backend:/app" -v logs:/app/logs -v /app/node_modules -e MONGODB_USERNAME=crispi -e MONGODB_PASSWORD=pinguino multicontainer-back-node`

## 4. Imagen front 

`docker build -t multicontainer-front-react .`

## 5. Container front 

sin network `docker run -d -it -p 3000:3000 --rm --name multicontainer-front -v "/Users/bcome/Desktop/docker-course/6_multi-containers/frontend/src:/app/src" multicontainer-front-react`

En tanto que react es una aplicación que se ejecuta en el anvegador, no en el servidor (o el contenedor ), necesitamos un dominio que este entienda, y no docker como en el caso del backend. Por lo tanto, en el caso del front, hay que mantener localhost como dominio.

Por otro lado, hay que exponer los puertos y no hace falta que forme parte de la network.