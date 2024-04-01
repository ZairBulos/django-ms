# Trabajo con modelos y datos en Django

Django se centra en las aplicaciones controladas por datos, por lo que proporciona su propio asignador relacional de objetos (ORM). Exploraremos los conceptos de ORM y cómo funciona el ORM de Django. Crearemos nuestros propios modelos y configuraremos la base de datos.

## Introducción

Aunque es posible crear aplicaciones que interactúen directamente con una base de datos relacional, esta interacción directa puede conducir a código duplicado y poco seguro. Para solventar este problema, se introdujeron los asignadores relacionales de objetos (ORM), que separan las llamadas de base de datos de los objetos.

Como desarrollador, puede usar los ORM para diseñar objetos que representen los datos. Los ORM también pueden administrar las operaciones de la base de datos.

Django tiene un ORM integrado, que es un componente básico del marco.

## Ejercicio: Configuración del proyecto e instalación de Django

Trabajaremos en un sitio web de refugios para perros. Este proyecto se centra en recopilar información sobre todos los refugios de perros existentes y los perros que esperan ubicar en Estados Unidos. La esperanza ficticia de esta aplicación es que los perros podrán encontrar hogares adecuados con más rapidez, ya que habrá personas de todo Estados Unidos que quieran adoptarlos, y no solo de su área local.

Django es un buen marco para este proyecto porque proporciona una ruta para desarrollar rápidamente una aplicación orientada al cliente. También proporciona una función de administración y base de datos establecida a la que pueden acceder fácilmente los empleados para obtener una actualización rápida

### Clonación del repositorio de inicio

````bash
git clone https://github.com/MicrosoftDocs/mslearn-django-models-data
cd mslearn-django-models-data/starter
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

## Asignador relacional de objetos de Django

Trabajar con una base de datos relacional requiere una mentalidad diferente de la que requiere el trabajo con objetos en una aplicación. Cambiar entre estos dos entornos puede ralentizar el proceso de creación de una aplicación. Además, para convertir los resultados de las consultas de una base de datos en datos que la aplicación pueda utilizar se requiere un código adicional.

Los asignadores relacionales de objetos, u ORM, resuelven este problema actuando como middleware entre una aplicación y la base de datos. Puede crear objetos que modelan los datos, incluida la adición de restricciones y otras formas de metadatos. Así pues, el ORM:

- Administra la creación y actualización de la base de datos según sea necesario.
- Controla las consultas.
- Convierte (o asigna) las solicitudes que se realizan mediante los objetos en las llamadas de base de datos adecuadas.

### Introducción al ORM de Django

Django se creó para aplicaciones controladas por datos, por lo que es natural que tenga un ORM integrado. El ORM de Django resultará natural para los desarrolladores de Python, ya que usa la herencia y la sintaxis de clase.

Dado que Django está diseñado para ser un marco web, puede usar la estructura de los modelos que crea para generar automáticamente HTML y formularios. En la mayoría de los casos, Django puede crear dinámicamente el código HTML para permitir al usuario editar los datos sin necesidad de crear el formulario manualmente. Incluso puede administrar las llamadas de base de datos.

## Modelos en Django

Los modelos se encuentran en el corazón de cualquier ORM. Un modelo es una representación de los datos con los que trabajará la aplicación.

### Creación de un modelo

En Django, un modelo es cualquier clase que hereda una colección de funcionalidades de `django.models.Model`. La colección incluye métodos que permiten consultar la base de datos, crear nuevas entradas y guardar actualizaciones. También puede definir campos, establecer metadatos y establecer relaciones entre los modelos.

````python
from django.db import models

class Product(models.Model):
    # details would go here
    pass

class Category(models.Model):
    # details would go here
    pass
````

### Agregar métodos

es importante resaltar el hecho de que un modelo es una clase de Python. Como resultado, puede agregar métodos e invalidar los que proporciona `Django.models`.Model o los que son inherentes a todos los objetos de Python.

Un método concreto que cabe resaltar es `__str__`. Este método se usa para mostrar un objeto si no se especifica ningún campo.

