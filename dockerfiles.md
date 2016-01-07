# DockerFiles

En el capítulo anterior vimos como pudimos crear una imágen basadas en un contenedor que lanzamos y le hicimos algunos cambios, como instalarle un paquete. En esa misma entrada les hablaba que es posible que necesitemos ciertos niveles de personalización y esto lo podemos lograr mediante un DockerFile.

Un DockerFile es un documento de texto que contiene todos los comandos que queramos ejecutar en la linea de comandos para armar una imágen. Esta imágen se creará mediante el comando docker build que irá siguiendo las instrucciones. Antes de hablar de los Dockerfiles vamos a hablar un poco del comando docker build que es el que ejecutaremos una vez tenemos las instrucciones a seguir en un archivo.

El comando docker build arma una imágen siguiendo las instrucciones de un DockerFile que se puede encontrar en el directorio actual o un repositorio. La creación de la imágen es ejecutada por el daemon de Docker. Es importante tener en cuenta que docker build le manda todo el contexto del directorio actual al daemon, por lo que es buena práctica colocar el DockerFile en un directorio limpio y agregar los archivos necesarios en ese directorio en caso de ser necesario.

El Docker Daemon corre las instrucciones en un Dockerfile linea por linea y va lanzando los resultados en pantalla. Un punto importante es que cada instrucción es ejecutada en nuevas imágenes, hasta que muestra el ID de la imágen resultante una vez finalizada las instrucciones, el daemon irá haciendo una limpieza automáticamente de las imágenes intermedias.

Nota: Dicho esto de que el Docker daemon va creando imágenes intermedias durante la creación de la imágen, si por ejemplo en un comando ejecutamos cd /scripts/ y en otra linea le mandamos a ejecutar un script no va a funcionar, ya que ha lanzado otra imágen intermedia. Teniendo esto en cuenta, la manera correcta de hacerlo sería, cd /scripts/ ; ./install.sh.

Ahora, viene otra pregunta, como nos ayuda todo esto de las Imágenes intermedias o Cache? Si por alguna razón la creación de la imágen falla, ya sea por un comando mal digitado en el archivo, o lo que sea, cuando corregimos el Dockerfile, este no iniciará todo el proceso nuevamente, sino, que hará uso de las imágenes intermedias, y continuará la creación en el punto donde falló.

Ya que entendemos que es el Dockerfile, vamos a ver ahora el formato y las opciones que podemos pasarle.

Lo primero es que un DockerFile Inicia con una instrucción:
FROM

FROM indica la imágen base que va a utilizar para seguir futuras instrucciones. Buscará si la imagen se encuentra localmente, en caso de que no, la descargará de internet.

Sintaxis

    FROM <imagen>
    FROM <imagen>:<tag>

Ejemplos

    FROM centos:latest

El tag es opcional, en caso de que no la especifiquemos, el daemon de docker asumirá latest por defecto.

Vamos a seguir con las otras instrucciones
MAINTAINER

Esta instrucción nos permite configurar datos del autor que genera la imágen.

Sintaxis

    MAINTAINER <nombre> <Correo>

Ejemplo

    MAINTAINER Jason Soto “jason_soto@jsitech.com”

RUN

RUN tiene 2 formatos:

    RUN <comando>, esta es la forma shell, /bin/sh -c
    RUN [“ejecutable”, “parámetro1”. “parámetro2], este es el modo ejecución

Esta instrucción ejecuta cualquier comando en una capa nueva encima de una imágen y hace un commit de los resultados. Esa nueva imágen intermedia es usada para el siguiente paso en el Dockerfile.

El modo ejecución nos permite correr comandos en imágenes bases que no cuenten con /bin/sh, nos permite además hacer uso de otra shell si así lo deseamos, ej: RUN [“/bin/bash”, “-c”, “echo prueba”].
ENV

ENV tiene 2 formas:

    ENV <key><valor> , variable única a un valor
    ENV <key><valor> … , Múltiples variables a un valor

Esta instrucción configura las variables de ambiente, estos valores estarán en los ambientes de todos los comandos que sigan en el DockerFile. Pueden por igual ser sustituidos en una linea.

Estos valores persistirán al momento de lanzar un contenedor de la imagen creada. Pueden ser sustituida pasando la opción –env en docker run. Ej: docker run –env <key>=<valor>
ADD

ADD tiene 2 formas:

    ADD <fuente> ..<destino>
    ADD [“fuente”, … “<destino>”]

Esta instrucción copia los archivos, directorios de una ubicación especificada en <fuente> y los agrega al sistema de archivos del contenedor en la ruta especificada en <destino>.

Ejemplo:

    ADD ./prueba.sh /var/tmp/prueba.sh

EXPOSE

Esta instrucción le especifica a docker que el contenedor escucha en los puertos especificados en su ejecución. EXPOSE no hace que los puertos puedan ser accedidos desde el host, para esto debemos mapear los puertos usando la opción -p en docker run

Ejemplo:

    EXPOSE 80 443

    docker run centos:centos7 -p 8080:80

CMD

