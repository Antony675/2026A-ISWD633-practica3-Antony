# BIND MOUNT
En un bind mount mapeamos (montar) un directorio o archivo específico del sistema de archivos del host con una parte del sistema de ficheros del contenedor.

```
docker run -d --name <nombre contenedor> -v <ruta carpeta host>:<ruta carpeta contenedor> <imagen> 
```
ó
```
docker run -d --name <nombre contenedor> --mount type=bind,source=<ruta carpeta host>,target=<ruta carpeta contenedor> <imagen>
```
- destination, dst, target: La ruta donde se monta el archivo o directorio en el contenedor.
- source, src: El origen del montaje.
  
### En tu computador crear una carpeta llamada nginx y dentro de esta carpeta crea otra llamada html. Como se aprecia en la figura.
![Volúmenes](/Imagenes/directorio.PNG)

### Crear un contenedor con la imagen nginx:alpine, mapear todos por puertos, para la ruta carpeta host colocar el directorio en donde se encuentra la carpeta html en tu computador y para la ruta carpeta contenedor: /usr/share/nginx/html (esta ruta se obtiene al revisar la documentación de la imagen)
![Volúmenes](/Imagenes/volumen-host.PNG)
````
docker run -d --name servidor-web -p 8080:80 -v /home/tony/nginx/html:/usr/share/nginx/html nginx:alpine
````
![imagen](/Imagenes/1.png)


### ¿Qué sucede al ingresar al servidor de nginx?
![imagen](/Imagenes/2.png)

Al acceder a localhost:8080 a través del navegador, se observa un error 403 Forbidden (o una respuesta vacía). Esto ocurre porque la carpeta del host, que actualmente está vacía, se ha montado sobre la carpeta pública del servidor web, dejándolo sin un archivo index.html para servir.


### ¿Qué pasa con el archivo index.html del contenedor?

El archivo index.html original que provee la imagen nginx:alpine queda inaccesible o enmascarado (shadowed). En una operación de bind mount, el contenido del directorio del host se superpone y oculta temporalmente el contenido preexistente en el directorio de destino dentro del contenedor. El archivo original no se elimina de la imagen, pero el contenedor solo puede leer lo que existe en el directorio del host. 


### Ir a https://html5up.net/ y descargar un template gratuito, descomprirlo dentro de tu computador en la carpeta html
### ¿Qué sucede al ingresar al servidor de nginx?

El servidor web muestra inmediatamente la página principal del template que se ha descomprimido. Cualquier modificación, adición o eliminación de archivos que realice en el sistema de archivos de su host se reflejará en tiempo real dentro del contenedor. Esto demuestra la naturaleza de sincronización directa del bind mount.

![imagen](/Imagenes/3.png)
### Eliminar el contenedor

````
docker rm -f servidor-web
````

### ¿Qué sucede al crear nuevamente un contenedor montado al directorio definidos anteriormente?

El nuevo contenedor servirá el sitio web del template de manera inmediata. Esto evidencia la persistencia de datos independiente del ciclo de vida del contenedor. Aunque el contenedor original fue destruido, los datos permanecen intactos y seguros en el sistema de archivos de la máquina host, listos para ser consumidos por cualquier otra instancia que se monte en el mismo directorio.
