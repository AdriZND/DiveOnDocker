# DiveOnDocker
### Índice
[1. ¿Qué es Docker?](#qué-es-docker)

[2. Instalar Docker](#instalar-docker)

[3. Verificar instalación](#verificar-instalación)

[4. Como utilizar Docker](#como-utilizar-docker)

[5. Buenas prácticas](#buenas-prácticas)

Vamos a hacer una pequeña guía de qué es docker, como se utiliza y para que sirve, asi como una serie de consejos y buenas practicas para hacer su uso más facil.

Gran parte de la información de esta guía se basa en la [Documentación oficial](https://docs.docker.com/get-started/) de Docker, y aconsejo acudir a ella con cualquier duda ya que es bastante buena.

## ¿Qué es Docker?
Docker es una plataforma que facilita el desarrollo, envío y ejecución de aplicaciones, permitiendo separar las aplicaciones de la infraestructura para una entrega rápida de software y una gestión unificada. Sus metodologías reducen el tiempo entre la escritura del código y su ejecución en producción. 

Sus elementos más importantes son las [imágenes](https://docs.docker.com/guides/docker-concepts/the-basics/what-is-an-image/) y los [contenedores](https://docs.docker.com/guides/docker-concepts/the-basics/what-is-a-container/)

Haciendo un breve resumen y una comparación con algo que nos puede resultar más familiar, una imagen seria como una clase y un contenedor seria como una instancia de esa clase. 

Imagina que tenemos una aplicación simple que nos permite registrar usuarios en una base de datos y esta dividida en un frontend de Vue 3, un backend en Nodejs y Express y una base de datos en MySQL. Gracias a Docker podriamos crear una imagen para cada una de las partes y luego quien quisiera podria hacer uso de esas imagenes para desarrollar o desplegar la aplicación a su gusto, sin tener que preocuparse de las dependencias y/o problemas de compatibilidad ya que los contenedores se nutren de las imagenes para funcionar de manera aislada y agnostica al entorno local en el que nosotros estemos trabajando.


## Instalar Docker
Lo primero que vamos a necesitar es instalar Docker en nuestra distribución de Debian en WSL 2. Para realizar esto tenemos dos alternativas, si vais a la documentación oficial vereis que recomiendan instalar Docker Desktop para poder tener una GUI que nos facilite la visualización y configuración de nuestras imagenes y contenedores etc, lo cual es bastante más intuitivo, sobretodo a la hora de  gestionar aplicaciones que utilizan varios contenedores a la vez. La otra opción es instalar Docker Engine directamente y trabajar todo a través de la CLI de docker. Las dos maneras son válidas y vamos a ver las dos, si bien es recomendable trabajar con la CLI aunque tengamos Docker Desktop en la medida de lo posible para familiarizarse con ella y con los comandos, y servirnos de Docker Desktop para visualizar mejor las cosas. 

### Docker Desktop

##### Configurar WSL 2
Lo más seguro es que esto ya lo tengamos hecho pues para utilizar Debian en Windows nos hace falta pero si no tenemos que instalar WSL y asegurarnos de actualizarlo a su versión 2 y utilizar esta.

##### Descargar e instalar Docker

Una vez tenemos WSL 2 simplemente tenemos que hacernos con la última versión de Docker Desktop e instalarla. 
Docker Desktop incluye por defecto las siguientes  funcionalidades o herramientas:

-Docker Engine

-Docker CLI client

-Docker Scout

-Docker Build

-Docker Extensions

-Docker Compose

-Docker Content Trust

-Kubernetes

-Credential Helper

Descargamos la ultima versión de [Docker Desktop](https://docs.docker.com/desktop/install/windows-install/) desde la web oficial de Docker.

Una vez se descargue simplemente ejecutamos el instalador y nos aseguramos de que cuando nos pregunte marcamos la opción de "Use WSL instead of Hyper-V"

En caso de que ya tengamos descargado Docker y queramos cambiar la configuración o añadamos alguna nueva distribución que queramos habilitar para Docker en WSL debemos hacer lo siguiente.

En el menú de Docker Desktop nos dirigimos a la pestaña de Settings, arriba a la derecha. Y vamos a Resources > WSL Integration

Debemos tener marcada la opción de "Enable integration with my default WSL distro"

Debajo podemos además marcar distros adicionales que queramos integrar, en mi caso veis que Debian no esta marcada a pesar de ser la que utilizo, eso es porque ya de por si Debian es mi distribución por defecto.

[![desktop-enable.png](https://i.postimg.cc/tChC1wwD/desktop-enable.png)](https://postimg.cc/7fZydsxT)

Aplicamos cambios guardamos y si queremos reiniciamos la herramienta.

También tenemos que asegurarnos de que la opción de "Use WSL 2 based engine" está marcada en la pestaña general de settings

[![desktop-wsl2.png](https://i.postimg.cc/RVRxxBjr/desktop-wsl2.png)](https://postimg.cc/YhhsQJr8)

### Docker Engine
De esta manera prescindimos de la GUI, si tu editor de código es VS Code tienes una [extensión](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) que puedes instalar.

##### Configurar el repositorio apt de Docker e instalar
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
## Verificar instalación
Hayamos seguido una u otra instalación deberiamos seguir los siguientes pasos para confirmar que ha quedado correctamente instalado y funcionando.

##### Manejar Docker sin ser root
```
# En Debian
# Sustituir $USER por tu usuario, despues de este paso conviene reiniciar Debian para que se aplique

sudo usermod -aG docker $USER
```
##### Verifica que se ha instalado
```
docker --version
```
##### Verifica que se ha iniciado
```
sudo service docker status
```
##### Hello World from Docker
```
docker run hello-world
```
###### Nos devolverá algo semejante a esto

[![Hello-world.png](https://i.postimg.cc/VvjkXd8T/Hello-world.png)](https://postimg.cc/BPn30Ztc)

Si estamos utilizando Docker Desktop podemos ver como se muestran tanto el contenedor que hemos ejecutado, que ahora ya ha terminado su ejecución.

[![Contenedores-desktop.png](https://i.postimg.cc/3RHpc38z/Contenedores-desktop.png)](https://postimg.cc/ns0CsbQ1)

Como la imagen que hemos descargado y de la cual se ha creado el contenedor.

[![Imagenes-desktop.png](https://i.postimg.cc/T104PkNY/Imagenes-desktop.png)](https://postimg.cc/tn15SzHL)

Como puedes apreciar en mi caso aparece otra imagen adicional que he utilizado con anterioridad siguiendo la [guia de introducción](https://docs.docker.com/get-started/) oficial de Docker, muy completa y que ayuda a familiarizarse con la herramienta.

Por otra parte también podemos acceder a toda esta información a traves de la **CLI de Docker**.

En la terminal ejecutamos el siguiente comando 

`docker image ls`

Lo cual nos muestra todas las imagenes que tenemos en nuestro equipo

y despues 

`docker ps`

Seguramente veas que no aparece nada, esto se debe a que `docker ps` solo nos muestra los contenedores que se estan ejecutando en este momento, y hello-world ya terminó su ejecución. Para ver todos los contenedores debemos hacer

`docker ps -a` o en su defecto `docker ps -all`

[![cli-contenedores-imagenes.png](https://i.postimg.cc/fb3FzfhY/cli-contenedores-imagenes.png)](https://postimg.cc/Ff4GGczK)


Con esto en principio deberiamos tener la instalación de docker hecha.

## Como utilizar docker

Ahora que tenemos Docker instalado vamos a proceder a dockerizar nuestra aplicación. Voy a tomar de referencia [esta aplicación](https://github.com/AdriZND/ProyectoFormativo) que desarrollé. Consiste en un Frontend de Vue 3 + Vite, un Backend en ExpressJs y Node y una base de datos MySQL que utilizamos mediante el ORM de Sequelize.

#### ¿Qué queremos conseguir?
Nuestra intención es ser capaces de lanzar la aplicación tanto en modo de producción como de desarrollo a través de [Docker Compose](https://docs.docker.com/compose/), es una herramienta que nos permite lanzar varios contenedores a la vez e interconectarlos para trabajar a la vez con ellos.

**[1. Crear Dockerfiles para Frontend, Backend y BDD](#1-crear-dockerfiles-para-frontend-backend-y-bdd)**

Las [Dockerfiles](https://docs.docker.com/guides/docker-concepts/building-images/writing-a-dockerfile/) son los archivos que nos permiten construir imagenes para luego poder ser utilizadas a traves de contenedores.

**[2. Crear archivos docker-compose para lanzar la app.](#2-crear-archivos-docker-compose-para-lanzar-la-app)**

El [docker-compose.yaml](https://docs.docker.com/compose/compose-application-model/) nos permite lanzar a la vez diferentes contenedores basados en sus correspondientes imagenes y con sus configuraciones.

**[3. .dockerignore](#3-dockerignore)**

Fichero similar a un .gitignore para que determinadas cosas no se vean incluidas en la imagen, como por ejemplo las dependencias.

**[4. Ejecutar docker compose](#4-ejecutar-docker-compose)**

**[5. Subir, descargar y utilizar imagenes de otros.](#5-subir-descargar-y-utilizar-imagenes-de-otros)**

Una vez hemos aprendido a crear nuestras imagenes y a lanzar una aplicación multi-contenedor es conveniente aprender a subir las imagenes a un registro para que otras personas puedan utilizarlas.

**[6. Desarrollar aplicación desde cero con Docker.](#6-desarrollar-aplicación-desde-cero-con-docker)**

Aprende a integrar Docker en tu desarrollo desde cero.


### 1. Crear Dockerfiles para Frontend, Backend y BDD
El primer paso para poder desplegar o desarrollar nuestra aplicación con Docker es crear las Dockerfiles, son archivos de configuración separados por ordenes o instrucciones que se van ejecutando por [capas](https://docs.docker.com/build/guide/layers/) para poder formar la imagen. 
Esas capas van almacenandose en caché de manera que si más tarde queremos actualizar la imagen porque hemos hecho un cambio en una linea de código, solo la capa que se vea afectada por ello tendra que ser reconstruida y añadida a la imagen, acelerando el proceso de ejecución de la misma.

<figure>
<img src="https://docs.docker.com/build/guide/images/layers.png" alt="Dockerfile layers">
<figcaption>Imagen perteneciente a la documentación de docker.</figcaption>
</figure>

Algunas de las instrucciones más comunes que se utilizan en las DockerFiles son las siguientes, para ver todas acude a la [referencia de Dockerfile](https://docs.docker.com/reference/dockerfile/)

`FROM <image>` - Especifica la imagen base que se va a utilizar

`WORKDIR <path>` - Establece el "directorio de trabajo" o path de la imagen en el que se va a trabajar.

`COPY <host-path> <image-path>` copia archivos del host en el path especificado y los pega en el path de la imagen

`RUN <command>` ejecuta el comando especificado

`ENV <name> <value>` asigna o crea variables de entorno que el contenedor utilizará

`EXPOSE <port-number>` indica el puerto que a la imagen le gustaria exponer

`CMD ["<command>", "<arg1>"]` establece el comando por defecto que ejecutara un contenedor que utiliza esta imagen. 
[Diferencias entre RUN, CMD y ENTRYPOINT](https://dev.to/kittipat1413/docker-run-vs-cmd-vs-entrypoint-demystifying-the-differences-2a4p)

Ahora que sabemos un poco más sobre las Dockerfiles y como se construyen vamos a ver como he Dockerizado la aplicación.
La estructura del proyecto es la siguiente:

[![estructura-proyecto.png](https://i.postimg.cc/fb4WT2SN/estructura-proyecto.png)](https://postimg.cc/SnrbgGGT)

**Dockerfile del backend**

[![backend-dockerfile.png](https://i.postimg.cc/CxDjP2jB/backend-dockerfile.png)](https://postimg.cc/5X4YjnKb)

Como vemos en lugar de utilizar simplemente `COPY . .` para copiar todos los archivos de golpe realizamos dos `COPY` en el primero copiamos el package.json y el package-lock.json y en el segundo el resto de los archivos. Esto es una **buena practica** ya que son archivos que no suelen modificarse tanto y nos evita tener que copiarlos e instalar las dependencias cada vez que se construya la imagen, a no ser que hayamos incluido alguna nueva dependencia. Solamente copiariamos el resto de archivos para implementar nuevas modificaciones de código etc.

**Dockerfile del Frontend para desarrollo**

[![frontend-dev-dockerfile.png](https://i.postimg.cc/sxQBBBmQ/frontend-dev-dockerfile.png)](https://postimg.cc/kRCMzgkq)

Como observamos es practicamente igual a la del backend, a diferencia del comando que se lanza al construir el contenedor `npm run dev` para lanzar el frontend de Vue 3 + Vite en modo desarrollo.
Si haceis esto y ves que no se muestra el frontend en el puerto adecuado seguramente sea porque teneis que configurar Vite correctamente, tenemos que tener en cuenta que estamos usando el frontend a través de un contenedor y no de nuestro propio localhost. 
Por lo que tenemos que añadir esto a la configuración de Vite
```
server: {
    host: '0.0.0.0', 
    port: 5173,     
  },
```
Para asi indicarle a que escuche otras direcciones.

**Dockerfile del Frontend para producción**

[![frontend-prod-dockerfile.png](https://i.postimg.cc/Qx79hTVv/frontend-prod-dockerfile.png)](https://postimg.cc/svVDJ1YJ)

Puesto que cada contenedor debe estar lo más aislado posible y desempeñar solo una función vamos a tener frontend y backend en contenedores separados. Por ello vamos a utilizar [nginx](https://hub.docker.com/_/nginx) para servir los archivos estaticos de la build del frontend.

El archivo nginx.conf se ve tal que así

[![nginx-conf.png](https://i.postimg.cc/c4x2NQDL/nginx-conf.png)](https://postimg.cc/0r4cYJRT)

Es una configuración por defecto que practicamente no tenemos que cambiar, si queremos retocar algo acudir a la documentación de nginx.
Lo unico que he cambiado aqui ha sido el "server_name" para establecer la dirección del backend.

**Resumen**

Con esto tenemos todas las Dockerfiles que necesitamos para desplegar la aplicación tanto en producción como en desarrollo. Podemos pensar que falta aun la configuración de la BDD y otras características pero eso lo realizaremos todo en los archivos de docker-compose.

### 2. Crear archivos docker-compose para lanzar la app.
Docker Compose es una herramienta que nos permite manejar aplicaciones multi-contenedor. Lanzar varios contenedores especificando para cada uno la imagen que queramos y la configuración deseada. Así como crear [bind-mounts](https://docs.docker.com/storage/bind-mounts/) para monitorizar los cambios en los documentos, [volumenes](https://docs.docker.com/storage/volumes/) para almacenar los datos de los contenedores e implementar [networks](https://docs.docker.com/network/) para que los contenedores puedan comunicarse entre si.

Si bien Docker Compose hace gran parte del trabajo por nosotros y nos simplifica mucho la configuración de todas estas cosas es importante al menos saber a que se refieren estos conceptos por lo que recomiendo echar un vistazo a la documentación antes de continuar.

Docker compose funciona a traves de **servicios** cada uno de estos servicios hace referente a una parte o funcionalidad de nuestra aplicación que se ejecutará en un contenedor aislado el cual esta basado en una imagen.

Cuando un contenedor es destruido toda su información desaparece y no podemos volver a acceder a ella, de manera que si estamos trabajando con una base de datos MySQL por ejemplo y durante el desarrollo estamos haciendo operaciones de CRUD con datos en ella a los que más adelante queramos acceder vamos a necesitar **volumenes**, estos volumenes los definiremos en el archivo de compose también y los asignaremos a los servicios correspondientes.

Para comunicarse entre si los contenedores tienen que estar dentro de una misma network, ya que tenemos que ser conscientes de que por defecto se ejecutan de manera aislada y no pueden relacionarse entre si. Docker Compose establece por defecto una network entre todos los servicios del archivo en cuestión, si bien es **conveniente, y una buena práctica** definir nosotros mismos la network para que tenga al menos un nombre conocido.

Una vez hemos visto las características más importantes vamos a ver como ejemplo los archivos de docker-compose que tenemos en nuestro proyecto.

#### 2.1 docker-compose.dev.yaml para lanzar la app en desarrollo

En los archivos de docker-compose como podemos ver es importante la indentación (como en python), ya que si no es correcta puede llevar a un mal funcionamiento del archivo.

En este archivo tenemos 3 servicios:

\- **mysql**

[![mysql-service.png](https://i.postimg.cc/0QT6wYQg/mysql-service.png)](https://postimg.cc/Q9qxLW1S)

En este servicio hacemos lo siguiente

`image: mysql:8.0` definimos la imagen que va a utilizar el servicio mysql y sobre la que se va a crear el contenedor de la base de datos. Si recordais no hemos creado Dockerfile para la base de datos y esto se debe a que directamente vamos a utilizar una **imagen oficial** para la base de datos, lo cual es una **buena práctica**.

`restart: always` Define que el contenedor se reiniciara automaticamente si se detiene por algun motivo

```
environment:
    MYSQL_ROOT_PASSWORD: user
    MYSQL_DATABASE: proyecto_formativo_db
    MYSQL_USER: user
    MYSQL_PASSWORD: user
```
Define las variables de entorno que se van a utilizar en el contenedor de mysql, estas variables de entorno sirven para configurar la base de datos, podemos ver como se utilizan en la [documentación de la imagen](https://hub.docker.com/_/mysql) de MySQL.
En este caso simplemente establecemos una password para el root de mysql, creamos la database que vamos a utilizar si no existe y creamos un usuario para usar la base de datos al que se le dan privilegios automaticamente.
```
ports:
    - "3307:3306"
``` 
Mapeamos el puerto del contenedor 3306 (por defecto en MySQL) al puerto 3307 de nuestra máquina host. Esto es util si por ejemplo tenemos otro contenedor corriendo en el puerto 3306.

```
volumes:
    - mysql-dev-data:/var/lib/mysql
```
Establecemos el volumen que vamos a utilizar para persistir los datos de la BDD, "mysql-dev-data" es el nombre de volumen que creamos en el docker-compose y ":/var/lib/mysql" mapea el path por defecto en el que el contenedor de MySQL guarda los datos, a nuestro volumen.

```
networks:
    - app-network
```
Establecemos la network a la que va a estar conectado nuestro contenedor, si no establecemos network en los servicios docker-compose crea una por defecto y los conecta a ella, pero asi controlamos cual es de mejor manera.

```
 healthcheck:
    test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
    interval: 10s
    timeout: 5s
    retries: 5
```
Esta parte es totalmente opcional pero la recomiendo, lo que hace basicamente es un check de salud para cerciorarse de que la base de datos esta funcionando, esto lo utilizamos más adelante en el servicio del backend para que el backend no se inicie hasta que la BDD este operativa.
En lo personal me ha resultado útil ya que si no a veces el backend se inicia antes de que la BDD esté preparada y da fallos.

\- **backend**

[![backend-service.png](https://i.postimg.cc/VkQr7QXx/backend-service.png)](https://postimg.cc/dL6V1fH6)

Pasaremos por alto configuraciones ya explicadas anteriormente.

```
build:
    context: ./Backend
    dockerfile: Dockerfile
```
En este caso en lugar de utilizar una imagen ya creada hacemos el build de la imagen directamente desde el Dockerfile. Tambien es posible subir la imagen a un registro y utilizar esa imagen, lo veremos más adelante.

```
env_file:
    - ./Backend/.env
```
Especificamos el archivo .env que utilizará el contenedor para las variables de entorno de nuestro Backend.

```
environment:
    NODE_ENV: development
```
Definimos otras variables de entorno que no están en el .env, esto es interesante ya que podemos definir el NODE_ENV por ejemplo desde docker-compose y de esa manera manejar en que modo de producción corre nuestro backend.

```
volumes:
    - ./Backend:/app
    - /app/node_modules
```
Al contrario que en el servicio de **mysql** no hemos definido ningún volumen al que asignar estas rutas, por lo que estariamos realizando bind-mounts. Tenemos que tener en cuenta que los datos y los cambios que efectuemos en el código, si bien se ven en tiempo real y se modifican mientras tengamos el contenedor corriendo, solo estarán guardados en nuestro host, no se veran guarados en el contenedor o su imagen. Por lo que si destruimos el contenedor y lo volvemos a crear los cambios no se verán reflejados, a no ser que volvamos a hacer el bind-mount. Si queremos que las modificaciones persistan deberemos actualizar la imagen del contenedor.
¿Para que sirve esto entonces?
con `- ./Backend:/app` Montamos el directorio ./Backend del host en /app dentro del contenedor, permitiendo que el código fuente sea accesible dentro del contenedor y los cambios se reflejen mientras desarrollamos en tiempo real sin necesidad de reconstruir la imagen y volver a montar el contenedor.
`- /app/node_modules` es un bind-mount que apunta al directorio de node_modules del contenedor. Al haber hecho bind-mount del ./Backend al contenedor podemos cambiar accidentalmente el archivo node_modules o borrarlo completamente mientras desarrollamos, haciendo que la aplicación falle, con esta opcion impedimos que sea modificado de alguna manera. Esto hace que las dependencias de nuestro proyecto sean manejadas de manera aislada por el contenedor evitando incompatibilidades.

```
depends_on:
    mysql:
      condition: service_healthy
```
Espera al health-check que hemos establecido en el servicio de la BDD, y solo se inicia una vez la BDD tiene el status de healthy

```
 command: npm start
 ```
 Define el comando que se ejecutará al iniciar el contenedor, en este caso lanzar el backend.

\- **frontend**

[![frontend-dev-service.png](https://i.postimg.cc/9fgSGjk9/frontend-dev-service.png)](https://postimg.cc/NyTpcZBG)

En esta no hay nada nuevo que no hayamos visto, utilizamos el npm run dev para lanzar el frontend en modo desarrollo.


Y después tenemos definidos un volumen para la base de datos y una network, que hemos visto utilizados en los servicios anteriores.

[![volumes-network.png](https://i.postimg.cc/FzrLm29P/volumes-network.png)](https://postimg.cc/tZcTNvnP)



#### 2.2 docker-compose.yaml para lanzar la app en producción

[![docker-compose-prod.png](https://i.postimg.cc/2j7twvgm/docker-compose-prod.png)](https://postimg.cc/vDDX81QS)

Como vemos hay diferencias pero son muy sutiles

\- El servicio de **mysql** de producción corre en el puerto 3306 de nuestro host, por eso el de desarrollo lo teniamos mapeado al 3307.

\- El volumen al que se monta el servicio de la BDD es "mysql-data" en lugar de "mysql-dev-data", para tener volumenes distintos en producción y desarrollo.

\- En el servicio de **backend** para producción establecemos la NODE_ENV a "production" en lugar de a "development"

\- En el servicio de **frontend** establecemos NODE_ENV también a producción y hacemos el build de la imagen con el Dockerfile de producción en lugar del de desarrollo. Esto nos hara un build del frontend y nos servirá el dist a través de nginx.

\- En el servicio de **frontend** mapeamos el puerto de nuestro host en que queremos que se sirva el frontend al puerto expuesto de nginx. He puesto el 81 pero pudes poner el que quieras siempre que este libre.

\- El **volumen** creado es diferente al anterior 

**NOTA:** Podeis ver que la network de desarrollo y producción es la misma, esto se debe a que no he usado ambos entornos a la vez nunca y por lo tanto no habria problemas ya que la network se crea y se destruye cuando iniciamos o paramos docker compose. Si queremos tener el entorno de producción y el de desarrollo desplegados a la vez habria que especificar nombres de network diferentes para cada una. 

### 3. .dockerignore

[![dockerignore.png](https://i.postimg.cc/jjW56R50/dockerignore.png)](https://postimg.cc/t1jppQW2)

Es un archivo muy sencillito en el que simplemente especificamos los archivos que no queremos que docker tenga en cuenta a la hora de crear las imagenes.

### 4. Ejecutar docker compose

Nos dirigimos en la consola de Debian al directorio en el que se encuentren nuestros archivos de docker-compose.
Ejecutamos el siguiente comando
```
docker-compose -f docker-compose.yaml up --build -d
```
`-f docker-compose.yaml` Como nosotros tenemos 2 archivos docker compose, para producción y dev tenemos que seleccionar el archivo que queremos usar
`up --build` inicia el archivo y construye las imagenes necesarias que se van a utilizar
`-d` hace que se ejecute en segundo plano

Una vez terminada la ejecución obtendremos algo semejante a esto 
[![compose-prod-log.png](https://i.postimg.cc/25zpmwsH/compose-prod-log.png)](https://postimg.cc/K1spDnvL)

Y podremos acceder a nuestra aplicación en los puerto establecidos, en este caso como es en producción podriamos acceder al frontend en localhost:81

Y podemos ver también toda la información de los contenedores, las imagenes que utilizan, sus logs y demás en Docker Desktop.

[![docker-compose-desktop.png](https://i.postimg.cc/KjVkh44N/docker-compose-desktop.png)](https://postimg.cc/9wdFGmdR)

[![docker-compose-logs-desktop.png](https://i.postimg.cc/jqkJ73v7/docker-compose-logs-desktop.png)](https://postimg.cc/nMBz8kyF)

Por supuesto también tenemos acceso a toda esta información desde la CLI sin necesidad de Docker Desktop, pero Desktop nos lo muestra de una manera más visual.

con `docker ps` veriamos los contenedores que estan funcionando y con 
`doker logs proyectoformativo-backend-1` veriamos los mismos logs del backend que arriba. 

Para detener los contenedores podemos hacerlo bien desde Docker Desktop o simplemente con el comando de 
```
docker compose down 
```
Este comando elimina tanto los contenedores como las network asociadas al archivo compose correspondiente. Los volumenes persisten (que es lo que queremos) a no ser que  se añada la etiqueta `--volumes` al comando.

Las imagenes permanecen también en nuestro equipo para ser utilizadas con mayor rapidez la proxima vez que las necesitemos, si queremos eliminarlas podemos con Dekstop o con la CLI.

### 5. Subir, descargar y utilizar imagenes de otros.

[En este apartado](https://docs.docker.com/get-started/04_sharing_app/) de la documentación hay un pequeño resumen de como subir nuestras imagenes a Docker Hub.

Lo primero es crear nuestra cuenta en Docker Hub.
Una vez creada en el menú principal elegimos la opción de **crear repositorio**.

[![crear-repositorio.png](https://i.postimg.cc/MZ47GJw4/crear-repositorio.png)](https://postimg.cc/w3XyWP7c)

Establecemos el nombre del repositorio, la descripción y le damos a crear.

[![repositorio-nombre.png](https://i.postimg.cc/XJ5CGp4z/repositorio-nombre.png)](https://postimg.cc/N9gMZGy6)

Ahora, nos dirigimos al proyecto donde se encuentre la Dockerfile para crear la imagen que deseemos y la creamos especificando como tag el nombre de nuestro repositorio seguido del nombre de la imagen, en mi caso:

adriznd/guia-docker:1

El :1 es una tag adicional para controlar la versión de la imagen, o lo que deseemos.

Creamos la imagen, en mi caso voy a utilizar simplemente el proyecto de getting started de la documentación.

```
docker build -t adriznd/guia-docker:1 .
# El . del final indica el directorio en el que nos encontramos
```
Comprobamos que la imagen ha sido creada con `docker image ls` lo cual nos muestra la lista de imagenes que tenemos

Después deberiamos de hacer `docker login` en la CLI y especificar nuestro usuario y contraseña para loggear en Docker Hub.
Una vez hayamos iniciado sesión correctamente podemos utilizar el comando `push` para subir la imagen al repositorio.

```
docker push adriznd/guia-docker:1
```
[![repositorio-push.png](https://i.postimg.cc/L513S2yz/repositorio-push.png)](https://postimg.cc/FkhLg5XR)

[![repositorio-version.png](https://i.postimg.cc/NG94mkg5/repositorio-version.png)](https://postimg.cc/s1z7RSGr)

Podemos observar como la imagen esta subida a nuestro repositorio y podemos identificarla con el TAG que habiamos dado antes. 

Ahora ya podriamos utilizar esta imagen, nosotros mismos o quien quisiera (si el repositorio es publico) o quien tuviera acceso (si es privado).

Podemos hacer uso de ella en docker-compose con

```
image: adriznd/guia-docker:1
```

O si queremos correr un contenedor de esa imagen de manera aislada podemos descargarla con 

```
docker pull adriznd/guia-docker:1
```

y despues ejecutarla con 

```
docker run -dp 8080:8080 adriznd/guia-docker:1
```

### 6. Desarrollar aplicación desde cero con Docker

Hemos visto como Dockerizar una aplicación que tenemos ya construida y esto nos va genial para antiguos proyectos. Pero una vez que sabemos utilizar Docker podemos implementarlo desde la base del desarrollo de nuestras apps para hacer el desarrollo mucho más fluido y solido.

Vamos a hacer un pequeño scaffold o plantilla para iniciar el desarrollo de un proyecto con Docker, basado en un Backend de Node y Express y un Frontend de Vue 3 + Vite, en este caso utilizaremos la imagen de MySQL oficial para el contenedor de la Base de Datos.

La estructura del proyecto quedará de este estilo

[![folder-structure.png](https://i.postimg.cc/Nf5vPqLG/folder-structure.png)](https://postimg.cc/rKXHKHS7)


Nos situamos en la carpeta donde vamos a situar el proyecto 
**1. Creamos estructura del proyecto**
```
mkdir backend
mkdir frontend
touch docker-compose.yaml .env
```
**NOTA:** Podemos crear las carpetas y los archivos directamente desde nuestro editor de codigo si lo deseamos también.

**2. Configuramos el Backend**
```
cd backend
npm init -y
npm install express
touch Dockerfile
mkdir src
touch src/server.js
```

Editamos la Dockerfile 
```
# Use an official Node runtime as a parent image
FROM node:20.13.1-bookworm-slim

# Set the working directory
WORKDIR /usr/src/app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application code
COPY . .

# Expose port
EXPOSE 8080

# Start the application
CMD ["node", "src/server.js"]
```

Editamos src/server.js
```
const express = require('express')
const app = express()
const port = 8080

app.get('/', (req, res) => {
  res.send('Hello World from Backend!')
})

app.listen(port, () => {
  console.log(`Backend listening at http://localhost:${port}`)
})
```
**2. Configuramos el Frontend**

```
cd ../frontend
npm init vite@latest
# Deja el nombre default o lo que quieras, elige Vue como framework y sigue las instrucciones
#Ahora vamos a mover los archivos creados a frontend y borrar la carpeta que nos ha creado
cd vite-project
mv * ../
cd ..
rm -r vite-project
# De esta manera tenemos todos los archivos del proyecto de vite vue directamente en la root de frontend
#Instalamos dependencias y creamos Dockerfile
npm install
touch Dockerfile
```

Configuramos el Dockerfile
```
# Use an official Node runtime as a parent image
FROM node:20.13.1-bookworm-slim

# Set the working directory
WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application code
COPY . .

# Expose port
EXPOSE 3000

# Start the application
CMD ["npm", "run", "dev"]

Recordemos que tenemos que configurar el archivo de vite.config para poder trabajar con Docker

```

**3. Configuramos archivo docker-compose.yaml**
```
services:
  postgres:
    image: postgres:13
    restart: always
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: user
      POSTGRES_DB: scaffold
    ports:
      - "5432:5432"
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - docker-scaffold
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -q -d scaffold -U user"]
      interval: 10s
      timeout: 5s
      retries: 3

  backend:
    build: 
      context: ./backend
      dockerfile: Dockerfile
    restart: always
    ports:
      - 8080:8080
    environment:
      - NODE_ENV=development
    volumes:
      - ./backend:/app
      - /app/node_modules
    networks:
      - docker-scaffold
    depends_on:
      postgres:
        condition: service_healthy

  frontend:
    build: 
      context: ./frontend
      dockerfile: Dockerfile
    restart: always
    ports:
      - 3000:3000
    environment:
      - NODE_ENV=development
    volumes:
      - ./frontend:/app
      - /app/node_modules
    networks:
      - docker-scaffold
    depends_on:
      - backend

volumes:
  db-data:

networks:
  docker-scaffold:
```

Y listo, nos situamos en el directorio que contenga el archivo de docker-compose.yaml y ejecutamos
```
docker compose up --build
```
o
```
docker compose up --build -d 
```
Si queremos que corra en segundo plano 

En este caso no es necesario especificar el nombre del archivo compose ya que solo hay uno y tiene el nombre por defecto. Esta build esta configurada para desarrollo si quisieramos desplegar la aplicación tendriamos que añadir las modificaciones necesarias o mejor aún añadir otro archivo compose para modo producción como ya hemos visto.

Y ya lo tenemos, podemos acceder tanto al frontend como al backend en los puertos establecidos y podemos empezar a desarrollar nuestra aplicación.
## Buenas prácticas

Vamos a hacer una pequeña recopilación de buenas prácticas que seguir mientras desarrollamos con Docker. Gran parte de ellas han sido recapituladas de la [documentación oficial](https://docs.docker.com/develop/dev-best-practices/).

#### 1. Manten tus imagenes pequeñas y sencillas
Cuanto más pequeñas son las imagenes más rápido cargan y más rapido pueden descargarse.

**1.1** Siempre que sea posible deberias **utilizar como imagen base** una [imagen oficial de Docker](https://hub.docker.com/search?q=&image_filter=official), en lugar de construir tu propia imagen.

**1.2** **Utilizar [multi-stage builds](https://docs.docker.com/build/building/multi-stage/)** es recomendable siempre que se pueda para que la imagen final guarde la menos cantidad de información posible.

**1.3** Si utilizas varias imagenes que tienen mucho en común **considera crear tu propia imagen** con los componentes en común y ramificar a partir de ahi. De esa manera las capas en común quedan en cache.

**1.4** Cuando construyas imagenes intenta **utilizar tags apropiados** que establezcan la version, el entorno de producción, etc.

**1.5 No instales paquetes innecesarios**, para reducir la complejidad de las imagenes intenta que cada una de ellas contenta solo los paquetes necesarios para su uso.

#### 2. Persistencia de datos

**2.1** **Utiliza volumenes** en lugar de almacenar los datos en el propio contenedor

**2.2** Utilizar [bind-mounts](https://docs.docker.com/storage/bind-mounts/) es apropiado **durante el desarrollo**, pero en producción utilizar volumenes.

#### 3. Desacopla las aplicaciones
Cada contenedor deberia estar dedicado a realizar **exclusivamente una tarea**, de manera que deberiamos separar y aislar la funcionalidad de los contenedores lo mayor posible. Es preferible por ejemplo tener un contenedor para la base de datos, otro para el frontend y otro para el backend que ejecutar todo en el mismo contenedor

#### 4. Variables de Entorno
Es común que trabajando con Docker hagamos uso de variables de entorno. Tenemos que entender que en Docker si establecemos la misma variable de entorno, por ejemplo NODE_ENV, desde diferentes fuentes, Docker utiliza la regla de la procedencia para establecer el valor de la misma. 

1. Con **-e** en la CLI
```
# Usando docker run -e si trabajamos la imagen
docker run -e NODE_ENV=dev <mi-imagen>
# Si trabajamos el servicio
docker compose run -e NODE_ENV=dev <mi-servicio>
```

2. Sustituirlo en consola de comandos
```
#Podemos cambiar el valor de la variable a traves de consola donde docker compose se ejecuta
export NODE_ENV=dev
```

3. Con atributo **enviroment** en archivo docker-compose
```
services:
  my_service:
    image: my_image
    environment:
      NODE_ENV: dev
```

4. Usar **--env-file** en la CLI
```
docker compose --env-file ./config/.env.dev up
```

5. Usar **env_file** en archivo docker-compose
```
web:
  env_file:
    - web-variables.env
```
6. Archivo **.env** en root del proyecto, donde se encuentra docker-compose.yaml

```
services:
  web:
    image: "webapp:${MI_VARIABLE_DE_ENTORNO}"
```

7. Utilizando directiva **ENV** en Dockerfile
Esto solo se resuelve si nada de lo anterior lo sobreescribe, podemos establecer un valor default por ejemplo para el modo de desarrollo.

```
# En el archivo Dockerfile

ENV NODE_ENV= production
```

Las **buenas prácticas** con variables de entorno son:

###### 1. Utiliza la flag -e o el archivo .env.

###### 2. Mantén seguro tu archivo .env: El archivo .env puede contener información sensible, como claves API y contraseñas de bases de datos. Es crucial mantener tu archivo .env seguro y no incluirlo en tu repositorio de código.

###### 3. Usa nombres de variables descriptivos.

###### 4. Define valores predeterminados.

###### 5. Actualiza tu archivo .env regularmente.

###### 6. Utiliza Docker Compose: Al trabajar con Docker Compose, las variables de entorno pueden definirse en el archivo docker-compose.yml, proporcionando una ubicación para el archivo .env

