# Esto sera un Readme.md de DOCKER

###### --> ___Comment:___ Primero que todo, debemos saber que el servicio que hace que docker funcione, se llama daemon docker.
==

##### Para iniciar, usaremos una "image" de docker, que son como plantillas:
```
$ docker run hello-world
```
###### --> ___Comment:___ Como no tenemos esa image o "plantilla" docker usara su repositorio llamada (DockerHub) y descargara esa imagen de alli, llamada hello-world.
==
##### Para ver que contenedores (procesos) esta corriendo el daemon corremos:
```
$ docker ps
```

==
##### Para ver que contenedores (procesos) esta corriendo y/o esten FRENADOS corremos:
```
$ docker ps -a
```

==
##### Si queremos saber mas acerca de un contendor especificamente corremos:
```
$ docker inspect IDContainer o NameProcessContainer
```
###### --> ___Comment:___ Podemos ver el ID y nombre del proceso con docker ps, cualquiera de los 2 nos vale para inspeccionar la estructura de configuracion de nuestro contenedor elegido.
==

##### Si queremos saber el id de nuestro proceso, podemos seguir usando el inspect y corremos:
```
$ docker inspect --format '{{.State.Pid}}' NameProcessContainer
```
==

##### Si queremos correr un contendor con una imagen y a su vez, ese contendor queremos que tenga un nombre precolocado corremos:
```
$ docker run --name ContainerBelloBello hello-world-img
```
###### --> ___Comment:___ Podemos colocarle el nombre que queramos, pero debe ser unico, seguido de la imagen que usaremos, puede estar local o en DockerHub.
==

##### Si queremos FRENAR un contendor corriendo, corremos:
```
docker stop ContainerBelloBello
```
==

##### Para cambiar el nombre de un contenedor que tenemos, corremos:
```
$ docker rename ContainerBelloBello ContainerMedioBello
```
==

##### Si ya no necesitamos un contendor para borrarlo, corremos:
```
$ docker rm IDContainer o ContainerBelloBello
```
==
##### Si queremos crear un container que se AUTODESTRUYA al dejar de utilizarlo, corremos:
```
$ docker rm -f ContainerBelloBello
```
==

##### En caso que tengamos muchos containers apagados o "frenados" y queremos borrarlos todos, corremos:
```
$ docker container prune
```
==

##### Si queremos correr y descargar una image de docker, corremos:
```
$ docker run ubuntu
```
###### --> ___Comment:___ Aqui la image puede ser cualquiera, puede estar local o en dockerhub.
==

##### Para correr he interactuar con un contenedor que creemos a partir de una image, corremos:
```
$ docker run -it ubuntu
```
###### --> ___Comment:___ Si no corremos nuestro contenedor con la image con el flag -it este se ejecutara y se apagara al momento.
==

##### Si queremos basarnos en UNA IMAGEN creada y asignarle un puerto, corremos los siguientes comandos:
```
$ docker stop ID_DeNuestraImagenACopia
```

```
$ docker commit ID_DeNuestraImagenACopia nombreDeNuestroNuevoContainer
```

```
$ docker run -p 8080:3000 -td nombreDeNuestroNuevoContainer
```

###### --> ___Comment:___ Usamos los puertos que queramos exponer, siempre debemos tener el contendor detenido, tambien podemos mapearlo, basa tu info aqui.
https://stackoverflow.com/questions/19335444/how-do-i-assign-a-port-mapping-to-an-existing-docker-container
==

