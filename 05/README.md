# Uso de vistas genéricas en Django

Dado que Django se ha diseñado para aplicaciones controladas por datos, incluye numerosas utilidades integradas para simplificar la cantidad de código necesario. Un área clave donde Django facilita la vida del desarrollador es proporcionando vistas genéricas, que están generadas previamente con todo el código necesario para realizar operaciones básicas, como mostrar y editar datos.

## Introducción

Las aplicaciones controladas por datos a menudo comparten una estructura parecida. Tiene una página en la que se muestra una lista de los elementos, una página para mostrar los detalles de un elemento y, después, las páginas adecuadas para permitir la creación, las actualizaciones y las eliminaciones. Tener que crear cada una de estas páginas a mano puede resultar tedioso, especialmente porque gran parte del código y HTML es repetitivo.

Para ayudar a simplificar la creación de aplicaciones controladas por datos, Django proporciona vistas genéricas. Las vistas genéricas son vistas basadas en clases diseñadas para realizar todas estas operaciones comunes. Podemos usar vistas genéricas para crear rápidamente páginas con el fin de mostrar y editar datos. Estas vistas pueden incluso generar los formularios HTML de forma automática.

## Ejercicio: Configuración del proyecto de inicio

En este módulo, trabajaremos en un sitio web de refugios para perros. Este proyecto se centra en recopilar información sobre los refugios para perros existentes y los perros que esperan ser adoptados en Estados Unidos. La esperanza ficticia de esta aplicación es que los perros podrían encontrar hogares adecuados con más rapidez, ya que habría personas de todo Estados Unidos que querrían adoptarlos, y no solo del área local del refugio.

### Clonación del repositorio de inicio

````bash
git clone https://github.com/MicrosoftDocs/ms-learn-django-generic-views.git
cd ms-learn-django-generic-views/starter
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

## Uso de vistas genéricas para mostrar datos

El sistema de vistas genéricas de Django simplifica la creación de código repetitivo. Las operaciones comunes que realiza en una aplicación controlada por datos comparten el mismo patrón. Por ejemplo, para mostrar un elemento individual por su id o clave principal, el flujo de trabajo siempre es el siguiente:

1. Cargue el elemento de la base de datos por id.
2. Si no se encuentra el elemento, devuelve un mensaje de error 404.
3. Si se encuentra el elemento, páselo a una plantilla para mostrarlo.

El sistema de vistas genéricas confirma este hecho y proporciona clases que se pueden usar y que contienen el código principal ya escrito. Herede de la clase adecuada, establezca un par de propiedades y, después, registre una ruta adecuada en el elemento URLconf. El resto se hace de forma automática.

Django incluye dos vistas genéricas para mostrar datos: `DetailView` y `ListView`.

### DetailView para obtener el detalle del elemento

La vista genérica `DetailView` se usa para mostrar una página de detalles de un elemento. `DetailView` recupera el elemento del elemento `model` especificado por la clave principal y lo pasa a la plantilla. Puede establecer `template_name` en el nombre de la plantilla que se va a usar. De manera predeterminada, es `<model>_detail.html`. Por último, podemos establecer `context_object_name` en el nombre de la variable que se quiere usar en nuestra plantilla.

Para crear una vista de detalles de un perro mediante la vista genérica, podría usar el código siguiente:

```python
from . import models
from django.views import generic

class DogDetailView(generic.DetailView):
    model = models.Dog
    template_name = 'dog_detail.html'
    context_object_name = 'dog'
```

El registro de `DogDetailView` es similar a cualquier otra entrada de path. El elemento clave para asegurarse de que se incluye es un parámetro denominado `pk`. Django usa esta convención para identificar la clave principal. También deberá tener en cuenta que usamos el método `as_view()` para convertir la clase en una vista.

```python
path('dog/<int:pk>', views.DogDetailView.as_view(), name='dog_detail')
```

### ListView para obtener una lista de elementos

La vista genérica `ListView` se comporta de manera similar a `DetailView`. Puede establecer `context_object_name` para el nombre de la variable en la vista y `template_name` para el nombre de la plantilla.

La principal diferencia es que `ListView` está diseñada para trabajar con cualquier forma de una consulta que devuelve varios elementos. Como resultado, debe invalidar la función `get_queryset`. El sistema de vista genérico llama a la función `get_queryset` para recuperar los elementos de la base de datos, lo que permite ordenar o filtrar los elementos según sea necesario.

Para crear una vista que muestre la lista de todos los refugios mediante la vista genérica `ListView`, podría usar el código siguiente:

```python
from . import models
from django.views import generic

class ShelterListView(generic.ListView):
    template_name = 'shelter_list.html'
    context_object_name = 'shelters'

    def get_queryset(self):
        return models.Shelter.objects.all()
```