````python
class Product(models.Model):
    name = models.TextField()
    
    def __str__(self):
        return self.name
````

### Adición de campos

Los campos definen la estructura de datos de un modelo.

Los diferentes datos tienen diferentes tipos de datos, reglas de validación y otras formas de metadatos. El ORM de Django contiene un amplio conjunto de opciones para configurar los campos de los modelos según sus especificaciones. El ORM es extensible, por lo que puede crear sus propias reglas según sea necesario.

#### Definición del tipo de campo

La parte principal de los metadatos de todos los campos es el tipo de datos que almacenará, como una cadena o un número. Los tipos de campo se asignan a un tipo de base de datos y un tipo de control de formularios HTML (como un cuadro de texto o una casilla). Django usa varios tipos de campo, incluidos los siguientes:

- `CharField`: una línea de texto única.
- `TextField`: varias líneas de texto.
- `BooleanField`: una opción booleana true/false.
- `DateField`: una fecha.
- `TimeField`: una hora.
- `DateTimeField`: fecha y hora.
- `URLField`: una dirección URL.
- `IntegerField`: un número entero.
- `DecimalField`: un número decimal de precisión fija.

````python
from django.db import models

class Product(models.Model):
    name = models.TextField()
    price = models.DecimalField()
    creation_date = models.DateField()

class Category(models.Model):
    name = models.TextField()
````

#### Opciones de campo

Puede usar las opciones de campo para agregar metadatos para permitir valores NULL o en blanco, o para marcar un campo como único. También puede establecer opciones de validación y proporcionar mensajes personalizados para los errores de validación.

Al igual que con los tipos de campo, las opciones de campo se asignan a la configuración adecuada en la base de datos. Las reglas se aplicarán en los formularios que Django genere automáticamente.

Las opciones de campo se pasan a la función del propio campo. Distintos campos pueden admitir distintas opciones. Algunas de las opciones más comunes son las siguientes:

- `null`
    - Opción booleana para permitir valores NULL.
    - El valor predeterminado es False.
- `blank`
    - Opción booleana para permitir valores en blanco.
    - El valor predeterminado es False.
- `default`
    - Permite la configuración de un valor predeterminado si no se proporciona un valor para el campo.
    - Si quiere establecer el valor predeterminado en una base de datos null, establezca default en None.
- `unique`
    - Este campo debe contener un valor único.
    - El valor predeterminado es False.
- `min_length` y `max_length`
    - Se usa con tipos de cadena para identificar la longitud mínima y máxima de la cadena.
    - El valor predeterminado es None.
- `min_value` y `max_value`
    - Se usa con tipos numéricos para identificar los valores mínimo y máximo.
- `auto_now` y `auto_now_add`
    - Se usa con tipos de fecha y hora para indicar si se debe usar la hora actual.
    - `auto_now` siempre establecerá el campo en la hora actual al guardar, lo que resulta útil para los campos last_update.
    - `auto_now_add` establecerá el campo en la hora actual en el momento de la creación, lo que resulta útil para los campos creation_date.

````python
from django.db import models

class Product(models.Model):
    name = models.TextField(max_length=50, min_length=3, unique=True)
    price = models.DecimalField(min_value=0.99, max_value=1000)
    creation_date = models.DateField(auto_now_add=True)

class Category(models.Model):
    name = models.TextField(max_length=50, min_length=3, unique=True)
````

#### Claves y relaciones

Una práctica estándar en las bases de datos relacionales es tener una clave principal para cada fila de una tabla, normalmente un entero incrementado automáticamente. El ORM de Django agregará esta clave automáticamente a todos los modelos que cree mediante la adición de un campo denominado `id`.

Si quiere invalidar este comportamiento, puede establecer el campo que quiere que sea la clave principal. Sin embargo, debe basarse en el campo `id` de Django en la mayoría de las situaciones.

Las bases de datos relacionales también tienen relaciones entre las tablas. El ORM de Django admite todas las relaciones que podría querer crear entre los modelos.

La relación más común es "de uno a varios", técnicamente conocida como relación de clave externa. En una *relación de clave externa*, varios elementos comparten un solo atributo. Para modelar esta relación, se usa el campo `ForeignKey`. 

