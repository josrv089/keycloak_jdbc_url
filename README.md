# keycloak modificando el url de conexión predeterminado.

Los contenedores se han realizado por medio de Docker compose, pero estos pueden ser adaptados al uso necesario.
Para este ejemplo se adapta una versión de keycloak 15.0.1 conectado a una base de datos postgresql y el proceso se crea por dos formas, usando un volumen, y creando un contenedor con Dockerfile, cualquiera de las dos da un resultado similar.

## Generación por medio de un volumen.

Utilizar el archivo docker-compose en la carpeta volumen.
Para este caso el contenedor se ha creado usando la imagen jboss/keycloak:15.0.1 y el cambio necesario es realizar un volumen donde se mapee la carpeta /cli/databases/postgres que contiene el archivo de configuración que se ha modificado para aceptar la nueva variable que contiene el String de conexión. Agregar el volumen de la siguiente forma: 

	volumes:
      - './cli/databases/postgres/:/opt/jboss/tools/cli/databases/postgres/'

Esto implicará que se debe agregar un nuevo parámetro con el nombre JDBC_URL y que contiene toda la ruta a la cual se conectará keycloak.

Nota: El volumen está mapeado a la carpeta de postgres, este debe de ser cambiado a la que se necesite modificar, por ejemplo Oracle.
En la carpeta de configuración de postgresql debe existir el archivo “change-database.cli” al cual se le ha modificado la variable que contiene el string de conexión ‘connection-url’ y se agrega env.JDBC_URL:

        connection-url=${env.JDBC_URL}

Para poder correr el docker-compose up debe de existir la carpeta ./cli/databases/postgres/ con todo su contenido.


## Generación por medio de Dokerfile.


Utilizar el archivo docker-compose en la carpeta dockerfile.
Para este caso se debe de validar que exista el archivo Dockerfile, este ya cuenta con la descarga de keycloak en la versión 15.0.1 y se encarga de copiar el archivo change-database.cli a la carpeta de confiuración de la base de datos de postgres.

se debe compilar la imagen con el comando docker build -t <nombre_imagen> .

Una vez creada la imagen en el docker-compose se debe de agregar el nombre de la imagen creada en la sección image.

Una vez realizado esto se puede iniciar el contenedor con el comando docker-compoose up