**Tema 1: Instalación y configuración de Debian**

***Autor: Manuel Gómez Ruiz***

***Fecha: 13/10/2024***

***Asignatura: Despliegue de Aplicaciones Web***

***Curso: 2º Desarrollo de Aplicaciones Web***

## Objetivo de la Práctica: Configurar un entorno seguro de conexión y administración remota entre un cliente y un servidor Debian utilizando SSH.

Una vez instalado el servidor Debian, ve al terminal e introduce el comando **ip a** para ver su IP, también recuerda hacer un **sudo apt update && sudo apt upgrade** (y habilita la instalación del servidor FTP en el proceso de instalación, en caso contrario, instala el paquete vsftpd o busca ayuda)

Guárdate el nombre de usuario y la IP del servidor, ve a la terminal de tu cliente e introduce el comando **ssh -l usuario-servidor ip-servidor**

### Otorgar permisos a nuestro usuario.

Haz un **sudo nano /etc/sudoers** o **/etc/sudoers.tmp** dentro de este fichero introduce el nombre de usuario de tu servidor y otorgale permisos de sudo (administrador) añadiendo la siguiente línea al script: **usuario-servidor ALL=(ALL:ALL) ALL**

Finalizamos la sesión por SSH y comprobamos que tenemos permisos en el usuario del cliente con los comandos: **sudo -v** y **timeout 2 sudo id && echo Access granted || echo Access Denied**

### Configuración de SSH

Creamos el par de claves pública y privada en el cliente con el comando **ssh-keygen -b 4096**

Instalamos las claves públicas SSH de nuestro cliente en nuestro servidor, para así evitar que el cliente tenga que autentificarse cada vez que quiera acceder al servidor, usa el comando: 
**ssh-copy-id usuario-servidor@ip-servidor**

Por motivos de seguridad, en nuestro servidor damos acceso a la carpeta que contiene las claves SSH solo al usuario que creó el directorio, usa los comandos: **chmod 700 .ssh/** y **chmod 600 .ssh/authorized_keys**

Inicia sesión en el servidor desde el cliente: **ssh usuario-servidor@ip-servidor**
