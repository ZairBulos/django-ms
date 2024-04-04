# Creación de vistas y plantillas en Django

Django tiene un motor de plantillas para crear HTML dinámico. Este motor crea la información a la que el usuario quiere acceder, la cual se presenta mediante vistas. 

## Introducción

Al empezar a crear sitios web por primera vez, lo natural es tener páginas estáticas. Pero ¿qué ocurre si quiere ser un poco más creativo y hacer que los datos cambien por usuario o por página?

Aquí es donde el uso de plantillas puede facilitar el proceso de desarrollo de las aplicaciones. Las plantillas le ofrecen la opción de usar la misma página varias veces en todo el sitio, con secciones de datos que pueden cambiar continuamente en función de la solicitud.

## Ejercicio: Obtención del proyecto de inicio

En este módulo, trabajaremos en un sitio web de refugios para perros. Este proyecto se centra en recopilar información sobre todos los refugios de perros existentes y los perros que esperan ubicar. La esperanza ficticia de esta aplicación es que los perros podrán encontrar hogares adecuados con más rapidez, ya que habrá personas de todo Estados Unidos que quieran adoptarlos, y no solo de su área local.

Django es un buen marco para este proyecto porque proporciona una ruta para desarrollar rápidamente una aplicación orientada al cliente. También proporciona una función de administración y base de datos establecida a la que pueden acceder fácilmente los empleados para obtener una actualización rápida.

### Clonación del repositorio de inicio

````bash
git clone https://github.com/MicrosoftDocs/mslearn-django-views-templates
cd mslearn-django-views-templates/starter
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

## Introducción a las vistas

En programación, una vista suele ser un componente que muestra información al usuario. Las tareas que realiza una vista pueden variar en función del marco de trabajo y de las convenciones, incluida la responsabilidad de cargar los datos.

En Django, una vista normalmente se encarga de lo siguiente:

- Validar la solicitud de un usuario.
- Cargar o modificar los datos adecuados.
- Devolver una plantilla HTML con la información al usuario.

### Creación de una vista

Para crear una vista desde cero en Django, normalmente se crea una función. Normalmente, la función contiene el código adecuado para realizar lo siguiente:

- Realizar la tarea que el usuario ha solicitado.
- Devolver una plantilla con los datos adecuados que se van a mostrar al usuario.

Las funciones de vista siempre toman al menos un parámetro denominado `request`, que representa la solicitud del usuario. Puede proporcionar más parámetros según sea necesario si espera más información del usuario en la dirección URL, como el nombre o el id de un elemento.

#### Errores 404

Un error 404 en las aplicaciones web significa "no encontrado". Como procedimiento recomendado, debe devolver un 404 cada vez que se realiza una solicitud para un objeto que no existe.

Django proporciona métodos abreviados de teclado para intentar cargar datos:

- `get_object_or_404` y `get_list_or_404`: carga un objeto por una clave principal o devuelve un 404 al usuario si un objeto no se encuentra.
- `get_list_or_404`: realiza la misma operación que el otro método abreviado de teclado, excepto por el hecho de que acepta un parámetro de filtro.

#### Representación de la plantilla

El motor de plantillas de Django tomará la plantilla HTML que creemos, la combinará con los datos que proporcionemos y emitirá el código HTML para el explorador. La función auxiliar para realizar esta tarea es `render`.

La función `render` necesita el objeto que representa la solicitud, que es el parámetro `request` que resaltamos anteriormente. También puede pasar el nombre de la plantilla, normalmente un archivo HTML que residirá en una carpeta denominada *templates*.

Para pasar datos a la plantilla, debe proporcionar a `render` un objeto de diccionario `context`. El objeto context contiene un conjunto de pares clave-valor, en los que cada clave se convierte en una variable de la plantilla.

#### Ejemplo

````python
def shelter_list(request):
    shelters = Shelter.objects.all()
    context = { 'shelters': shelters }
    return render(request, 'shelter_list.html', context)
````

### Registro de una ruta de acceso

Casi cualquier marco web usa rutas de acceso para procesar las solicitudes de usuario. Las rutas de acceso convierten la parte de la dirección URL que hay después del nombre de dominio y antes de la cadena de consulta (que sigue al signo de interrogación) en una llamada de función.