Para crear la relación, agregue el campo `ForeignKey` al objeto secundario. Django agrega automáticamente una propiedad al elemento primario para proporcionar acceso a todos los elementos secundarios denominados `<child>_set`, donde `<child>` es el nombre del objeto secundario.

`ForeignKey` tiene un parámetro obligatorio, on_delete. Este parámetro indica a Django qué debe hacer si se elimina el elemento primario. Las dos opciones más comunes son:

- CASCADE
- PROTECT

````python
from django.db import models

class Product(models.Model):
    name = models.TextField()
    price = models.DecimalField()
    creation_date = models.DateField()
    category = models.ForeignKey(
        'Category', #The name of the model
        on_delete=models.PROTECT
    )

class Category(models.Model):
    name = models.TextField()
    # product_set will be automatically created
````

## Ejercicio: Creación de modelos

Al crear un modelo, puede definir los campos esenciales y el comportamiento de los datos. Vamos a agregar los modelos necesarios para la aplicación *dog_shelters*.

### Crear modelos

Django proporciona un archivo vacío denominado *models.py* que puede usar para los modelos.

1. Abra el archivo *dog_shelters/models.py*
2. Agregue dos clases de Python para contener los modelos escribiendo el código siguiente en el comentario `Create your models here`:

    ````python
    # Create your models here
    class Shelter(models.Model):
        name = models.CharField(max_length=200)
        location = models.CharField(max_length=200)
        def __str__(self):
            return self.name

    class Dog(models.Model):
        shelter = models.ForeignKey(Shelter, on_delete=models.PROTECT)
        name = models.CharField(max_length=200)
        description = models.TextField()
        intake_date = models.DateTimeField(auto_now_add=True)
        def __str__(self):
            return self.name
    ````

### Registro del modelo

Todas las aplicaciones deben estar registradas con el proyecto en Django. Puede parecer poco intuitivo, pero el simple hecho de que exista una carpeta de aplicación dentro de un proyecto, no significa que esa carpeta se cargue automáticamente. Necesitamos registrarla agregándola a la lista `INSTALLED_APPS`.

1. Vaya al archivo *dog_shelters/apps.py* y compruebe que el nombre de clase es `DogSheltersConfig` en el código siguiente:
    ````python
    class DogSheltersConfig(AppConfig):
        default_auto_field = 'django.db.models.BigAutoField'
        name = 'dog_shelters'
    ````
2. Abra *settings.py* en el proyecto.
3. Agregue la ruta de acceso completa al nombre de clase en el comentario `#[TODO] - Add the app to the list of INSTALLED_APPS`:
    ````python
    #[TODO] - Add the app to the list of INSTALLED_APPS
    'dog_shelters.apps.DogSheltersConfig',
    ````

## Administración de la base de datos

El ORM de Django va más allá de permitirle interactuar con los datos. También puede usarlo para crear y actualizar la base de datos mediante un proceso conocido como migraciones.

### Migraciones

Una migración es una colección de actualizaciones que se realiza en el esquema de una base de datos. Un esquema de base de datos es la definición de la propia base de datos, incluidas todas las tablas y columnas y las relaciones entre esas tablas.

Cuando creamos nuestros modelos y definimos los campos, también definimos las tablas, columnas y relaciones entre esas tablas. Al crear los modelos, hemos creado la definición de esquema.

Las migraciones se usan para crear y actualizar la base de datos a medida que cambian los modelos. Como probablemente sabe, el software cambia constantemente. La forma en que se definen los modelos hoy podría ser diferente de cómo se definen mañana. Las migraciones nos ahorran el proceso de actualizar la base de datos nosotros mismos. Así pues, podemos realizar cambios en los modelos y usar Django para realizar los cambios necesarios en la base de datos.

### Realización de una migración

Para crear una migración, use el comando `makemigrations` de *manage.py*. El comando `makemigrations` usa la lista actual de migraciones para obtener un punto de partida y, después, usa el estado actual de los modelos para determinar la diferencia. Por último, genera el código necesario para actualizar la base de datos.

