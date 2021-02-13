# Docker

## 1. Intro a Docker

Herramienta para correer aplicaciones en un entorno aislado.

Parecido a una máquina virtual pero consume muchos menos recursos y espacio de disco.

Todo docker corre en el mismo entorno, lo que permite migrar contenido a otros equipos.

Se está convirtiendo en un estándar en desarrollo de software

No necesita un sistema operativo.




## 2. Imágenes, Contenedores y puertos
Un **contenedor** es una abstracción en la capa de aplicación que contiene paquetes de código y dependencias juntas. Varios contenedores pueden correr en un mismo sistema operativo compartiendo el kernel pero con procesos puramente aislados unos de otros. 

Una **imagen** es una plantilla para crear un entorno particular.
Es un Snapshot (Permite volver hacia atrás, actualizar, etc). Contiene todo el SW necesario para que la aplicación corra.

Un contenedor es una instancia de una imagen.

### Comandos básicos de Docker

        docker pull <image>       %Coger una imagen de docker hub
        docker image              %Ver las imágenes

        docker run <nombre:tag>    %Lanzar un contenedor

Para que  no se nos quede un proceso colagado y tengamos que otra terminal, echamos mano de -d.       

        docker run -d <nombre:tag>    %Lanzar un contenedor detatch mode

        docker container ls           %Ver el contendor

### Puertos
El servidor estará escuchando desde un puerto en particular. En nuestro ejercicio se utiliza la imagen de nginx que emplea el puerto 80. Yo puedo conectar uno o varios puertos de mi dispositivo a ese puerto en particular haciendo uso del comando -p.
        
        docker run -d -p <puerto cliente>:<puerto servidor> <imagen>

        Ejemplo: docker run -d -p 8080:80 -p 3000:80 nginx:latest

### Gestionar contenedores

Para parar un contenedor:

        docker stop <nombre o ID>

Para rearrancarlo:

        docker start <nombre o ID>

Para borrar los contendores, no basta con stop, puesto que se quedan ahí. Para ello hay que echar mano de los siguientes comandos:

        docker ps -aq  %Me enlista los ids de todos los contendores 
        docker rm -f $(docker ps -aq)   %Para borrarlos todos

### Nombrar contenedores

Para nombrar los contenedores solo hace falta introducir --name a la hora de crearlo:

       docker run --name contenedor1 -d -p 2000:80 nginx:latest 

Para que cuando se nos muestre un contenedor, éste sea de la forma más vistosa posible, lo haremos pasándole el comando --format
       
       docker ps --format="ID\t{{.ID}}\nNAME\t{{.Names}}\nIMAGE\t{{.Image}}\nPORTS\t{{.Ports}}\ nCOMMAND\t{{.Command}}\nCREATED\t{{.CreatedAt}}\nSTATUS\t{{.Status}}\n"

Para no tener que escribir este churro de carcteres, exportamos una variable donde guardamos todos y volvemos a ejecutar.

        export FORMAT="ID\t{{.ID}}\nNAME\t{{.Names}}\nIMAGE\t{{.Image}}\nPORTS\t{{.Ports}}\ nCOMMAND\t{{.Command}}\nCREATED\t{{.CreatedAt}}\nSTATUS\t{{.Status}}\n"

        docker ps --format "$FORMAT"

¡¡¡¡OJO, EL SIGNO = TIENE QUE IR JUNTO!!!!!! (Sin espacios) 

## 3. Volúmenes
Nos permite compartir info entre contedores, y entre host y contenedor.

Cada contenedor nos da las propias indicaciones sobre los vólumenes en la propia página web de docker hub.

En el ejemplo de nginx:

        docker run --name some-nginx -v /some/content:/usr/share/nginx/html:ro -d nginx


donde la parte /some/content hace referencia a la **carpeta del host** y usr/share/nginx/html al **contenedor**.

### Compartir entre host y contenedor

Para ello, se debe primero acceder a la página web y mirar los comandos como se describía en el apartado anterior.

Una vez hecho eso, se levanta un contendor en el mismo sitio donde están los archivos que queremos compartir.

        docker run --name contenedor1 -v $(pwd):/usr/share/nginx/html:ro -d -p 2000:80 nginx

donde  pwd es el directorio en el que me encuentro y el otro es donde se encuentra el servidor. ro significa read only, pero tb se le pueden pasar otros parámetros.