Una llamada a `www.contoso.com/shelters` podría llamar a una función para enumerar todos los refugios, mientras que `www.contoso.com/shelters/1` podría llamar a una función para mostrar un refugio con un identificador de 1. Para registrar las rutas de acceso en Django, se debe crear un URLconf.

Supongamos que tenemos un módulo denominado `views`, que es una convención de Django. Se puede enrutar el tráfico de índices en el que no se especifica una ruta de acceso (como www.contoso.com) a una función de `views` denominada `index`, y asignarle un nombre `index` mediante la siguiente ruta de acceso:

````python
path('', views.index, 'index')
````

También podemos crear carpetas virtuales para solicitudes específicas. Por ejemplo, si quisiéramos enumerar todos los refugios si alguien solicita /shelters, podríamos usar el comando siguiente:

````python
path('shelters', views.shelter_list, 'shelter_list')
````

#### Parámetros de dirección URL

Es una práctica común pasar parámetros a una aplicación como parte de la dirección URL, como un identificador o un nombre. Dado que estos valores cambiarán, no queremos codificarlos de forma rígida en nuestra ruta de acceso. En Django, puede especificar un parámetro mediante una sintaxis especial. En esa sintaxis, puede indicar el tipo de datos que espera, como un entero, y un nombre.

Por ejemplo, para crear una ruta de acceso para que alguien solicite un refugio específico mediante un identificador, es necesario tener un parámetro de tipo integer. (La razón es que nuestra clave principal es un entero). Después, podemos proporcionar el nombre que queremos usar para la variable, que se pasará como parámetro a la función de vista. La sintaxis para identificar este parámetro sería `<int:pk>`. Observe la declaración de tipos, los dos puntos y el nombre de la variable.

La ruta de acceso completa podría tener el aspecto siguiente:

````python
path('shelter/<int:pk>', views.shelter_detail, name='shelter_detail')
````

La función de vista asociada tendría la siguiente firma:

````python
def shelter_detail(request, pk):
    # code
````

## Ejercicio: Creación de vistas

Queremos actualizar la aplicación para proporcionar una lista de los refugios para perros. También queremos que la aplicación permita a alguien hacer clic o pulsar en un refugio individual para ver los detalles. Lo haremos creando dos vistas y registrando las rutas de acceso adecuadas.

### Creación de vistas