````bash
python manage.py makemigrations
````

### Visualización del SQL para la migración

Todas las operaciones que se producen dentro de una base de datos relacional requieren SQL. Las migraciones de Django generan el SQL adecuado cuando se ejecutan. Aunque puede usar las herramientas de migración para actualizar la base de datos directamente, algunos entornos pueden tener administradores de bases de datos que administren el proceso automáticamente.

Para compilar las instrucciones SQL adecuadas, puede usar `sqlmigrate`.

````bash
python manage.py sqlmigrate <app_label> <migration_name>
````

### Visualización de la lista de migraciones

Si quiere ver todas las migraciones, puede usar `showmigrations`.

````bash
python manage.py showmigrations
````

### Realización de una migración

El comando `migrate` ejecuta una migración específica o todas las migraciones en la base de datos configurada en *settings.py* en la raíz de la carpeta del proyecto.

Si abre *settings.py*, verá una sección `DATABASES` en la parte inferior. En esta sección se incluye una opción default, que en un nuevo proyecto está configurada para usar SQLite. Puede configurar diferentes cadenas de conexión de base de datos en esta sección según sea necesario.

````bash
python manage.py migrate <app_label> <migration_name>
````

## Ejercicio: Creación del esquema de base de datos

Una vez que hemos creado los modelos, podemos crear la base de datos. Usaremos el SQLite predeterminado y las herramientas disponibles en Django.

### Enumeración de todas las migraciones

````bash
python manage.py showmigrations
````

### Creación de migraciones para dog_shelters

````bash
python manage.py makemigrations dog_shelters
````

### Actualizar la base de datos

````bash
python manage.py migrate
````

## Ejercicio: Trabajar con datos

Al crear los modelos, creamos una API que se puede usar para acceder a los datos de la base de datos. Esta API nos permite crear, recuperar, actualizar y eliminar objetos de la base de datos.

### Configuración del shell interactivo

Django incluye un shell interactivo en el que puede ejecutar código de Python en el entorno de Django.

1. Ejecute el comando siguiente para iniciar el shell:
    ````bash
    python manage.py shell
    ````
2. Importe los modelos desde models dentro de dog_shelters:
    ````bash
    from dog_shelters.models import Shelter, Dog
    ````

### Creación y modificación de objetos

Dado que los modelos son clases de Python, las nuevas instancias se crean con la misma sintaxis que usaríamos para crear un objeto. Dado que heredan de `Django.models.Model`, heredan la funcionalidad del ORM de Django. Esa funcionalidad incluye `save`, que se usa para guardar el objeto en la base de datos.

1. Cree un nuevo refugio ejecutando el comando de Python siguiente en el shell:
    ````bash
    shelter = Shelter(name="Demo shelter", location="Seattle, WA")
    shelter.save()
    ````
2. Actualice la ubicación del refugio a Redmond, WA; para ello, establezca el campo location llamando a save:
    ````bash
    shelter.location = "Redmond, WA"
    shelter.save()
    ````
3. Cree dos nuevos perros para el refugio ejecutando los comandos de Python siguientes en el Shell:
    ````bash
    Dog(name="Sammy", description="Cute black and white dog", shelter=shelter).save()
    Dog(name="Roscoe", description="Lab mix", shelter=shelter).save()
    ````

### Recuperación de objetos

Para recuperar objetos de una base de datos, Django proporciona una propiedad `objects` en todas las clases `Model`. La propiedad `objects` proporciona varias funciones, como `all`, `filter` y `get`.

1. Recupere todos los perros del Refugio de demostración ejecutando el comando siguiente:
    ````bash
    shelter.dog_set.all()
    ````
2. Recupere el segundo perro usando `get` tal como se muestra en el comando siguiente:
    ````bash
    Dog.objects.get(pk=1)
    ````
3. Recupere todos los perros del refugio de demostración mediante `filter` tal como se muestra en el comando siguiente:
    ````bash
    Dog.objects.filter(shelter__name='Demo shelter')
    ````

### Cierre del shell

Cuando haya terminado de experimentar con los objetos, puede cerrar el shell mediante el comando `exit()`.