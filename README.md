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

### 3. Editar el vagrantfile e iniciar las VMs.

Abro el vagrantfile VSCode `code ./Vagrantfile` y edito el archivo para que me crea las 4 VMs junto a sus IPs , red y puertos correspondiente. 
Ahora hago `vagrant up` para iniciar todas las VMs. 

**INCIDENCIA:**

![Error vagrant up](images/error_vagrant_up.png)

Al intentar ejecutar la práctica apareció un error en VirtualBox que impedía a Vagrant acceder correctamente a las máquinas virtuales. Tras revisar el problema e investigar información adicional, ya que esta situación no se explicaba en los apuntes, comprobé que estaba relacionado con la información almacenada en la carpeta .vagrant, que había quedado desactualizada al mover la práctica de ubicación. Eliminé dicha carpeta para que Vagrant generara una nueva configuración y pude continuar con el despliegue.

**INCIDENCIA:**
Ahora me aparece un nuevo error con la vm frontal1 debido a que durante el despliegue aparece un error de tiempo de espera agotado al intentar conectarme por SSH a la máquina frontal1. Aunque la máquina virtual arranca correctamente en VirtualBox, Vagrant no consigue establecer la conexión dentro del tiempo previsto, por lo que el proceso se detiene y tengo que revisar el estado de la máquina y la configuración de red para continuar con el despliegue.

![Error frontal1](images/error_frontal1.png)

Para solucionar el problema, primero accedo a la máquina virtual desde VirtualBox y compruebo que el sistema operativo ha terminado de arrancar correctamente, ya que aparece la pantalla de inicio de sesión de Ubuntu. A continuación inicio sesión con el usuario vagrant y verifico que el servicio SSH está funcionando mediante el comando `sudo systemctl status ssh`
Al comprobar que el servicio se encuentra activo, vuelvo al host y reinicio la máquina gestionada por Vagrant con `vagrant reload frontal1`
Se ha podido establecer correctamente la conexion por ssh y se ha terminado la configuración de la vm frontal1. 

![Solucion error vm frontal1 conexion ssh](images/solucion_frontal1.png)




Continuo con el inicio del resto de VMs: frontal2 y back-mysql. Para ello ejecuto: `vagrant up frontal2` y `vagrant up back-mysql`

Al iniciar frontal2 `vagrant up frontal2` me ha salido el mismo error de conexión ssh. Por ello, inicié sesión directamnete desde la consola con el usuario vagrant y verifico que el servicio shh está activo.

![estado activo ssh frontal2](images/activo_ssh_frontal2.png)

Tras estas comprobaciones, confirmo que no existe un fallo real de funcionamiento, sino que el primer arranque de la máquina virtual requiere más tiempo del esperado y da la impresión de haberse quedado bloqueado cuando en realidad termina iniciándose correctamente. 

Ahora paso al inicio de la última vm back-mysql. 
Con esta vm, me pasa lo mismo, Vagrant no consigue conectarse inmediatamente por SSH porque Ubuntu tarda más de lo esperado en completar la inicialización del sistema. Aunque Vagrant muestra errores o avisos relacionados con la conexión, al comprobar el estado de las máquinas desde VirtualBox se verifica que terminan arrancando correctamente y que el servicio SSH está activo. 

Verifico que las cuatro máquinas virtuales están listas:

![VMs listas](images/vms_running.png)



### 4. Comprobar las IPs y conectividad entre las capas. 

Entro por ssh en todas las VMs y con `ip a` compruebo sus IPs estáticas.

![IP balanceador](images/ip_balanceador.png)

![IP frontal1](images/ip_frontal1.png)

**INCIDENCIA:**

Tanto en la conexión de ssh de frontal2 y back-mysql como nombre de host me aparece ubuntu-jammy y no aparece la IP estática.

![Incidencia ssh/ip a](images/incidencia_ip_a.png)

He verificado el vagrantfile y está correcto. 

Lo que he hecho con frontal2 y back-mysql es elimianrlas `vagrant destroy frontal2/back-mysql-f` y luego hacer un `vagrant up` y una vez terminado me he conectado por ssh a la vm `vagrant ssh frontal2/back-mysql` y ha funcionado correctamente.

![Ip y hostname frontal2](images/ip_frontal2.png)

![IP y hostname back-mysql](images/ip_back-mysql.png)


Ahora paso a comprobar la conectividad entre los diferentes niveles:
1. Pruebo la conectividad desde el balanceador a los frontales.
   ![Ping desde balanceador a frontales](images/ping_balanceador.png)

2. Hago ping desde los frontales a la vm de la base de datos.
   ![Ping desde frontal1 a back-mysql](images/ping_frontal1.png)
   ![Ping desde frontal2 a back-mysql](images/ping_frontal2.png)



### 5. Instalar servidor web (Apache) en frontales.
Me conecto por ssh al frontal1 y ejecuto estos comandos:
```bash
sudo apt update
sudo apt install -y apache2
sudo systemctl status apache2
```
![Apache en frontal1](images/apache_frontal1.png)
![Apache en frontal2](images/apache_frontal2.png)





















