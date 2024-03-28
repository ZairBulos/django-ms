# Introducción a Django

Django es uno de los marcos web de Python más populares. Es especialmente eficaz cuando se trabaja con una aplicación controlada por datos, donde el objetivo principal es proporcionar un front-end a una base de datos. Django incluye numerosas características integradas para simplificar el proceso de desarrollo. En este módulo, exploramos las ventajas de Django, cómo se instala y cómo crear su primer proyecto.

## Introducción

Django es un conocido marco web del lado servidor. Su principal enfoque se centra en los sitios web controlados por datos con utilidades necesarias de forma frecuente y previamente integradas en la plataforma. Django se basa en Python. Ofrece flexibilidad y una colección de extensiones compiladas por la comunidad.

## ¿Qué es Django?

Django es un marco gratuito y de código abierto que se lanzó por primera vez en 2005. Es adecuado para el desarrollo web tanto de front-end como de back-end. Las bibliotecas de Python integradas facilitan el desarrollo rápido.

### Tipos de aplicación

Django ofrece una solución completa de marco, lo que significa que le proporciona todo lo que necesita para implementar rápidamente sus proyectos. Django ofrece una excelente seguridad de serie y una amplia comunidad de usuarios. Además, se puede escalar a petición. Mediante el uso de Django, se pueden desarrollar aplicaciones web complejas y controladas por bases de datos que pueden incluir lo siguiente:

- Machine Learning
- Plataformas de comercio electrónico
- Análisis de datos
- Administración de contenido

### Django frente a Flask

| Django                                          | Flask                                                |
|------------------------------------------------ |----------------------------------------------------- |
| Marco de pila completo                          | Marco web ligero                                     |
| Ideal para aplicaciones controladas por datos   | Ideal para API y servicios existentes                |
| Posiblemente más como una curva de aprendizaje  | Posiblemente menos como una curva de aprendizaje     |
| Seguridad de serie                              | Bibliotecas adicionales necesarias para la seguridad |
| Motor de plantillas HTML personalizado          | Motor de plantillas HTML Jinja                       |

## Ejercicio: Instalación de Django

La creación de un proyecto de Django es similar a la de cualquier aplicación de Python. Comenzaremos por crear una carpeta y, después, instalaremos el paquete mediante `pip`.

## Exploración de los conceptos básicos en Django

### Proyectos frente a aplicaciones

| Proyecto                                                                        | Aplicación                                           |
| ------------------------------------------------------------------------------- | ---------------------------------------------------- |
| Solo hay un proyecto                                                            | Puede haber muchas aplicaciones en el proyecto único |
| Incluye la configuración o aplicaciones necesarias para un sitio web específico | Es un componente del sitio web de mayor tamaño       |
| Los proyectos no se usan en otros proyectos                                     | Las aplicaciones pueden usarse en varios proyectos   |

### Vistas

Las vistas son otro componente de las aplicaciones de Django que cumplen una función específica en la aplicación. Las vistas incluyen todo el código necesario que devolverá una respuesta específica cuando se solicite, como una plantilla o una imagen. Incluso pueden redirigirse a otra página en caso de que la solicitud no siga la lógica necesaria en la función.

### Asignación de la URL

A la asignación de direcciones URL en Django se le llama `URLconf` y sirve como tabla de contenido para la aplicación. Una vez solicitada una dirección URL, este módulo busca el vínculo adecuado en el proyecto y redirige la solicitud al archivo de vistas incluido en la aplicación. Después, la vista procesa la solicitud y realiza las operaciones necesarias.

La función `URLconf` desempeña un papel clave porque permite disponer de una manera sencilla de administrar y organizar las direcciones URL en la aplicación. También proporciona mayor libertad para cambiar las raíces de ruta sin interrumpir la aplicación.

## Ejercicio: Creación del primer proyecto

### Creación de un proyecto con django-admin

Un proyecto de Django es el contenedor para todo el proyecto y cualquier aplicación que vayamos a crear.

````bash
django-admin startproject helloproject .
````

### Exploración de la estructura del proyecto

````text
manage.py
helloproject/
  __init__.py
  asgi.py
  settings.py
  urls.py
  wsgi.py
````

- La utilidad de la línea de comandos manage.py se crea en cada proyecto de Django. Tiene la misma función que django-admin.
- **helloproject** se considera como el paquete de Python para el proyecto.
- **__init__.py** es un archivo vacío que funciona para indicar a Python que este directorio se debe considerar como un paquete.
- **settings.py** incluye todos los valores o configuraciones.
- **urls.py** incluye las direcciones URL del proyecto.
- **asgi.py** y **wsgi.py** sirven como punto de entrada para los servidores web en función del tipo de servidor implementado.