CMD tiene tres formatos:

    CMD [“ejecutable”, “parámetro1”, “parámetro2”] , este es el formato de ejecución
    CMD [“parámetro1”, “parámetro2”] , parámetro por defecto para punto de entrada
    CMD comando parámetro1 parámetro2, modo shell

Solo puede existir una instrucción CMD en un DockerFile, si colocamos más de uno, solo el último tendrá efecto. El objetivo de esta instrucción es proveer valores por defecto a un contenedor. Estos valores pueden incluir un ejecutable u omitir un ejecutable que en dado caso se debe especificar un punto de entrada o ENTRYPOINT en las instrucciones.

Ejemplos:

Modo Shell

    CMD echo “Esto es una prueba”

Si queremos ejecutar un comando sin un shell, debemos expresar el comando en formato JSON y dar la ruta del ejecutable. Es el formato recomendado.

    CMD [“/usr/bin/service”, “httpd”, “start”]

Si lo que queremos es que el mismo ejecutable corra todo el tiempo, lo que necesitamos es un punto de entrada ENTRYPOINT en combinación con CMD. En caso de pasarle un comando mediante docker run, este correrá en vez del especificado por CMD.
ENTRYPOINT

ENTRYPOINT tiene 2 formas:

    ENTRYPOINT [“ejecutable”, “parámetro1”, “parámetro2”], forma de ejecución
    ENTRYPOINT comando parámetro1 parámetro2, forma shell

Cualquier argumento que pasemos en la línea de comandos mediante docker run serán anexados despues de todos los elementos especificados mediante la instrucción ENTRYPOINT, y anulará cualquier elemento especificado con CMD. Esto permite pasar cualquier argumento al punto de entrada. Ejemplo:

    docker run centos:centos7 -c

Esto le pasará el argumento -c al punto de entrada o ENTRYPOINT que especificamos en el DockerFile. Podemos anular las instrucciones del punto de entrada pasando la opción –entrypoint al comando docker run.

Veamos un ejemplo de uso ENTRYPOINT en un DockerFile

    FROM centos:centos7

    ENTRYPOINT [“http”, “-v ]”

    CMD [“-p”, “80”]

En este ejemplo corremos httpd, en modo verbose, en el ENTRYPOINT, y los argumentos que entendamos puedan cambiar con CMD, puerto 80. Si quisiera correr el contenedor con httpd corriendo en el puerto 8080, solo tendría que ejecutar docker run centos:centos7 -p 8080.

Es posible tambien hacer uso de scripts para ejecutar ciertas cosas, pero lo mantendré simple.
VOLUME

Esta instrucción crea un punto de montaje con un nombre especificado y lo marca con un volumen montado externamente desde el host y otro contenedor. El valor pueden ser pasado en formato JSON o argumento plano.

    VOLUME [“/var/tmp”]
    VOLUME /var/tmp

El comando docker run inicializará el nuevo contenedor con cualquier data que exista en la ubicación dentro de la imágen base.
USER

Esta instrucción configura el nombre de usuario a usar cuando se lanza un contenedor y para la ejecución de cualquier instrucción RUN, CMD o ENTRYPOINT.
WORKDIR

    WORKDIR ruta/de/trabajo

Esta instrucción configura el directorio de trabajo para cualquier instrucción RUN, CMD, ENTRYPOINT, COPY o ADD en un DockerFile. Puede ser usada varias veces dentro de un DockerFile. Si se da una ruta relativa, esta será la ruta relativa de la instrucción WORKDIR anterior.

Esta instrucción tiene la capacidad de resolver variables de ambiente previamente configuradas mediante la instrucción ENV. Ejemplo:

    ENV rutadir /ruta

    WORKDIR $rutadir

Aquí están las opciones que más usaremos y que veremos en muchos DockerFiles. Existen otras opciones, pero para mantenerlo un poco simple, ya hablaremos mas adelante de ellos. Aquí les copio un DockerFile que cree para crear una imágen de un servidor maligno usando como imágen base kali linux. Pueden verlo también en GitHub.

    #Docker Container With Maligno, Metasploit Payload Server

    #Use Kali Linux Official Docker image

    FROM kalilinux/kali-linux-docker

    MAINTAINER Jason Soto “jason_soto@jsitech.com”

    ENV DEBIAN_FRONTEND noninteractive

    EXPOSE 443 22

    #Updates Repo and installs Maligno Dependencies

    RUN apt-get update; apt-get -y –force-yes install openssl ; apt-get -y install python-ipcalc; apt-get install python-crypto

    #Installs OpenSSH

    RUN apt-get -y install openssh-server

    #Installs Metasploit Framework

    RUN apt-get -y install metasploit-framework

    #Downloads And install Maligno Server

    RUN wget –no-check-certificate http://www.encripto.no/tools/maligno-2.4.tar.gz

    RUN tar xzvf maligno-2.4.tar.gz; cd maligno-2.4/; ./install.sh

    #Adds config XML with correct metasploit Path

    ADD ./server_config.xml /maligno-2.4/server_config.xml

Ya cuando se tiene todo lo queremos definido solo es correr el comando docker build desde la ruta donde se encuentra el Dockerfile.

    $ docker build -t jsitech/kali-maligno .

Nota: El archivo debe esta nombrado Dockerfile