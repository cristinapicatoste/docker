FROM node:14

WORKDIR /app
# Indica a Docker que todos los comandos a posteriori
# deben ejecutarse en la carpeta especificada

COPY package.json /app
# archivo + carpeta en la que se copia
# al copiar el package.json primero, nos permite instalar npm antes de 
# copiar el código y que se guarde en cache.
# De este modo, al hacer build de nuevo, el proceso es mucho más rápido

RUN npm install

COPY . /app
# COPY . . 

# Punto 1: Host file systyem
# El primer . indica a docker que haga una copia de 
# los ficheros que se encuentran en la misma carpeta 
# que el Dockerfil

# Punto 2: image/container file systyem
# El segundo . es la ruta dentro del contenedor 
# donde se almacena la copia

EXPOSE 80
# Puerto en el que correrá el container

CMD ["node", "server.js"]
# CMD = command
# Último comando en la imagen para 'levantar' el proyecto
# Se ejecuta cuanto el contenedor se ha creado, basado en la imagen

