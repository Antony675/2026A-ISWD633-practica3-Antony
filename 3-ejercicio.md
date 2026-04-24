## Esquema para el ejercicio
![Imagen](/Imagenes/esquema-ejercicio3.PNG)

### Crear red net-wp
````
docker network create net-wp
````

### Para que persista la información es necesario conocer en dónde mysql almacena la información.
# COMPLETAR LA SIGUIENTE ORACIÓN. REVISAR LA DOCUMENTACIÓN DE LA IMAGEN EN https://hub.docker.com/
En el esquema del ejercicio carpeta del contenedor (a) es **/var/lib/mysql**
|
Según la documentación oficial de la imagen de MySQL, este es el directorio estándar donde el motor de base de datos relacional almacena sus tablas, metadatos y logs de transacciones

Ruta carpeta host: .../ejercicio3/db

### ¿Qué contiene la carpeta db del host?

Inicialmente, la carpeta db se encuentra completamente vacía, ya que fue recién creada en el sistema de archivos del host y aún no ha sido inicializada por el motor de base de datos.

### Crear un contenedor con la imagen mysql:8  en la red net-wp, configurar las variables de entorno: MYSQL_ROOT_PASSWORD, MYSQL_DATABASE, MYSQL_USER y MYSQL_PASSWORD

````
docker run -d \
  --name servidor-mysql \
  --network net-wp \
  -v "$(pwd)/db":/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=admin \
  -e MYSQL_DATABASE=db_wordpress \
  -e MYSQL_USER=usuario_wp \
  -e MYSQL_PASSWORD=password123 \
  mysql:8
````
![imagen](/Imagenes/4.png)
### ¿Qué observa en la carpeta db que se encontraba inicialmente vacía?

![imagen](/Imagenes/5.png)

Al observar la carpeta del host (comando ls -la db), se observa la estructura interna del motor de base de datos MySQL. Esto incluye archivos del sistema como ibdata1, archivos de registro de rehacer/deshacer (ib_logfile0, ib_logfile1), y el directorio específico db_wordpress que albergará las tablas de la aplicación.

### Para que persista la información es necesario conocer en dónde wordpress almacena la información.
# COMPLETAR LA SIGUIENTE ORACIÓN. REVISAR LA DOCUMENTACIÓN DE LA IMAGEN EN https://hub.docker.com/
En el esquema del ejercicio la carpeta del contenedor (b) es: **/var/www/html**

Ruta carpeta host: .../ejercicio3/www

*Este es el directorio raíz estándar configurado en el servidor web Apache, que viene integrado en la imagen oficial de WordPress para servir los scripts de PHP y recursos estáticos*
### Crear un contenedor con la imagen wordpress en la red net-wp, configurar las variables de entorno WORDPRESS_DB_HOST, WORDPRESS_DB_USER, WORDPRESS_DB_PASSWORD y WORDPRESS_DB_NAME (los valores de estas variables corresponden a los del contenedor creado previamente)

````
docker run -d \
  --name servidor-wordpress \
  --network net-wp \
  -p 9500:80 \
  -v "$(pwd)/www":/var/www/html \
  -e WORDPRESS_DB_HOST=servidor-mysql \
  -e WORDPRESS_DB_USER=usuario_wp \
  -e WORDPRESS_DB_PASSWORD=password123 \
  -e WORDPRESS_DB_NAME=db_wordpress \
  wordpress
````

### Personalizar la apariencia de wordpress y agregar una entrada
![imagen](/Imagenes/7.png)
### Eliminar el contenedor y crearlo nuevamente, ¿qué ha sucedido?


Al acceder nuevamente a localhost:9500, se observa que toda la configuración, personalización de apariencia y las entradas creadas se conservan intactas. Esto demuestra el principio de desacoplamiento en la virtualización, el contenedor fue destruido, pero el estado de la aplicación reside de forma segura y persistente en el sistema de archivos del host mediante el bind mount, sobreviviendo al ciclo de vida del contenedor.