##### Cuando corremos un contenedor a partir de una image con ubuntu, podemos notar que esta se cierra, para que esto no suceda, corremos:
```
$ docker run --name UbuntuSiempreCorriendo -d ubuntu tail -f //dev/null
```
###### --> ___Comment IMPORTANT!:___ Esto sucede cuando la imagen es un bash que cuando termina su proceso, se cierra. Para que eso no sucede corremos los comandos anteriores con los distintos flags: -d para que corra en background y no nos fuerce a usar el contenedor al momento si no que siempre este ahi presente. Luego usamos (tail -f //dev/null ) con este comando al final, forzamos a correr el proceso de bash en infinito y no se cierre su proceso. 
==
##### Si queremos correr nuestro container en backgroud, sin interaccion inmediata, usamos el flag que vimos antes, asi que corremos:
```
$ docker run -d --name proxycontainer nginx
```
###### --> ___Comment:___ Si nos damos cuenta, esta image nginx, es para servidores, aqui se sirve el servidor en el puerto 80... Esta es la exposicion en el CONTENEDOR no en tu pc.
==


##### Para poder correr comandos aparte del principal como por ejemplo una consola de bash, corremos:
```
$ docker exec -it siempreCorriendo bash
```
###### --> ___Comment:___ Lo que logramos con esto, es correr cualquier proceso HIJO al proceso principal de nuestra imag contenedor.
==
##### Para crear un container y que este EXPONGA algo y nosotros lo vinculemos a un puerto de nuestra pc, corremos:
```
$ docker run --name containerProxy -d -p 8080:80
```
###### --> ___Comment:___ Usamos el flag -p que es nuestroPuertoPc:PuertoDelContainer.
==

##### Si queremos ver los logs de nuestros containers que estan en background corriendo, corremos:
```
$ docker logs --tail 10 -f NombreDelProceso
```
###### --> ___Comment:___ Usamos el flag --tail 10 que nos dara 10 lineas de logs de nuestro container corriendo.
==
//////////////////////////////////////////////////
====================================
Ahora veremos manejo de datos
====================================
//////////////////////////////////////////////////
##### Manejaremos datos que vienen de Docker Containers y terminaran en carpetas de MI PC. Para eso usamos, Bind Mounts --> Pero esto es un pelo peligroso en seguridad, corremos:
```
$ docker run -d --name dbcontainerConDatos -v rutaDeMiPc:rutaDeDatosEnELContainer imagenDelContenedor
```
###### --> ___Comment:___ Aqui vemos que corremos en backgroud, le colocamos a nuestro contenedor el flag -v que asocia nuestra ruta donde queremos que el contenedor GUARDE informacion que viene de : Ruta de nuestro Contenedor y por ultimo la imagen que queremos usar. Esto es algo inseguro, por eso usaremos volume.
==
##### Para ver nuestros volumenes primero, corremos:
```
$ docker volume ls
```
==
##### Si queremos crear un volumen, para acumular datos y no exponer una ruta de nuestro pc, corremos:
```
$ docker volume create dataVolumen1
```
==
##### Ahora crearemos un container que asocie los datos que genere la image a nuestro volumen, el cual es manejado por docker y no tiene acceso a nuestros archivos, corremos:
```
$ docker run -d --name dbcontener --mount src=dataVolumen1,dst=/data/db mongo
```
###### --> ___Comment:___  Nuestro flag --mount coloca nuestro volume --mount src=dataVolumen1 y el destino separado por coma , y donde estan los datos a copiar dst=/data/db.
==

# Copiar un archivo y/o Extraer de un Contenedor

<span>Aca usamos la ruta primero a copiar y luego el destino en nuestro container O viceversa.</span>

```sh
 docker cp texto.txt testubuntu:/pruebacarpeta/ka.txt
 o-
 docker cp testubuntu:/pruebacarpeta/ carpetaNueva 
```
<!--Puedes hacerlo al revez, solo cambia el orden.-->
<!--No tiene que estar corriendo el contenedor -->
<!---->

##### Imagenes en Docker
```
docker image ls
```
##### Para traernos una imagen con una version especifica.
```
docker pull ubuntu:20.04
```
##### Para crear una iamgen propia
Creamos un archivo Dockerfile
Y le colocamos:
~~~
FROM ubuntu:lastest
RUN touch /usr/src/hola.txt
~~~
##### Ahora la imagen debemos "buildearla" le colocamos nombre y tag y le decimos donde se va a buildear, como ya estamos parados en al ruta de nuestro archivo de config ponemos .
docker build -t ImagenCandelosa:Yei .

