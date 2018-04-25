# CIDAEN

# Tutorial Docker

### Jacinto Arias (jacinto.arias@uclm.es)

Link a la charla

https://www.youtube.com/watch?v=CJ3Px7mI7kY

---
# Introducción
Un contenedor es una capsula del entorno de ejecución (S.O, software,...)
para convertirlo en una especie de receta

Lo que hacemos con docker es aislar procesos individuales. De manera que se pueda ejecutar de una manera independiente.
Cada uno de los procesos tiene cada una de sus dependencias encapsulados del resto de procesos.

Conceptos básicos: **Docker Engine, Container, Image, Registry, DockerHub, Network, Volume and DockerFile**.

# Comandos básicos de docker

- docker info: Comprobar si funciona docker
- docker run: Ejecuta imágenes. Si es la primera vez la va a descargar de un registro. Después de la imagen podemos poner el comando que queramos que ejecute. Con la opción *--name* podemos darle un nombre a una imagen. Con *--rm* podemos eliminar la imagen al salir (No sea persistente).
- docker ps: Da la lista de los contenedores que se están ejecutando.
- docker ps -a: Da la lista de todos los contenedores que se hayan ejecutado.
- docker stop: Para un contenedor. Pero sigue consumiendo recursos mínimos. El sistema de ficheros sigue ahí.
- docker kill: Elimina un contenedor apagando su so.
- docker rm: Elimina un contenedor que esté parado.
- docker start: Inicia un contenedor parado.

#Imágenes
Punto de inicio. Es un contenedor con nombre y que está guardado de una manera específica.
Normalmente se partirá de una imagen basado en un sistema operativo, salvo que nos interese hacer un SO.
Las imágenes tienen capas (como los ogros), y las mejoras van en una nueva capa sobre lo que ya se ha hecho en la imagen. Finalmente lo último que vería el usuario es una capa de escritura.

#Contenedores
Los contenedores pueden ser sucios, es decir, tienen cosas que no se puedan reproducir. (Se isntala un firewall sobre la imagen). Hay que tener cuidado a la hora de crear contenedores e ir desarrollando sobre ellas sin hacer imagenes de estas.

Se inician a partir de una imagen.

#Registro y DockerHub
Las imagenes se guardan en un repositorio general. Podemos hacer push y pull (como en git).
Permite control de versiones:
*<image-name>:<tag>*
Para usar el registro hace falta hacer una cuenta en: https://hub.docker.com
docker login y docker logout

Ejecutamos: docker run --rm -ti python:3.6-slim

docker image ls

docker image rm /docker rmi (Borra las imagenes locales)

docker rmi <nombre del repositorio>

#Network y Volúmenes
Los contenedores se pueden conectar entre ellos o a otros sistemas mediante redes.
Permite crear puntos de acceso en una vpn para establecer una arquitectura establecida.
Hay que exportar los puertos manualmente.

Docker puede crear volúmenes de datos persistentes que se montan sobre el sistema de ficheros de un contenedor. También podemos montar un directorio local como un volumen de datos.
Tiene dos propósitos esto:
 - Persistencia
 - Compartir datos

---
#Demo 3

docker run -p <host>:<container> <image>

docker run -p 80:80 --rm nginx
*(Vincula el puerto 80 del contenedor al puerto 80 de nuestra máquina)*

docker run -p 80:80 -d nginx
*Ejecuta el contenedor en modo background*

docker run -v <local-dir>:<remote-dir> <image>
*Ruta absoluta de nuestro directorio y la ruta absoluta del directorio del contenedor donde se aloja*
Ejemplo: docker run -v $(pwd):/root --rm -ti ubuntu

Servidor x11 con salida gráfica. Pero en general no vamos a poder ver una interfaz gráfica con docker.

*docker run --rm -d -p 80:80 -v $(pwd)/webA:/usr/share/nginx/html:ro nginx*

*docker run --rm -v $(pwd):/root/ python:3.6-slim python3 /root/very-useful.py jorge*

#DockerFile
Script en el que se define la construcción de una imagen de docker.

Ejemplo:

***FROM ubuntu:14.04
LABEL maintainer="jarias"
RUN apt-get update && apt-get install -y apache2  (Crea una capa)
COPY index.html /var/www/                         (Copia ficheros)
EXPOSE 80                                         (Expone el puerto 80, pero no lo vincula)
CMD ["/usr/sbin/apache2", "-D", "FOREGROUND"]***

docker build -t <namespace>/<image-name>:<tag> <dir>
*(A partir de una dockerFile crea una imagen)*

Cuando creamos una dockerFile estamos creando intención. Estamos incluyendo el software y la intención para la que se va a usar.

Para actualizar cosas del dockerfile, podemos añadir un label llamado *refreshat*.

**Si un contenedor se ejecuta en modo root, el host puede acceder a tus datos.**

Buenas prácticas:

- Contenedor con una función específica.
- Los contenedores son efímeros.
- Minimiza el número de capas.
- Cuidado con la **seguridad**, evita usar root y vigila los puertos.
- Cuidado con la **privacidad**, todas las capas están disponibles.
