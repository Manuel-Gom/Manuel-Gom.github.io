***Tema 1: Instalación y configuración de Debian***

***Autor: Manuel Gómez Ruiz***

***Fecha: 13/10/2024***

***Asignatura: Despliegue de Aplicaciones Web***

***Curso: 2º Desarrollo de Aplicaciones Web***

## Objetivo de la práctica: Instalación y configuración de herramientas clave para la documentación y desarrollo de proyectos en Linux.

Actualizamos repositorios: ***sudo apt update** y **sudo apt upgrade**

En los sistemas operativos de Linux, la gestión de paquetes se realiza de forma centralizada para evitar que pip realice cambios que podrían romper el sistema, interfiriendo con los paquetes gestionados con apt.

Para evitar problemas de este tipo lo mejor es usar un entorno virtual.

Pero primero comprobemos si tenemos instalado Python 3, con **python3 –version**, en caso contrario lo instalamos con **sudo apt install python3**

Hacemos lo mismo con pip, escribe **pip3 –version** y si no esta instalado introduce el comando **sudo apt install python3-pip**

Instalamos el módulo venv: **sudo apt install python3-venv**

Creamos un entorno virtual: **python3 -m venv myenv**

Activamos el entorno virtual: **source myenv/bin/activate**

Recuerda usar el comando **deactivate** para salir del entorno virtual

Ahora dentro del entorno instalamos el paquete de mkdocs: **pip install mkdocs-material**

Si pese a los problemas que puede ocasionar, no queremos instalarlo en el entorno virtual sino en el equipo, utiliza el siguiente comando: 
**sudo pip install mkdocs-material –break-system-packages**

Crea una carpeta en el directorio donde quieras que se ubique tu proyecto y realiza el comando **mkdocs new .**

El comando anterior iniciará la documentación de tu proyecto usando un ejecutable mkdocs.

Entra al fichero mkdocs.yml y añade las siguientes líneas:

**site_url: https://mydomain.org/mysite**
**theme:**
  **name: material**

Ten en cuenta que el valor de site_url o site_name no tienen porque estar asociados al nombre de tu carpeta, pon el que quieras.

Añadimos la documentación de nuestro proyecto, para ello voy a necesitar un editor de texto, por lo que voy a descargar Visual Studio Code mediante comandos.

Primero instalamos las dependencias requeridas.

**sudo apt update**

**sudo apt install software-properties-common apt-transport-https wget**

Añadimos la clave GPG de Microsoft.

**wget -qO- https://packages.microsoft.com/keys/microsoft.asc | sudo gpg --dearmor -o /usr/share/keyrings/packages.microsoft.gpg**

Añadimos el Repositorio de Visual Studio Code.

**echo "deb [arch=amd64 signed-by=/usr/share/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" | sudo tee /etc/apt/sources.list.d/vscode.list**

Actualizamos el índice de paquetes.

**sudo apt update**

Instalamos Visual Studio Code

**sudo apt install code**

Para ejecutarlo escribe el comando **code**