El registro de la vista se realiza de forma muy similar a nuestro elemento `DetailView`.

```python
path('', ShelterListView.as_view(), name='shelter_list')
```

## Ejercicio: Implementación de vistas genéricas para mostrar datos

Queremos crear una nueva página de detalles para perros. Vamos a usar la vista genérica `DetailView` para simplificar la cantidad de código que necesitamos crear.

### Creación de la vista DogDetail

1. Abra **dog_shelters/views.py**.
2. Debajo de la línea que lee `# TODO: Import generic views`, agregue el código siguiente para importar el módulo de vistas genéricas.
    ```python
    # TODO: Import generic views
    from django.views import generic
    ```
3. Agregue el código siguiente a la parte inferior de **views.py** a fin de crear la vista genérica para `DogDetail` y establecer el modelo, la plantilla y el objeto de contexto.
    ```python
    class DogDetailView(generic.DetailView):
        model = models.Dog
        template_name = 'dog_detail.html'
        context_object_name = 'dog'
    ```

### Registro de la vista de detalles

1. Abra **dog_shelters/urls.py**.
2. Debajo de la línea que lee `# TODO: Register detail view`, agregue el código siguiente para registrar la ruta de nuestro elemento `DogDetailView`.
    ```python
    # TODO: Register detail view
    path('dog/<int:pk>', views.DogDetailView.as_view(), name='dog_detail'),
    ```

### Creación de la plantilla HTML

1. Cree un archivo dentro de **dog_shelters/templates** denominado **dog_detail.html**.
2. Agregue el código siguiente a **dog_detail.html** para crear la plantilla para mostrar los detalles del perro.
    ```HTML
    {% extends 'base.html' %}

    {% block title %}
    {{ dog.name }}
    {% endblock %}

    {% block content %}
    <h2>{{ dog.name }}</h2>
    <div>About {{ dog.name }} - {{ dog.description }}</div>
    {% endblock %}
    ```

### Actualización de la página de detalles del refugio para incluir nuestro vínculo

1. Abra **dog_shelters/templates/shelter_detail.html**.
2. Debajo de la línea que lee `{# TODO: Add link to dogs #}`, agregue el código siguiente para crear un vínculo de cada perro a la vista de detalles.
    ```HTML
    {# TODO: Add link to dogs #}
    <a href="{% url 'dog_detail' dog.id %}">
        {{dog.name}}
    </a>
    ```

### Prueba de la página

1. Guarde todos los archivos.
2. En el explorador, vaya a http://localhost:8000.
3. En la lista de refugios, seleccione **Contoso**.
3. En la lista de perros, seleccione **Roscoe**.

## Uso de vistas genéricas para editar datos

Al igual que ocurre con el código necesario para mostrar los datos, el código para permitir que los usuarios modifiquen los datos es repetitivo. También puede resultar tedioso, ya que se requieren varios pasos para asegurarse de que los datos son válidos y se envían correctamente. Afortunadamente, el sistema de vistas genéricas puede simplificar la cantidad de código que necesitamos para habilitar esta función.

### Flujo de trabajo de creación

A primera vista, el código para permitir que un usuario cree un elemento podría parecer trivial. Pero, por lo visto, se trata de un proceso aparentemente complicado.

1. El usuario envía una solicitud **GET** para indicar que quiere que el formulario cree un elemento.
2. El servidor envía el formulario con un token especial para evitar la falsificación de solicitud entre sitios (CSRF).
3. El usuario completa el formulario y selecciona Enviar, que envía una solicitud **POST** para indicar que el formulario se ha completado.
4. El servidor valida el token CSRF para asegurarse de que no se ha producido ninguna alteración.
5. El servidor valida toda la información para asegurarse de que cumple las reglas. En caso de que se produzca un error de validación, se devolverá un mensaje de error.
6. El servidor intenta guardar el elemento en la base de datos. Si se produce un error, se devuelve al usuario un mensaje de error.
7. Después de guardar correctamente el elemento nuevo, el servidor redirige al usuario a una página de éxito.

¡Este proceso requiere bastante código! La mayor parte de este es reutilizable, lo que significa que es el mismo cada vez que se crea.

#### Formularios

La creación de un formulario HTML puede ser un proceso tedioso. Los desarrolladores a menudo copian y pegan etiquetas `input`, recorren listas para crear listas desplegables y configuran botones de radio. Cada vez que el modelo cambia, debe actualizarse el formulario.

Es posible que haya observado que los modelos que creamos en Django contienen todo lo necesario para crear el formulario. Cuando agregamos los distintos campos, indicamos los tipos de datos, que se acoplan con distintos elementos HTML. Por ejemplo, un campo booleano sería una casilla y una clave externa normalmente sería una lista desplegable.

### Vistas genéricas para modificar datos

