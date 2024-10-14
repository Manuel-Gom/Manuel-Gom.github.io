***Tema 2 - Arquitectura Web. Implantación y administración de servidores web.***

***Autor: Manuel Gómez Ruiz***

***Fecha: 14/10/2024***

***Asignatura: Despliegue de Aplicaciones Web***

***Curso: 2º Desarrollo de Aplicaciones Web***

## Objetivo de la práctica: Instalar y configurar un servidor web Nginx en un Sistema Operativo Debian, de tal forma que permita conexiones por HTTPS y FTP.

### Instalación servidor web Nginx.

Primero, actualiza los repositorios de tu Sistema Operativo con los comandos **sudo apt update && sudo apt upgrade**.

Instala el paquete correspondiente que contiene el servidor Nginx **sudo apt install nginx**

Comprueba que se haya instalado correctamente y que el servicio esté activo, para ello utilizaremos el comando **sudo systemctl status nginx** para verificar el estado.

Crea las carpetas que alojarán tu sitio web, para ello usaremos el comando **mkdir -p** que crea múltiples carpetas de manera recursiva, nosotros crearemos dentro de **/var/www** una carpeta que se llamará como el nombre de nuestro servidor web y dentro de esta una carpeta que contendrá nuestro HTML, esas carpetas almacenarán los archivos de nuestro sitio web.

Nos dirigimos a la carpeta **html** recién creada y hacemos una clonación de un sitio web de GitHub con el comando **git clone https://github.com/cloudacademy/static-website-example**, esto copiará el repositorio de CloudAcademy haciendo que tengamos una plantilla para nuestro sitio web.

Ahora nos aseguramos de que el servidor Nginx tenga los permisos adecuados para acceder y servir los archivos, utilizando los comandos **sudo chown -R www-data:www-data /var/www/carpeta-sitio-web/html** y **sudo chmod -R /var/www/carpeta-sitio-web**

Hacemos un **ip a**, copiamos la IP y la ponemos en el navegador, debería de aparecer una paǵina con un mensaje que diga **Welcome to nginx!**

### Configuración de servidor web Nginx

Creamos y editamos el archivo de configuración del sitio web para personalizar Nginx, utilizando el comando **sudo nano /etc/nginx/sites-available/sitio-web**

(No hace falta que el archivo que vamos a crear tenga el mismo nombre que la carpeta en la que almacenamos nuestro archivos del sitio web /var/www/sitio-web, aunque sería una buena práctica, para mantener una estructura clara y organizada, lo mismo ocurre con el parámetro del fichero server_name)

```
    server {
        listen 80;
        listen [::]:80;
        server_name sitio-web;
        root /var/www/carpeta-sitio-web/html/static-website-example;
        index index.html index.html nginx-debian.html;
        location / {
            try_files $uri $uri/= 404;
        }
    }
```

Hacemos el enlace simbólico para hacer que Nginx reconozca nuestro sitio web como activo, agregándolo a la carpeta **sites-enabled** y así que esté disponible en el servidor, utiliza el comando **sudo ln -s /etc/sites-available/sitio-web /etc/nginx/sites-enabled/**

Haz un **sudo systemctl restart nginx** para que se actualicen los cambios y un **sudo systemctl status nginx** para comprobar que se han aplicado correctamente.

En caso de error, revisa el error que te devuelve al hacer el status y comprueba que el fichero de configuración **/etc/nginx/sites-available/sitio-web** está bien escrito, cuando lo corrijas vuelve a hacer un restart.

En nuestra máquina anfitriona, entra a la ruta **C:\Windows\SYstem32\drives\etc\hosts** con permisos de administrador y añade en la última línea la **IP del servidor Debian** junto al **server_name** que hayas añadido en tu fichero **/etc/nginx/sites-available/sitio-web**, esto hará que podamos acceder al sitio web de nuestra máquina virtual también desde nuestro anfitrión con su IP.

Comprobamos que las peticiones se estén registrando en los archivos de registro (logs) de Nginx, con los comandos **sudo cat /var/log/nginx/access.log** y **sudo cat /var/log/nginx/error.log**

