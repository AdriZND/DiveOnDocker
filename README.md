# DiveOnDocker

Vamos a hacer una pequeña guía de qué es docker, como se utiliza y para que sirve, asi como una serie de consejos y buenas practicas para hacer su uso más facil.

Gran parte de la información de esta guía se basa en la [Documentación oficial](https://docs.docker.com/get-started/) de Docker, y aconsejo acudir a ella con cualquier duda ya que es bastante buena.

## ¿Qúe es Docker?
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

Descargamos la ultima versión de [Docker Desktop](https://docs.docker.com/desktop/install/windows-install/) desde la web oficial de Docker.

Una vez se descargue simplemente ejecutamos el instalador y nos aseguramos de que cuando nos pregunte marcamos la opción de "Use WSL instead of Hyper-V"

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

## Cómo utilizar docker

Ahora que tenemos Docker instalado vamos a proceder a dockerizar nuestra aplicación. Voy a tomar de referencia [esta aplicación](https://github.com/AdriZND/ProyectoFormativo) que desarrollé. Consiste en un Frontend de Vue 3 + Vite, un Backend en ExpressJs y Node y una base de datos MySQL que utilizamos mediante el ORM de Sequelize.

#### ¿Qué queremos conseguir?
Nuestra intención es ser capaces de lanzar la aplicación tanto en modo de producción como de desarrollo a través de [Docker Compose](https://docs.docker.com/compose/), es una herramienta que nos permite lanzar varios contenedores a la vez e interconectarlos para trabajar a la vez con ellos.
Para lograr esto debemos seguir los siguientes pasos.
###### 1. Crear Dockerfiles para Frontend, Backend y BDD
Las [Dockerfiles](https://docs.docker.com/guides/docker-concepts/building-images/writing-a-dockerfile/) son los archivos que nos permiten construir imagenes para luego poder ser utilizadas a traves de contenedores.
###### 2. Crear archivos docker-compose para lanzar la app.
El [docker-compose.yaml](https://docs.docker.com/compose/compose-application-model/) nos permite lanzar a la vez diferentes contenedores basados en sus correspondientes imagenes y con sus configuraciones.
###### 3. Publicar nuestras imagenes
Una vez hemos aprendido a crear nuestras imagenes y a lanzar una aplicación multi-contenedor es conveniente aprender a subir las imagenes a un registro para que otras personas puedan utilizarlas.

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

**docker-compose.dev para lanzar la app en desarrollo**

[![docker-compose-dev.png](https://i.postimg.cc/L59YHHQw/docker-compose-dev.png)](https://postimg.cc/VrhLgc8W)





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

#### 4.