Uno de los objetivos clave de Django es eliminar la necesidad de volver a crear constantemente los mismos bloques de código una y otra vez. Para que este objetivo sea compatible con las modificaciones de datos, Django proporciona una recopilación de clases y formularios genéricos con el fin de administrar esta carga de trabajo. Como veremos, incluye todo el código necesario y puede incluso crear el formulario por nosotros dinámicamente. Las clases utilizadas para crear, actualizar y eliminar datos se denominan `CreateView`, `UpdateView` y `DeleteView` respectivamente.

#### CreateView

La clase `CreateView` se utiliza para permitir a los usuarios crear elementos. Le guía por el proceso anterior y crea dinámicamente el formulario. Una vez que se ha realizado correctamente, muestra la página de detalles del elemento recientemente creado.

Se debe especificar el elemento `model` y `template_name` que quiere asociar, tal como se haría con las otras vistas genéricas. La diferencia clave de `CreateView` es la inclusión de una propiedad `fields` en la que se muestra una lista de los campos modificables. Con esta propiedad, puede asegurarse de que los campos que no se deben editar, como una fecha de creación, no aparecen en el formulario. La vista para crear un perro podría ser similar a la del ejemplo siguiente:

```python
from . import models
from django.views import generic

class DogCreateView(generic.CreateView):
    model = models.Dog
    template_name = 'dog_form.html'
    fields = ['name', 'description', 'shelter']
```

#### UpdateView

La clase `UpdateView` se comporta de manera idéntica a `CreateView`. La única diferencia es que carga automáticamente un elemento basado en el parámetro `pk`. Django usa esta convención para la clave principal de un elemento.

```python
from . import models
from django.views import generic

class DogUpdateView(generic.CreateView):
    model = models.Dog
    template_name = 'dog_form.html'
    fields = ['name', 'description', 'shelter']
```

Después de crear o actualizar correctamente un elemento, Django redirige a la página de detalles del elemento. Recupera la dirección URL de los detalles mediante el uso de `get_absolute_url` en el modelo asociado. Para implementar este método, devuelva la dirección URL correcta. Puede recuperar la dirección URL apropiada de URLconf mediante `reverse`. Tenga en cuenta que `kwargs` se usa para pasar `pk` o el parámetro de clave principal o a la ruta.

```python
from django.db import models
# TODO: Import reverse
from django.urls import reverse
class Dog(models.Model):
    # Existing code
    def get_absolute_url(self):
        return reverse('dog_detail', kwargs={"pk": self.pk})
```

#### DeleteView

La clase `DeleteView` es parecida a `UpdateView`. Permite a un usuario eliminar un elemento e identifica el elemento que se va a eliminar mediante `pk`. A diferencia de `UpdateView`, `fields` no se necesita porque se eliminará todo el elemento. Además, dado que no se ha creado ni actualizado ningún elemento recientemente, necesitamos determinar dónde queremos redirigir al usuario. Podemos crear un redireccionamiento estableciendo `success_url` en el valor adecuado. Puede buscar una dirección URL mediante `reverse_lazy`.

```python
from . import models
from django.views import generic
from django.urls import reverse_lazy

class AuthorDelete(DeleteView):
    model = Author
    success_url = reverse_lazy('author-list')
```

### Plantillas de formulario para crear y actualizar

Las vistas genéricas pueden crear el formulario HTML por nosotros dinámicamente. Todo lo que debemos proporcionar es una plantilla que actúe como el marcador de posición del formulario. La plantilla de marcador de posición garantiza que el formulario coincida con el resto de nuestro sitio. Afortunadamente, no necesitamos mucho código para crearla.

Las vistas genéricas crean automáticamente una variable `form` para que la use nuestra plantilla. Los elementos del formulario que proporciona Django se pueden mostrar dentro de las etiquetas `<p>` o como un elemento `<table>`.

La variable `form` contiene todo el HTML adecuado para crear los controles del formulario. No contiene la propia etiqueta `<form>` o un botón de envío. Nuestra plantilla debe incluir cuatro elementos:

- El elemento `form` con `method` establecido en **POST**, porque esta configuración desencadena la operación de almacenamiento en el servidor.
- El código `{% csrf_token %}` para agregar el token CSRF a fin de evitar la suplantación de identidad.
- El código `{{ form.as_p }}` o `{{ form.as_table }}` para mostrar el formulario generado de forma dinámica.
- El botón `submit`.

El código siguiente puede actuar como el host para cualquier formulario de vista genérica.

```HTML
<form method="post">{% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Save</button>
</form>
```

## Ejercicio: Implementación de vistas genéricas para editar datos

Para permitir que los usuarios registren nuevos perros en un refugio, usaremos `CreateView`.

### Actualización del modelo para admitir get_absolute_url

