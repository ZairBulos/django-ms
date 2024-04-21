# Implementación de una aplicación de Django en Azure mediante PostgreSQL

Para que la aplicación esté disponible en todo el mundo, debe hospedarla en algún lugar. Aquí implementará la aplicación en Azure y creará una base de datos para los datos.

## Implementar en Azure

Como probablemente sepa, el desarrollo y la producción tienen un conjunto diferente de requisitos. Preparar una aplicación para producción puede llevar algunos pasos. Afortunadamente, no es necesario que el proceso de implementación real sea demasiado complejo.

En este módulo, se le guiará por los pasos necesarios para preparar una aplicación de Django para producción. Después, creará una base de datos y un entorno de hospedaje para la aplicación mediante Visual Studio Code. Terminará por implementar el sitio en Azure y ver la ejecución de la aplicación en la nube.

## Obtención del proyecto de inicio

En este módulo, trabajará en un sitio web de refugios para perros. Este proyecto recopila información sobre todos los refugios para perros y los perros que están listos para su adopción en todo el país o región. El objetivo ficticio de esta aplicación es encontrar hogares adecuados para los perros de forma rápida. La aplicación se pone en contacto con los usuarios que quieren llevar a cabo una adopción, no solo en las zonas cercanas a los refugios, sino en todo el país o región.

### Clonación del repositorio de inicio

````bash
git clone https://github.com/MicrosoftDocs/mslearn-django-deployment
cd mslearn-django-deployment/starter
````

### Creación del entorno virtual

````bash
py -3 -m venv venv
.\\venv\\Scripts\\activate
````

### Instalación de Django

````bash
pip install -r requirements.txt
````

### Inicio del servidor

````bash
python manage.py runserver
````

## Consideraciones de la implementación

Una aplicación que se ejecuta en un entorno de producción tiene un conjunto distinto de necesidades y requisitos que el que hay en un entorno de desarrollo. En concreto, los problemas de seguridad y de rendimiento no son tan críticos durante el desarrollo como lo son en producción. Por lo tanto, debe asegurarse de que el sitio web está configurado correctamente antes de la implementación.

### Modo de depuración

Como desarrollador, quiere ver los mensajes de error que puede generar la aplicación. Sin embargo, esta información puede proporcionar a un atacante información sobre cómo se ejecuta la aplicación, lo que podría permitir el acceso no autorizado. Por lo tanto, en *settings.py*, establezca la opción `DEBUG` en `False` antes de implementar la aplicación en producción.

### Clave secreta

Para proteger la información confidencial, Django usa una clave secreta para firmar los valores que no se deben alterar. Durante el desarrollo, la clave secreta se almacena en texto no cifrado en *settings.py*. Cuando se implementa en producción, la clave secreta se debe leer desde una ubicación más segura, como Configuración de App de Azure o Azure Key Vault.

### Hosts permitidos

El archivo *settings.py* contiene una lista de nombres del servidor denominada `ALLOWED_HOSTS`. Esta lista determina el lugar desde el que se puede ejecutar la aplicación. De manera predeterminada, la lista vacía permite que la aplicación se ejecute desde localhost. Actualice esta configuración antes de implementar en el host de producción.

### Archivos estáticos

Los archivos estáticos son archivos que no forman parte del sistema de plantillas de Django. Normalmente, estos archivos incluyen archivos JavaScript o CSS. Pero también podrían incluir archivos HTML estáticos. En concreto, el sitio de administración utiliza archivos estáticos para el estilo y el formato.

