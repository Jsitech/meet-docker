# Mejores Prácticas DockerFiles

Vamos a ver algunas mejores prácticas y métodos recomendados por Docker Inc. y la comunidad para crear Dockerfiles fáciles de usar y efectivos.


### Usar un Archivo .dockerignore



Como hablamos es recomendable colocar cada DockerFile en un directorio limpio, y según la imagen que vayamos a crear pues agregamos los archivos que son necesarios. Es posible que tengamos algún archivo en el directorio que cumpla una función pero no queremos que sea agregado a la imagen, es por esto que debemos hacer uso de un archivo ```.dockerignore``` para que docker build excluya esos archivos durante la creación de la imagen. 

Ejemplo de un .dockerignore

    */prueba*

    */*/prueba

    prueba?


### No instale Paquetes Innecesarios



Para reducir la complejidad, dependencias, tiempo de creación y tamaño de la imagen resultante, se debe evitar instalar paquetes extras o innecesarios solo para tenerlos ahí y que no van a cumplir ninguna función en la imagen. Si algún paquete es necesario durante la creación de la imagen, lo mejor es desinstalarlo durante el proceso.
Minimizar el número de capas

Debemos encontrar el balance entre la legibilidad del Dockerfile y minimizar el número de capa que utiliza.


### Correr un solo proceso por contenedor



En la mayoría de los casos, se debe correr solo un proceso en un contenedor, claro está esto dependerá del uso que le vamos a dar al contenedor. Desacoplar los componentes de una aplicación entre múltiples contenedores, hará mas sencillo escalar horizontalmente y reutilizar los contenedores.


### Organice argumentos de Múltiples Líneas



Cada vez que sea posible y para hacer más facil futuros cambios, organice argumentos que contengan múltiples líneas, esto evitará la duplicación de paquetes y hará que el archivo sea más fácil de leer. Ej:

    RUN apt-get update && apt-get install -y \

    git \

    wget \

    apache2 \

    php5


### Cache de la Creación de Imágenes



Durante el proceso de la creación de imágenes, Docker seguirá las instrucciones del DockerFile en el orden especificado. En cada instrucción Docker busca una imagen existente en el caché que pueda reutilizar, en vez de generar imágenes duplicadas. En caso de que no quieran que Docker haga uso de imágenes en el caché simplemente es pasarle ```--no-cache=true``` al comando ```docker build```. Sin embargo es importante que se entienda cuando docker encontrará o no una imágen que pueda reutilizar, en caso de que dejemos que use el caché.

* Comenzando con la imagen base que ya está en caché, la siguiente instrucción es comparada con todas las imágenes derivadas de esa imágen base para ver si una de ellas se creó usando exactamente las mismas instrucciones, si no, la caché es invalidada.
* En muchos de los casos con solo comparar el DockerFile con una imagen derivada debe ser suficiente. Sin embargo, hay ciertas instrucciones que requieren de mas verificación
* Para las instrucciones ADD y COPY, el contenido de los archivos en la imagen son examinadas y un Checksum es calculado para cada archivo. Durante la búsqueda en el caché el Checksum es comparado contra los Checksums de las imágenes ya creadas. Si algo cambió, la caché es invalidada.
* Si durante una instrucción, por ejemplo un ```RUN apt-get update```, los archivos son actualizados dentro del contenedor, estos no serán examinados para determinar si existe algo en la caché, en este caso solo se tomará en cuenta el Comando para encontrar una imágen existente.
* Una vez la caché es invalidada, todos las instrucciones siguientes en el DockerFile generarán nuevas imágenes y no harán uso de la caché.


### Mejores Prácticas para las Instrucciones en los DockerFiles



En este apartado vamos a ver algunas recomendaciones para algunas instrucciones que tenemos a la mano al momento de escribir un Dockerfile.


### FROM



Cada vez que sea posible, haga uso de imágenes oficiales para basar sus imágenes.


### RUN



Para hacer mas legible y entendible su DockerFile, divida comandos extensos y complicados en múltiples líneas.

**Apt-get
**

Una de los comandos más utilizado con ```RUN``` son la instalación de Paquetes o actualizaciones, por ejemplo, ```apt-get update```, ```apt-get upgrade```. Debemos evitar el uso de ```apt-get upgrade``` o ```dist-upgrade```, ya que los paquetes esenciales no llegan a ser actualizados en un contenedor no privilegiado. Si se encuentra con una imágen que tiene un paquete desactualizado, sencillamente haga un ```apt-get install -y``` para que lo actualice automáticamente.

Siempre combine ```RUN apt-get update``` y ```apt-get install``` en la misma instrucción, ya que de hacerlo por separado nos toparíamos con un tema de Caching. Recuerden todo lo que hablamos del uso de la caché, si Docker encuentra una imágen que pueda reutilizar y no necesariamente es la que acaba de generar con ```apt-get update```, terminará con paquetes desactualizados.

Ejemplo de un correcto uso:

    RUN apt-get update && apt-get install -y \

    git \

    apache2 \

    php5 \

    CMD


### CMD



La instrucción CMD debe ser usado para correr aplicaciones dentro de sus contenedores seguido de cualquier argumento. Debe ser usado siempre en la forma``` CMD [“ejecutable”, “parámetro1”, “parámetro2” …]``` , Ejemplo: si el contenedor es para servir una aplicación web tendríamos algo asi , ```CMD [“apache2” , “-D”, “FOREGROUND” ]```

En otros casos, debe ser utilizado para dar un shell interactivo. Ejemplo: ```CMD [“python”] o CMD [“/bin/bash” ]```, de esta manera terminaríamos con una shell listo para trabajar. Tenemos la opción de solo pasarlo los parámetros a CMD en conjunto con ENTRYPOINT para debemos entender bien como funciona todo esa combinación.


### ADD y COPY



Aunque ADD y COPY tiene funcionalidades similares, COPY es el prefererido. COPY soporta el copiado básico de archivos locales al contenedor, mientras ADD tiene otras funcionalidades como la extracción local de archivos tar y el soporte a URL’s remotos. Así que en estos casos que se requiera una auto extracción de un archivo hacia el contenedor, es mejor hacer uso de ADD.


### ENV



Para hacer mas sencillo la ejecución de aplicaciones dentro del contenedor, podemos hacer uso de ENV para actualizar las variables de las rutas por ejemplo, de la aplicación que el contenedor ejecuta. Ejemplo: ```ENV PATH /opt/app/bin:$PATH```, esto permitirá que ```CMD [“app”]``` funcione.


### USER



Si un servicio puede correr sin privilegios, use USER para cambiarlo a un usuario no privilegiado. Lo primero es crear el usuario y el grupo en una instrucción del DockerFile con RUN.


### WORKDIR



Para claridad y legibilidad, siempre se debe hacer uso de rutas absolutas para el directorio de trabajo WORKDIR, y siempre debe ser usado y no hacer uso de ```RUN cd /ruta/``` ya que igual puede traer temas con el caching y problemas para hacerle el troubleshooting de lugar.

Bueno, creo que en este punto punto vamos bien encaminados con las creaciones de imágenes y mas mediante los DockerFiles.