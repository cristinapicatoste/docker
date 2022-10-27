FROM node

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

ENV MONGODB_USERNAME=root 
ENV MONGODB_PASSWORD=secret

# Estos datos deben coincidir con los definidos al crear el contenedor de mongo.
# para ello, se pueden/deben indicar al crear el contenedor del backend con el flag -e o -env

EXPOSE 80

CMD ["npm", "start"]