### Ejecución del proyecto

````bash
python manage.py runserver
````

### Creación de la aplicación Hola mundo

````bash
python manage.py startapp hello_world
````

### Registro de la aplicación en el proyecto

Dado que las aplicaciones y los proyectos son independientes en Django, debe registrar la aplicación en el proyecto. Para ello, se actualiza la variable `INSTALLED_APPS` dentro de settings.py en el proyecto y se agrega una referencia a la clase config para la aplicación. La clase config se encuentra en apps.py y tiene el mismo nombre que el proyecto. En nuestro ejemplo, la clase se llamará `HelloWorldConfig`.

1. Dentro de **helloproject**, abra **settings.py**.
2. Busque la lista `INSTALLED_APPS`, que debe estar en la línea 33.
3. Agregue lo siguiente al final de la lista, entre corchetes ([ ]):
  ````python
  'hello_world.apps.HelloWorldConfig',
  ````
4. La lista `INSTALLED_APPS` actualizada debería ser similar a la siguiente imagen:
  ````python
  INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'hello_world.apps.HelloWorldConfig',
  ]
  ````
5. Guarde todos los archivos

## Descripción de las rutas y las vistas

Las vistas y rutas son fundamentales para cualquier marco web. Se usan para determinar qué información se debe mostrar al usuario y cómo accederá a ella

### Rutas de acceso

Todas las aplicaciones permiten a los usuarios ejecutar métodos o funciones diferentes mediante determinados mecanismos. Esta acción podría pulsar un botón de una aplicación móvil o ejecutar un comando desde la línea de comandos.

Para realizar solicitudes de usuario en una aplicación web, haga lo siguiente:

- Navegar a distintas direcciones URL.
- Escribir en ella.
- Seleccionar un vínculo.
- Pulsar un botón.
- Una ruta indica a Django qué función se va a ejecutar si el usuario realiza una solicitud para una dirección URL, o ruta, determinada.

Una dirección URL como https://adventure-works.com/about podría ejecutar una función denominada about. La dirección URL https://adventure-works.com/login podría ejecutar una función denominada authenticate.

Las rutas en Django se registran mediante la configuración de `urlpatterns`. Estos patrones identifican qué debe buscar Django en la dirección URL que el usuario solicita y determinan la función que debe controlar la solicitud. Estos patrones se recopilan en un módulo que Django denomina `URLconf`.

### Vistas

Las vistas determinan qué información se debe devolver al usuario. Las vistas son funciones o clases que ejecutan código en respuesta a la solicitud del usuario. Devuelven HTML u otros tipos de respuestas, como un error 404.

## Ejercicio: Creación de rutas y vistas

Una vez creada la estructura de la aplicación, podemos empezar a seguir los pasos para agregar nuestro propio código personalizado. Crearemos una vista y, después, registraremos una ruta dentro de un elemento `URLconf`.

### Crear la vista

1. Abra **views.py**, que estará dentro de **hello_world**.
2. Reemplace el código que hay dentro de **views.py** por el siguiente:
  ````python
  from django.shortcuts import render
  from django.http import HttpResponse

  def index(request):
    return HttpResponse("Hello, world!")
  ````

### Creación de la ruta

Una vez creada la vista, el paso siguiente consiste en asignarla a la dirección URL o ruta adecuada.

1. Cree un archivo en hello_world denominado urls.py.
2. Agregue el código siguiente al archivo urls.py nuevo.
    ````python
    from django.urls import path
    from . import views

    urlpatterns = [
        path('', views.index, name='index'),
    ]
    ````

La parte más importante de este código es la tupla `urlpatterns`. Esta tupla es donde las vistas y las direcciones URL están conectadas o asignadas. Tal como se puede ver, hemos importado nuestro archivo **views.py** para poder usarlo en la línea `urlpatterns`.

### Registro de nuestro elemento URLconf con el proyecto

Nuestro elemento `URLconf` recientemente creado está dentro de nuestra aplicación **hello_world**. Dado que el proyecto controla todas las solicitudes de los usuarios, es necesario registrar nuestro elemento `URLconf` en el archivo principal **urls.py**, que está dentro de **helloproject**.

1. Abra **urls.py** dentro de **helloproject**.
2. Reemplace la línea que lee `from django.urls import path`:
    ````python
    from django.urls import include, path
    ````
    El uso de `include` nos permite importar módulos de `URLconf`, y path se usa para identificar la raíz de `URLconf`.
3. Dentro de la lista, debajo de la línea que lee `urlpatterns = [`, agregue el código siguiente:
    ````python
    path('', include('hello_world.urls')),
    ````
    Este código registra nuestro elemento `URLconf`.