1. Abra *dog_shelters/views.py*.
2. Al final de la línea que con el texto `from django.shortcuts import render`, agregue `, get_object_or_404`. Ahora, la línea debería decir lo siguiente:
    ````python
    from django.shortcuts import render, get_object_or_404
    `````
3. Debajo de la línea con el texto `from django.shortcuts import render`, agregue el siguiente código de Python para importar los modelos:
    ````python
    from . import models
    ````
4. Al final de *views.py*, agregue el código siguiente para cargar todos los refugios, crear el objeto de contexto para la plantilla y, después, representar la plantilla para el usuario. Esto se convertirá en nuestra vista de lista para los refugios y la página predeterminada de nuestro sitio web.
    ````python
    def shelter_list(request):
        shelters = models.Shelter.objects.all()
        context = {'shelters': shelters}
        return render(request, 'shelter_list.html', context)
    ````
5. Al final de *views.py*, agregue el código siguiente para cargar un refugio concreto por su pk o su clave principal, crear el objeto de contexto para la plantilla y, después, representar la plantilla para el usuario. Se convertirá en la página de detalles de un refugio.
    ````python
    def shelter_detail(request, pk):
        shelter = get_object_or_404(models.Shelter, pk=pk)
        context = {'shelter': shelter}
        return render(request, 'shelter_detail.html', context)
    ````

### Creación del URLconf

1. Cree un nuevo archivo en *dog_shelters* denominado *urls.py*.
2. Agregue el código siguiente para registrar las rutas de acceso de las dos vistas que hemos creado:
    ````python
    from django.urls import path
    from . import views

    urlpatterns = [
        path('', views.shelter_list, name='shelter_list'),
        path('shelter/<int:pk>', views.shelter_detail, name='shelter_detail'),
        # More patterns to come later
    ]

### Registro de URLconf con nuestro proyecto

1. Abra *project/urls.py*.
2. Hacia la parte inferior del archivo, busque la línea 17, que dice:
    ````python
    from django.urls import path
    ````
    Al final de la línea, agregue `, include`. Ahora, la nueva línea 17 debe ser la siguiente:
    ````python
    from django.urls import path, include
    ````
3. Debajo de la línea en la que pone `TODO: Register URLconf`, agregue lo siguiente:
    ````python
    # TODO: Register URLconf
    path('', include('dog_shelters.urls')),
    ````

## Introducción a las plantillas de Django

Las plantillas son archivos de texto que se pueden usar para generar formatos basados en texto, como HTML o XML. Cada plantilla contiene algunos datos estáticos que se comparten en todo el sitio, pero también puede contener marcadores de posición para los datos dinámicos. Las plantillas contienen variables y etiquetas que controlan el comportamiento y lo que aparecerá como la página final.

### Variables

Las variables de una plantilla se comportan de la misma forma que en cualquier otro lenguaje de programación. Podemos usarlas para indicar un valor que se evalúa en tiempo de ejecución.

Django proporciona una manera de mostrar las variables de una plantilla mediante la sintaxis `{{ }}`. Cualquier variable colocada dentro de las llaves dobles se evalúa para obtener su contenido de texto y, después, se coloca en la plantilla. Si quisiéramos mostrar el nombre del perro, por ejemplo, podríamos usar `{{dog.name}}`.

### Filtros

Los filtros son una excelente manera de controlar cómo aparecen los datos cuando se solicitan en una plantilla. Dado que los filtros ya se han creado, son una manera fácil de dar formato a los datos sin tener que escribir ningún código especial.

Por ejemplo, supongamos que tenemos que imprimir los nombres de las razas de perro y queremos asegurarnos de que la primera letra de cada nombre esté en mayúsculas.

````HTML
{{ dog.name | capfirst }}
````

La variable está a la izquierda del símbolo de barra vertical (`|`) y el filtro está a la derecha. Este es solo uno de los muchos filtros que puede usar para manipular los datos al usar filtros de plantilla de Django.

### Etiquetas

Puede usar etiquetas para realizar bucles, crear texto o proporcionar otros tipos de comandos al motor de plantillas. Las etiquetas suelen ser similares a la sintaxis de Python. Sin embargo, dado que se ejecutan dentro de la plantilla (en lugar de ejecutarse en el intérprete de Python), observará algunas pequeñas diferencias en la sintaxis. Puesto que no podemos basarnos en pestañas como lo haríamos con Python, cada instrucción de bloque requerirá un elemento `end` correspondiente.

Podemos usar instrucciones `if` para la lógica booleana y bucles `for` para la iteración. La sintaxis básica de las instrucciones `if` es similar a la siguiente:

````HTML
{% if dogs %}
    <h2>There are {{ dogs | length }} ready for adoption!</h2>
{% else %}
    <h2>We have no dogs ready for adoption. Please check back later!</h2>
{% endif %}
````

Del mismo modo, podemos usar un bucle `for` para mostrar los nombres de todos los perros en una lista:

````HTML
<ul>
    {% for dog in dogs %}
        <li>{{ dog.name }}</li>
    {% endfor %}
<ul>
````

### Herencia de plantillas

Las plantillas se usan para generar el código HTML que quiere que el usuario vea al usar la aplicación. Normalmente, las páginas de una aplicación comparten una estructura común, por ejemplo con la navegación a la izquierda, un título en la parte superior y una hoja de estilos coherente. Las plantillas de Django admiten estructuras compartidas mediante la herencia.

#### Creación de una página primaria

Crear una página primaria es igual que crear cualquier plantilla HTML de Django. Debe proporcionar la estructura externa y, después, incluir marcadores de posición `{% block %}`. Estos marcadores de posición permiten a los elementos secundarios proporcionar el contenido que se va a colocar en esos marcadores de posición.

Vamos a crear una página primaria para importar una hoja de estilos, proporcionar un título predeterminado y proporcionar un encabezado que queremos mostrar en todas las páginas:

````HTML
<html>
<head>
    <link rel="stylesheet" href="site.css">
    <title>{% block title %}Shelter site{% endblock %}</title>
</head>
<body>
    <h1>Shelter site</h1>
    {% block content %}
    {% endblock %}
</body>
</html>
````

#### Creación de una página secundaria

Podemos crear una página secundaria desde el elemento primario mediante la palabra clave `extends`. Con esta palabra clave, proporcionamos el nombre del archivo HTML de la plantilla primaria. Después, usamos las instrucciones `{% block %}` adecuadas para agregar el contenido específico de esa página.

````HTML
{% extends "parent.html" %}

{% block title %}
Welcome to the Shelter site!
{% endblock %}

{% block content %}
Thank you for visiting our site!
{% endblock %}
````

Cuando se muestra la página, tiene el aspecto siguiente:

````HTML
<html>
<head>
    <link rel="stylesheet" href="site.css">
    <title>Welcome to the shelter site</title>
</head>
<body>
    <h1>Shelter site</h1>
    Thank you for visiting our site!
</body>
</html>
````

## Ejercicio: Creación de plantillas

Vamos a crear dos plantillas para mostrar una lista de los refugios y una página de detalles para cada refugio. También crearemos una plantilla base para garantizar la coherencia en toda la aplicación.

### Creación de la plantilla base

1. Cree una nueva carpeta dentro de *dog_shelters* denominada *templates*.
2. Cree un nuevo archivo dentro de templates con el nombre *base.html*.
3. Agregue el código HTML siguiente a base.html.
    ````HTML
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>{% block title %}Dog shelter site{% endblock %}</title>
        <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.0/dist/css/bootstrap.min.css" integrity="sha384-B0vP5xmATw1+K9KRQjQERJvTumQW0nPEzvF6L/Z6nronJ3oUOFUFpCjEUQouq2+l" crossorigin="anonymous">
    </head>
    <body>
        <article class="container">
            <section class="jumbotron">
                <h3>Dog shelter application</h3>
            </section>
            {% block content %}
            {% endblock %}    
        </article>
    </body>
    </html>
    ````

### Creación de la plantilla de lista de refugios

1. En *dog_shelters/templates*, cree un nuevo archivo denominado *shelter_list.html*.
2. Agregue el código siguiente para crear la plantilla para nuestra lista de refugios:
    ````HTML
    {% extends 'base.html' %}

    {% block title %}
    Shelter list
    {% endblock %}

    {% block content %}
    <h2>Shelter list</h2>
    <div>Here is the list of registered shelters</div>
        {% for shelter in shelters %}
            <div>
                <a href="{% url 'shelter_detail' shelter.id %}">
                    {{shelter.name}}
                </a>
            </div>
        {% endfor %}
    </div>
    {% endblock %}
    ````

    La etiqueta `url` genera dinámicamente una dirección URL. Nuestras direcciones URL se registran en nuestro URLconf, por lo que pueden cambiar. Mediante la etiqueta `url`, podemos indicar a Django que recupere la dirección URL adecuada de URLconf en lugar de codificarla de forma rígida en una ruta de acceso.

    La etiqueta `url` busca el nombre de la ruta de acceso, `shelter_detail` en nuestro caso, y, después, la lista de los parámetros esperados. `shelter_detail` tiene un parámetro, `pk`. Esta es la clave principal, o identificador, del refugio. Especificamos el identificador mediante `shelter.id`.

### Creación de la plantilla de detalles del refugio

1. En *dog_shelters/templates*, cree un nuevo archivo denominado *shelter_detail.html*.
2. Agregue el siguiente código para crear la plantilla:
    ````HTML
    {% extends 'base.html' %}

    {% block title %}
    Shelter list
    {% endblock %}

    {% block content %}
    <h2>{{ shelter.name }}</h2>
    <div>Located in {{ shelter.location }}</div>
    {% if shelter.dog_set.all %}
        <div>Here is the list of available dogs</div>
        {% for dog in shelter.dog_set.all %}
            <div>
                <a href="">
                    {{dog.name}}
                </a>
            </div>
        {% endfor %}
        </div>
    {% else %}
        <div>This shelter has no dogs available for adoption</div>
    {% endif %}
    {% endblock %}
    ````