Mientras la aplicación está en desarrollo, Django sirve automáticamente los archivos estáticos. En producción, debe configurar un servicio para que sirva cualquier archivo estático. Una solución común es una biblioteca [WhiteNoise](http://whitenoise.evans.io/en/stable/).

Durante el proceso de implementación, todos los archivos estáticos se recopilan en la ubicación que indica `STATIC_ROOT` en *settings.py*. Se recopilan mediante la ejecución de `python manage.py collectstatic`. Azure ejecuta este comando automáticamente, por lo que no es necesario ejecutarlo localmente antes de la implementación.

## Preparación de la aplicación para la implementación

Configure la aplicación para producción mediante la actualización de algunas de las opciones más comunes.

### Incorporación de bibliotecas

Usará dos bibliotecas nuevas para el proyecto:

- `whitenoise` para servir archivos estáticos.
- `psycopg2-binary` para conectarse a PostgreSQL, la base de datos de producción.

1. Abra **requirements.txt**.
2. Agregue el código siguiente al final del archivo **requirements.txt**.
  ```text
  whitenoise
  psycopg2-binary
  ```
3.Ejecute el comando siguiente para instalar las bibliotecas.
  ```bash
  pip install -r requirements.txt
  ```

### Creación de un archivo de valores de producción

Los valores que se asignan a dos parámetros principales, `ALLOWED_HOSTS` y `DATABASES`, dependen del entorno que hospeda la aplicación. Los valores predeterminados están diseñados para el desarrollo. Para ejecutar la aplicación en producción, asegúrese de que estos valores se han actualizado correctamente.

`ALLOWED_HOSTS` controla los servidores que pueden hospedar o ejecutar la aplicación. Lo configurará para permitir que el sitio se ejecute desde Azure y localmente. `DATABASES` incluye la lista de cadenas de conexión disponibles.

Una forma habitual de configurar los valores es crear un segundo archivo de Python que incluya la colección de valores para producción. Entonces, una comprobación al final del settings.pydetermina si debe usar las configuraciones de producción.

Ahora creará un archivo de configuraciones de producción y añadirá la comprobación para determinar si su aplicación está operando en la producción:

1. Cree un archivo dentro de **project**. Asígnele el nombre **azure.py**.
2. Agregue el código siguiente para importar `os`.
  ```python
  from .settings import *
  import os
  ```
3. Agregue el código siguiente al final del archivo para invalidar `ALLOWED_HOSTS`, a fin de permitir que Azure hospede la aplicación y defina orígenes de confianza.
  ```python
  ALLOWED_HOSTS = [os.environ['WEBSITE_HOSTNAME']] if 'WEBSITE_HOSTNAME' in os.environ else []
  CSRF_TRUSTED_ORIGINS = ['https://'+ os.environ['WEBSITE_HOSTNAME']] if 'WEBSITE_HOSTNAME' in os.environ else []
  ```
4. Agregue el código siguiente a fin de configurar la cadena de conexión de base de datos para PostgreSQL.
    ```python
    hostname = os.environ['DBHOST']
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql',
            'NAME': os.environ['DBNAME'],
            'HOST': hostname + ".postgres.database.azure.com",
            'USER': os.environ['DBUSER'],
            'PASSWORD': os.environ['DBPASS'] 
        }
    }
    ```
5. Agregue el código siguiente a la parte inferior del archivo para habilitar `whitenoise`, que servirá archivos estáticos.
    ```python
    MIDDLEWARE = [
        'django.middleware.security.SecurityMiddleware',
        # Enables whitenoise for serving static files
        'whitenoise.middleware.WhiteNoiseMiddleware',
        'django.contrib.sessions.middleware.SessionMiddleware',
        'django.middleware.common.CommonMiddleware',
        'django.middleware.csrf.CsrfViewMiddleware',
        'django.contrib.auth.middleware.AuthenticationMiddleware',
        'django.contrib.messages.middleware.MessageMiddleware',
        'django.middleware.clickjacking.XFrameOptionsMiddleware',
    ]
    STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
    ```
6. Establezca `SECRET_KEY` con una lectura de las variables de entorno mediante la incorporación del código siguiente.
    ```python
    SECRET_KEY = os.getenv('SECRET_KEY')
    ```
7. Deshabilite el modo de depuración mediante la incorporación del código siguiente.
    ```python
    DEBUG = False
    ```

### Configuración de la aplicación para usar el archivo de valores de producción

1. Abrir **settings.py**.
2. Agregue el código siguiente al final del archivo para invalidar la configuración necesaria cuando la aplicación se ejecuta en producción.
    ```python
    import os
    if 'WEBSITE_HOSTNAME' in os.environ: # Running on Azure
        from .azure import *
    ```
3. Guarde todos los archivos.

## Consideraciones de la implementación

Al implementar una aplicación en producción en la nube, debe tener en cuenta algunas consideraciones. Se tiene que determinar cómo implementar la aplicación y qué base de datos se va a usar. También es necesario asegurarse de que el entorno de producción está listo.

### Opciones de implementación

Puede realizar la implementación en Azure de varias maneras. Una de las formas más cómodas es usar una de estas extensiones para Visual Studio Code:

- [Bases de datos de Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb).
- [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).

Bases de datos de Azure permite crear el servidor de base de datos y la base de datos. Azure App Service permite crear el host web, configurarlo e implementarlo.

### Consideraciones sobre la base de datos

Django está diseñado para aplicaciones web controladas por datos. Por lo tanto, cada proyecto de Django suele incluir una base de datos. Durante el desarrollo, normalmente se usa SQLite, que es un motor de base de datos basado en archivos.

SQLite es una solución perfecta para el desarrollo, ya que no requiere ninguna instalación o servicios especiales. Sin embargo, los requisitos para la producción normalmente incluyen el escalado, el rendimiento y la fiabilidad. SQLite no está diseñado para administrar estos requisitos de producción.

Django es compatible de forma nativa con muchas bases de datos, como MySQL, PostgreSQL y MariaDB. También puede encontrar proveedores para habilitar la compatibilidad con SQL Server, MongoDB y muchas otras bases de datos.

Al crear el archivo de configuración de producción, ha configurado el entorno para PostgreSQL. PostgreSQL es una de las bases de datos más populares para Django. También es compatible con Azure.

### Creación del esquema de la base de datos

Django administra el esquema de la base de datos mediante migraciones. Django puede generar la base de datos SQL que se va a crear o bien puede actualizar el esquema. También puede usar el comando `makemigrations` para hacer que Django actualice la base de datos directamente.

Para ejecutar migraciones en la base de datos, puede proteger el shell (o SSH) en App Service. Este método permite ejecutar comandos en el host web del mismo modo en que se ejecutan localmente.