### FTP

Para configurar un servidor FTP en Debian y así permitir la transferencia de archivos, debemos de instalar vsftpd, que es un servidor FTP ligero y seguro, usa el comando: **sudo apt install vsftpd**

Una vez instalado, en el Home crea una carpeta llamada FTP, que será a la que accederán los clientes a través del puerto 21, para la subida o descarga de archivo, comando: **mkdir /home/nombre-usuario/ftp**

Entra al fichero **/etc/vsftpd.conf**, para permitir el acceso a usuarios locales, añadir SSL, certificado, etcétera.

```
    rsa_cert_file=/etc/ssl/private/vsftpd.pem
    rsa_private_key=/etc/ssl/private/vsftpd.pem
    ssl_enable=YES
    allow_annon_ssl=NO
    force_local_data_ssl=YES
    force_local_logins_ssl=YES
    ssl_tlsv1=YES
    ssl_sslv2=NO
    ssl_sslv3=NO
    require_ssl_reuse=NO
    ssl_cipher=HIGH

    local_root=/home/nombre-usuario/ftp
```

Reiniciamos el servicio **vsftpd** para que los cambios hagan efecto, **sudo systemctl restart --now vsftpd** y haz un **sudo systemctl status vsftpd**, al igual que dije antes debe devolver **active(running)**, en caso contrario es que algo haz escrito mal en el archivo de configuración **/etc/vsftpd.conf**, mira el mensaje de error que devuelve y corrige el error, antes de volver a intentar otro restart.

Instala Filezilla en el equipo principal y realiza un conexión FTP al servidor Debian a través del puerto 21, para ello necesitarás la IP del servidor, su nombre de usuario y contraseña.

Al hacerlo debería de aparecernos un certificado de seguridad, dale a aceptar y podrás acceder a la carpeta **ftp** del servidor con éxito.

También puedes acceder al servidor a través del puerto 22, aunque no es recomendado por motivos de seguridad ya que tendrías acceso a todo el contenido del servidor de forma remota.

### HTTPS

Para habilitar la seguridad de tu sitio web, es importante configurar el HTTPS, que garantiza que la información entre el cliente y el servidor esté cifrada. Para ello, necesitaremos crear un certificado SSL.

Primero, instala openssl, herramienta fundamental para generar certificados SSL, usa el comando **sudo apt install openssl**

Genera el certificado de seguridad, utilizando el comando **sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/certs/nginx-selfsigned.crt**

Configuramos Nginx para que utilice HTTPS. Para ello, edita el archivo de configuración usando **sudo nano /etc/nginx/sites-availables/sitio-web**, y añade las rutas al certificado, la clave privada y asegurate que Nginx escuche en el puerto 443.

```                                                   
    server {
        listen 80;
        listen [::]:80;
        server_name sitio-web.com www.sitio-web.com;
        root /var/www/carpeta-sitio-web;
        index index.html index.html index.nginx-debian.html;
        return 301 https://$server_name$request_uri/;
    }

    server {
        listen 443 ssl;
        server_name sitio-web.com www.sitio-web.com;
        ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
        ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;
        root /var/www/carpeta-sitio-web;
        index index.html;
        location / {
            try_files $uri $uri/ = 404;
        }
    }

```

Comprobamos con el comando **nginx -t** que la sintaxis y la configuración son válidas.

Modificamos nuestro nuestro fichero host en la máquina anfitrión, con las direcciones añadidas anteriormente a server_name.

Nos dirigimos a **/var/www/sitio-web/**, hacemos un **sudo rm -rdf ./html** y reiniciamos el servidor Nginx.

Comprobamos ahora, conectandonos desde el anfitrión que funcionan las conexiones HTTP y HTTPS, para ello introduce **http://www.nombre-servidor.com** y **https://www.nombre-servidor.com**, el resultado debería ser el visualizado de una nueva plantilla web, pero si entras por HTTP te avisará que la conexión no es segura.