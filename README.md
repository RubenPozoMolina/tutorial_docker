# Tutorial Docker

## 1. Introducción

Docker es una herramienta que permite a los desarrolladores y administradores de sistemas desplegar aplicaciones en entornos de pruebas controlados. La principal ventaja de Docker es que no es necesario instalar ninguna dependencia, todo lo que necesitamos viene integrado en un contenedor. A diferencia de las máquinas virtuales no necesitamos tener una emulación de una máquina física, Docker aprovecha los recursos del sistema anfitrión para ejecutar los contenedores. Esta diferencia es fundamental a la hora de escalar aplicaciones ya que el gasto de recursos es muchísimo menos que si utilizáramos máquinas virtuales.

Para obtener información adicional consultar se pueden consultar los siguientes enlaces:

* [Docker for begginers](https://docker-curriculum.com/)
* [Docker Documentation](https://docs.docker.com/)


## 2. Instalación

Docker se puede instalar en muchas distribuciones de Linux habilitando la virtualización del procesador de la máquina (VT o AMD-V). En el caso de Microsoft Windows tan solo funciona a partir de Windows 10 usando la virtualización por software de Windows ( Hyper-V).

* [Instalación en Windows](https://docs.docker.com/docker-for-windows/)
* [Instalación Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04)
* [Instalación Mac](https://docs.docker.com/docker-for-mac/install/)
* [Instalación documentación oficial](https://docs.docker.com/install/)

## 3. Uso básico

Para saber la versión de docker utilizaremos el siguiente comando:

```
docker version
```

Para poner en marcha un contenedor básico de prueba:
```
docker run --name prueba hello-world
```
Para consultar los contenedores corriendo en nuestra máquina:
```
docker ps
```
Para consultar los contenedores existentes en nuestra máquina:
```
docker ps -all
```
Para borrar el contenedor de prueba:
```
docker rm prueba
```
Para ver las imagenes existentes en nuestro equipo:
```
docker images
```
Para borrar la imagen de prueba
```
docker rmi hello-world
```
A parte de ejecutar docker como un comando también podemos lanzarlo como un servicio, para ello necesitamos imagenes que dejen el docker funcionando:
```
docker run -p 80:80 httpd
```
En este caso el comando no se queda parado

## 4. Creación de imagenes

Hay disponibles muchos repositorios e imagenes preconfiguradas para que podamos realizar nuestro trabajo. Pero muchas veces necesitamos crear nuestra propia imagen para probar o poner en desarrollo nuestras aplicaciones. En este caso crearemos un servicio con Nodejs.

[Tutorial para crear una aplicación con nodejs en docker](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/)

### 4.1 Creación de una aplicación
Creamos fichero de configuración llamado: package.json
```
{
    "name": "docker_web_app",
    "version": "1.0.0",
    "description": "Node.js on Docker",
    "author": "First Last <first.last@example.com>",
    "main": "server.js",
    "scripts": {
      "start": "node server.js"
    },
    "dependencies": {
      "express": "^4.16.1"
    }
  }
```
Creamos el fichero principal de nuestra aplicación: server.js
```
'use strict';

const express = require('express');

// Constants
const PORT = 8080;
const HOST = '0.0.0.0';

// App
const app = express();
app.get('/', (req, res) => {
  res.send('Hello world\n');
});

app.listen(PORT, HOST);
console.log(`Running on http://${HOST}:${PORT}`);
```

### 4.2 Creación de plantilla

Para poder correr nuestra aplicación dentro de una imagen debemos crear un fichero: Dockerfile

```
FROM ubuntu
RUN apt-get update \
    && apt-get install -y apt-utils \
    && apt-get install -y locales \
    && apt-get install -y curl \
    && apt-get install -y gnupg2 \
    && rm -rf /var/lib/apt/lists/* \
    && localedef -i es_ES -c -f UTF-8 -A /usr/share/locale/locale.alias es_ES.UTF-8
ENV LANG es_ES.utf8
RUN curl -sL https://deb.nodesource.com/setup_6.x -o nodesource_setup.sh \
    && chmod +x nodesource_setup.sh \
    && ./nodesource_setup.sh \
    && apt-get install -y nodejs \
    && apt-get install -y npm \
    && apt-get install -y build-essential 
WORKDIR /usr/src/app
COPY package*.json server.js ./
RUN npm install
EXPOSE 8080
CMD [ "npm", "start" ]
```

### 4.3 Construir imagen

Para construir nuestra imagen debemos acceder al directorio de nuestro fichero Dockerfile y ejecutar:

```
docker build -t yourhubusername/node-web-app .
```

Podemos utilizar nuestra imagen ejecutandola en segundo plano mediante:

```
docker run --name=node_test -d -p 8080:8080 yourhubusername/node-web-app
```

## 5. Acceso a contenedores corriendo en segundo plano

Para poder acceder a una máquina que corre en segundo plano podemos abrir una una sesion de consola mediante este comando:

```
docker exec -it node_test /bin/bash
```

También podemos añadir ficheros o traer ficheros del contenedor mediante:

```
docker cp --help
```

Podemos executar comandos sin abrir una sesión de consola:

```
docker exec node_test ls
```

## 6. Compartir imágenes

Una vez desarrollado nuestro contenedor podemos compartir nuestra imagen con otros usuario de [DockerHub](https://hub.docker.com/):

```
docker login --username=yourhubusername
docker push yourhubusername/node-web-app
```