1. Abra **dog_shelters/models.py**.
2. Agregue el código siguiente debajo de la línea que lee `# TODO: Import reverse` para importar la función `reverse`.
    ```python
    # TODO: Import reverse
    from django.urls import reverse
    ```
3. Agregue el código siguiente a la clase `Dog` inmediatamente en la línea que lee `# TODO: Add get_absolute_url` para leer la ruta **dog_detail** de URLconf y pasar el identificador como parámetro.
    ```python
    # TODO: Add get_absolute_url
        def get_absolute_url(self):
            return reverse('dog_detail', kwargs={"pk": self.pk})
    ```

### Creación de DogCreateView

1. Abra **dog_shelters/views.py**.
2. Al final de **views.py**, agregue el código siguiente para crear `DogCreateView`.
    ```python
    class DogCreateView(generic.CreateView):
        model = models.Dog
        template_name = 'dog_form.html'
        fields = ['shelter', 'name', 'description']
    ```

### Registro de la ruta

1. Abra **dog_shelters/urls.py**.
2. Debajo de la línea que lee `# TODO: Register create view`, agregue el código siguiente para registrar la ruta.
    ```python
    # TODO: Register create view
    path('dog/register', views.DogCreateView.as_view(), name='dog_register'),
    ```

### Creación de la plantilla HTML

1. Cree un archivo dentro de **dog_shelters/templates** denominado **dog_form.html**.
2. Agregue el código siguiente a **dog_form.html** para crear la plantilla que hospedará el formulario.
    ```HTML
    {% extends 'base.html' %}

    {# TODO: Register crispy_forms_tags #}

    {% block title %}
    Register dog at shelter
    {% endblock %}

    {% block content %}
    <h2>Register dog at shelter</h2>
    <form method="POST">
        {% csrf_token %}

        {{ form.as_p }}

        <button type="submit" class="btn btn-primary">Save</button>
    </form>
    {% endblock %}
    ```

### Creación de un vínculo a la página de registro

1. Abra **dog_shelters/templates/shelter_list.html**.
2. Debajo de la línea que lee `{# TODO: Add link to registration page #}`, agregue el código siguiente para crear el vínculo.
    ```HTML
    {# TODO: Add link to registration page #}
    <div>
        <a href="{% url 'dog_register' %}">Register a dog available for adoption</a>
    </div>
    ```

### Prueba de la página

1. Guarde todos los archivos.
2. En el explorador, vaya a http://localhost:8000.
3. Seleccione **Register a dog available for adoption**.
4. Elija un refugio y agregue un nombre y una descripción para un perro.
5. Seleccione Guardar.

## Ejercicio: Implementación de django-crispy-forms

Después de ver el formulario creado, es posible que observe que el formato no es el mismo que en el resto de la página. Estamos usando Bootstrap, pero el formulario todavía no. Afortunadamente, existe una biblioteca que puede garantizar que nuestros formularios también usan Bootstrap.

### La biblioteca django-crispy-forms

La biblioteca [django-crispy-forms](https://django-crispy-forms.readthedocs.io/en/latest/) mejora el modo en que Django genera los formularios.

### Instalación de la biblioteca

1. Abra **requirements.txt**.
2. En la parte inferior del archivo, agregue una nueva línea que lea lo siguiente:
    ```text
    django-crispy-forms
    crispy-bootstrap4
    ```
3. Instale todos los paquetes mediante la ejecución del comando siguiente:
```bash
pip install -r requirements.txt
```

### Registro de la aplicación en Django y configuración de la plantilla

1. Abra **project/settings.py**.
2. Debajo de la línea que lee `# TODO: Register crispy_forms`, agregue el código siguiente para registrar django-crispy-forms.
    ```python
    # TODO: Register crispy_forms
    'crispy_forms',
    'crispy_bootstrap4',
    ```
3. Debajo de la línea que lee `# TODO: Set template_pack`, agregue el código siguiente para configurar django-crispy-forms a fin de que use Bootstrap 4.

```python
# TODO: Set template_pack
CRISPY_TEMPLATE_PACK = 'bootstrap4'
CRISPY_ALLOWED_TEMPLATE_PACKS = 'bootstrap4'
```

### Actualización de la plantilla para usar django-crispy-forms

1. Abra **dog_shelters/templates/dog_form.html**.
2. Debajo de la línea que lee `{# TODO: Load crispy_forms_tags #}`, agregue el código siguiente para cargar el filtro o la etiqueta.
    ```HTML
    {# TODO: Load crispy_forms_tags #}
    {% load crispy_forms_tags %}
    ```
3. Reemplace la línea que lee `{{ form.as_p }}` por el código siguiente para usar el filtro `crispy`.
    ```HTML
    {{ form | crispy }}
    ```

### Prueba del sitio

1. Guarde todos los archivos.
2. En el explorador, vaya a http://localhost:8000/dog/register.