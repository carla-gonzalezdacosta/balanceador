# Tarea: Balanceador de carga con Apache
## IWEB - ASIR 2
### Carla González Da Costa
### 12/11/2026

### Introducción:
En esta práctica nos hemos propuesto montar una infraestructura web profesional y robusta, como si fuera un entornos real para que las páginas no se caigan y aguanten muchas visitas. Para conseguirlo, en lugar de meterlo todo en un único servidor, hemos dividido el trabajo en cuatro máquinas virtuales independientes que se comunican entre sí en una red privada.

La infraestructura se divide en tres niveles:

**1. El Balanceador de Carga:** Es la puerta de entrada de todo el sistema. Recibe las peticiones de los usuarios desde fuera y las va repartiendo por igual entre los servidores web.

**2. Dos servidores web:** Son las dos máquinas que tienen instalada la pila LAMP (Linux, Apache, MySQL, PHP) y se encargan de procesar las páginas web y atender lo que pide el balanceador.

**3. Base de Datos (Back-End):** Una máquina única dedicada en exclusiva a albergar el servidor MySQL para guardar toda la información de forma centralizada y segura.

Para crear todo esto, voy ha automatizar la instalación mediante scripts en Bash. 

### 1. Arquitectura y despliegue de la insfraestructura.

Para levantar todo este entorno sin tener que instalar los sistemas operativos uno por uno a mano, he utilizado el mecanismo de Vagrant. Mediante un único archivo de configuración (Vagrantfile), despliego simultáneamente las cuatro máquinas virtuales con Ubuntu Server 22.04 LTS y las conecto a través de una red privada interna con IPs estáticas

| Máquina Virtual | Arquitectura | IP
| --- | --- | --- |
| balanceador | Proxy | 192.168.56.10 |
| frontal1 | servidor web 1 (Pila LAMP) | 192.168.56.11 |
| frontal2 | servidor web 2 (Pila LAMP) | 192.168.56.12 |
| back-mysql | servidor de base de datos MySQL | 192.168.56.13 |

### 2. Crear carpeta y levantar VMs

Voy a crear una carpeta llamada practica_balanceador, dentro de mi carpeta principal ASIR_2/WEB
Entro en el cmd desde ese directorio y creo el vagrantfile `vagrant init`

### 3. Editar el vagrantfile

Abro el vagrantfile VSCode `code ./Vagrantfile` y edito el archivo para que me crea las 4 VMs junto a sus IPs , red y puertos correspondiente. ç

**INCIDENCIA:**

![Error vagrant up](images/error_vagrant_up.png)











