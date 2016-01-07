# Primeros Pasos con Docker

En Capítulos anteriores vimos lo que es Docker y como instalarlo en Linux CentOS y Ubuntu. Pero antes de entrar de lleno con Docker, recuerdan que les hablaba de Docker registries, quiero hablarles brevemente de **Docker Hub**.

![](dockerhub1.png)

**Docker Hub** es un servicio de registro basado en la nube para crear y entregar contenedores de servicio o aplicaciones. Provee un recurso centralizado para el descubrimiento de contenedores, distribución, control de cambios, colaboración de equipos y automatización de flujos de trabajo. Veanlo como como un** Github** de contenedores.

Características de Docker Hub

* **Repositorios de Imágenes**: Encuentra, administra, sube y descarga imágenes de la comunidad y oficiales

* **Imágenes automáticas**: Crea nuevas imágenes cuando haces un cambio en la Fuente de Github o BitBucket

* **Webhooks**: creaciones automáticas de imágenes al hacer un push exitoso a un repositorio.

* **Integración con GitHub y BitBucket**


Docker Hub le provee a usted y su organización un lugar donde alojar y entregar las imágenes.

Se puede configurar los repositorios de Docker Hub de dos maneras:


**Repositorios**, que nos permiten subir y actualizar las imágenes cuando deseemos desde el docker daemon y las **imágenes automáticas** que nos permiten configurar una cuenta de Github o BitBucket que desencadenan la reconstrucción de una imágen cuando se realizar algún cambio en el repositorio.


Se pueden crean repositorios públicos que pueden ser accedidos por otros usuarios de HUB, o se pueden crear repositorios privados con accesos limitados.

Pueden crear su cuenta en https://hub.docker.com, luego veremos como podemos aprovechar todo esto.