Desde dentro del contenedor también se pueden crear archivos que se pueden pasar al host.

                docker exec -it <nombre del contenedor> bash

                En el ejemplo: docker exec -it contenedor1 bash

*-it* significa que le queremos pasar algo en modo interactivo y accedo dentro del contenedor 

Así comparto de contenedor a usuario del ordenador. Esto es muy importante, y se debe tener en cuenta de que no se puede lanzar el contenedor con el parámetro ro (read only).

<br>

### Editar la página web
 Me puedo descargar templates desde bootstrap y editar desde ahí.

 ### Compartir ficheros entre dos contenedores

 Existe un comando para realizar esta función, que es introducir la variable 
                
                --volume-from <cotenedor fuente>

La orden completa en el ejercicio que venimos haciendo sería:

                docker run --name contenedor2 --volumes-from contenedor1 -d -p 3001:80 nginx

Siendo contenedor2 el nuevo creado. De esta manera, creamos un contenedor que contendrá lo mismo que contiene el 1.

<br>

## 4. Construir imágenes

Dockerfile nos permite construir una imagen a partir de este documento. Contiene una serie de directrices.
Hasta ahora, nos habíamos descargado las propias imágenes de internet.

La imagen debe contener todo lo necesario para disponer de una aplicación

FROM es el primer comando a utilizar. La gran mayoría de las ocasiones no se construirá una imagen desde 0, sino desde una imagen base a partir de la cual crearemos la nuestra.
ADD permite añadir los ficheros de nuestro host al contenedor (montar volúmenes).

El comando para montar la imagen, el comando a ejecutar es:

                docker build --tag <nombre_imagen:tag> <path del directorio donde está el Dockerfile>


## 5. Docker con Node

También se pueden crear API's utilizando node.js. Para ello, podemos hacer uso de express (de node.js).

### Más comandos básicos de DOCKER.
El comando FROM:
                FROM    node:lastest   %me bajo la imagen de node
El comando WORKDIR:

                WORKDIR /app 

Nos permite crear una carpeta (en este caso app) dentro del contenedor en caso de que no exista y que haga uso de ella.
Todas las líneas que sigan a esta sentencia se ejecutaran dentro de la carpeta indicada en WORKDIR.

                ADD . <directorio actual> /usr/share/nginx/html <directorio destino>
                RUN npm install   %para instalar express
                CMD node index.js   %llama a la línea de comando y corre la orden

También existen otros comandos como COPY

                COPY <ruta directorio host> <rura contenedor>


### Caché
Cuando  se han creado nuevas imágenes a partir de un docker file, el proceso de carga puede ser longevo. Por eso cuando no es la primera vez, que esto sucede, docker echa mano de la caché. 

Gracias a esta funcionalidad, sólo se cargarán las dependencias nuevas o que se hayan cambiado, ahorrando mucho tiempo en el proceso.

<br>

## 6. Alpine (Docker)

Alpine son versiones de imágenes que ocupan muy poco tamaño.

Para obtener este tipo de versiones basta con ir a dockerhub y buscar en el propio perfil de la imagen la versión de alpine y hacer un pull:

                docker pull <imagen>:alpine

Para crearnos nuestra imagen sobre ésta, habrá que modificar el propio archivo de dockerfile.

Luego creamos el contenedor. En este caso, el contenedor es node, cuya aplicación escucha por defecto en el puerto 3000.


<br>

## 7. Tags, Veriones y Tagging (Etiquetas)
Las etiquetas nos permiten comprobar la versión de las imágenes que tenemos. Nos aseguramos de que no nos afecten los cambios.

                docker run --name nodeAlpine -d -p 2000:3000 user-api-service:latest 

Cada vez que construimos una imagen sobre un mismo dockerfile, se sobreescribe la anterior. Esto desemboca en una imagen none.

### Buen etiquetado de las imágenes

Para crear varias versiones de una misma imagen, podemos echar mano de **tag**. Tag nos permite "clonar" una misma imagen cambiando la etiqueta. 
                
                docker tag <imagen:tag1> <imagen:tag2>
                docker tag nodealpine:ultima nodealpine:1

En etse momento tenemos dos imágenes iguales con distinto nombre, a partir de las cuales prodremos hacer múltiples cambios y generar contenedores de cada una de ellas.

Esto es una herramienta muy buena para trabajar con fallos. Podemos crerar nuevas versiones de las imágenes sobre las que operaremos sin miedo a equivocarnos. 

Sería un símil a las ramas de Git.

<br>

## 8. Registros de docker 