##### Para renombrarla en caso de que nos equivoquemos o USEMOS una imagen superior ejemplo: Ubuntu:
~~~
docker tag ImagenCandelosa:Yei nomb
redeLogin/ImagenCandelosa:Yei
~~~

#### Para entrar en hub docker y subir nuestra imagen.
~~~
docker login
~~~
#### Para mandar a hub docker nuestra imagen usamos 
~~~
docker push nombredeLogin/ImagenCandelosa:Yei
~~~
#### Ahora crearemoos un docker file en nuestro proyecto de node, para contenerlo y poder exponerlo en un puerto especifico.

#### Ahora vamos a la ruta del docker.
WORKDIR /usr/src

#### Copiamos las dependencias
COPY ["package.json","package-lock.json","/usr/src/"]

#### Ahora estando en la ruta del docker, corremos este comando.
RUN npm install

#### copy, copia todos los archivos de este directorio, a la ruta dentro del contendor.
COPY [".","/usr/src/"]

#### Asi, exponemos el contendor a nuestra pc en el puerto
EXPOSE 3000

#### Corremos estos comandos 
CMD ["node","index.js "]

#### Construimos nuestra IMAGEN llamada asi...
docker build -t platziapp .

#### Corremos nuestra imagen que se borrara y la vincularemos al puerto 4300 y los archivos se vinculan (Problema es que solo es un archivo)
```
docker run --rm -p 4300:3000 -v %userprofile%/desktop/carpeta/index.js:/usr/src/index.js  platziapp
```
========================================
### Ahora COMUNICAREMOS 2 contenedores. Node y Mongo
### Para eso usaremos "redes virtuales" las redes de docker
- Primero Dejaremos el docker file tal que asi en nuestro proyecto:
```
FROM node:12
COPY [".", "/usr/src/"]
WORKDIR /usr/src
RUN npm install
EXPOSE 3000
CMD ["node", "index.js"]
```
##### Ahora usaremos las redes, debemos dejar que se atachen a ella: 
```
docker network create --attachable platziNet
```
##### Ahora conectamos a la red los contenedores.
```
docker network connect platziNet contenedor1
docker network connect platziNet aplicacionNode
```
##### Ahora, podemos crear otro contendor, en este caso mi app y vincularle una variable de entorno CONECTADO a la exposicion de mi contendor1 que en este caso tiene mongodb... Hacemos algo tal que asi.
```
docker run -d --name aplicacionNode -p 4300:3000 --env MONGO_UR=mongo://contenedor1:27017/midb platziapp
```
##### Ahroa debemos vincular al segundo contendor.
```
docker network connect platziNet aplicacionNode
```
========================================
========================================
Ahora usaremos Docker Compose para ahorrarnos esos pasos
========================================
- Debemos tener este archivo primero para configurar todo:
    docker-compose.yml
-Se compone asi:
    version: "3.8"
    services: 
        app:
            build: .
            images: platziapp
            environment:
                MONGO_URL: "mongodb://dbap:27017/midb"
            depends_on:
                - dbap
            ports:
                - "4300:3000"
        dbap:
            image: mongo

##### Ahora, corremos el docker y hara toda la chamba en backgrouund para no ver los logs.
```
docker-compose up -d
```
Para borrar TODOS los contenedores
docker rm -f $(docker ps -aq)

//Contenedores, network, imagenes  seran limpiadas.
docker system prune

docker rm -f $(docker ps -aq)

docker run -d --name app --memory 1g imagen

docker rmi Image Image

//> 128 de status son errores.
//ps del ultimo proceso.
docker ps -l
//matar proceso de UNA sola vez.
docker kill container1

//para ver la listas de sus procesos
docker exec container1 ps -ef

shell form  ->hijo del shell
CMD "/.loop.sh"
exec form   -> comando autonomo
CMD ["/.loop.sh"]
