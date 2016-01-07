# Fundamentos de Docker

#Que es Docker?

Docker es un proyecto Open Source basado en contenedores de Linux, es básicamente un motor de contenedores que usa características del Kernel de Linux como espacios de nombres y controles de grupos para crear contenedores encima del Sistema operativo y automatizar el despliegue de aplicaciones en estos contenedores. Nos permite además un flujo de trabajo bastante eficiente al momento de mover nuestras aplicaciones desde un ambiente de desarrollo, a pruebas y finalmente a producción.


### Hablamos de Espacios de nombres y Control de Grupos, pero que son estos?


**Espacios de Nombres (namespaces)
**

Un espacio de nombre envuelve un recurso de Sistema global en una abstracción que le hace parecer al proceso dentro del espacio de nombre, que tiene su propia instancia aislada del recurso. Los cambios al recurso global son visibles para los proceso dentro del espacio de nombre pero invisible para otros procesos. Una de las implementaciones mas utilizada son los contenedores.

**Linux provee los espacios de nombre:
**

**IPC (Interprocess Communication Mechanism)**: Mecanismo de comunicación Interproceso, aisle ciertos recursos IPC, específicamente System V IPC y cola de mensajes POSIX.

**NETWORK**: Provee aislamiento de los recursos del Sistema asociados con las comunicaciones de red, Dispositivos de red, Stack de protocolos IPv4 e IPv6, tablas de enrutamiento, firewalls, directorio /proc/net, sockets de comunicación entre otros.

**PID**: Aisla el espacio de nombre del ID de proceso, que significa que procesos en otros espacios de nombres PID, pueden tener el mismo PID. Esto le permite a los contenedores proveer funcionalidades de suspension y recuperación de procesos en el contenedor y la migración del contenedor a otro host manteniendo el mismo PID.

**User**: Aisla atributos e identificadores relacionados con la seguridad, particularmente los ID’s de usuarios y grupos, el direcorio raíz, permisos, etc. El usuario y el grupo de un proceso puede ser distinto dentro y fuera del espacio de nombre de usuario, Quiere decir que un proceso puede tener un ID no privilegiado fuera del espacio de nombre y un ID de 0 dentro del espacio de nombre.

**UTS:** Provee aislamiento de dos identificadores de Sistema. El hostname y el nombre del dominio NIS.

**Control de Grupos
**

Los recursos usados por un contenedor son controladores por el control de grupos. Se puede configurar cuanto CPU y memoria usa un contenedor hacienda uso de ellos.
