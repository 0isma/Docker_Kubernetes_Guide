# Docker

## 1. Intro a Docker

Herramienta para correer aplicaciones en un entorno aislado.

Parecido a una máquina virtual pero consume muchos menos recursos y espacio de disco.

Todo docker corre en el mismo entorno, lo que permite migrar contenido a otros equipos.

Se está convirtiendo en un estándar en desarrollo de software

No necesita un sistema operativo.


<br>


<img src="./pictures/1 Docker vs VM.png"
     alt="Markdown Monster icon"> 



<br>


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
       
       docker ps --format = "ID\t{{.ID}}\nNAME\t{{.Names}}\nIMAGE\t{{.Image}}\nPORTS\t{{.Ports}}\ nCOMMAND\t{{.Command}}\nCREATED\t{{.CreatedAt}}\nSTATUS\t{{.Status}}\n"

Para no tener que escribir este churro de carcteres, exportamos una variable donde guardamos todos y volvemos a ejecutar.

        export FORMAT = "ID\t{{.ID}}\nNAME\t{{.Names}}\nIMAGE\t{{.Image}}\nPORTS\t{{.Ports}}\ nCOMMAND\t{{.Command}}\nCREATED\t{{.CreatedAt}}\nSTATUS\t{{.Status}}\n"

        docker ps --format "$FORMAT"


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

<br>

<img src="./pictures/20. Montar un volumen en el otro.png"
     alt="Markdown Monster icon"> 


<br>



