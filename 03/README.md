# Trabajo con el sitio de administración de Django

Probablemente la característica más popular de Django para marco de Python sea su sitio de administración integrado, que permite a los usuarios internos administrar datos sin necesidad de crear una utilidad especial.

## Introducción

Cuando cree aplicaciones, es probable que le interese crear permisos individuales para acceder al sitio. La creación de una interfaz de administración para empleados o clientes para manipular el contenido puede ser una tarea complicada, pero Django acude al rescate con una simple característica de administración que se crea automáticamente en los proyectos. Para proseguir nuestro recorrido por la plataforma de Django, activaremos el sitio de administración, agregaremos permisos de usuario y exploraremos otra vista de la base de datos.

## Ejercicio: Obtención del proyecto de inicio

En este módulo, trabajaremos en un sitio web de refugios para perros. Este proyecto se centra en recopilar información sobre todos los refugios de perros existentes y los perros que esperan ubicar en Estados Unidos. La esperanza ficticia de esta aplicación es que los perros encuentren hogares adecuados con más rapidez, ya que es posible que haya personas de todo el país que quieran adoptarlos, y no solo de su área local.

Django es el marco perfecto para este proyecto. Proporciona una ruta para desarrollar rápidamente una aplicación orientada al cliente. Django también proporciona una función de administración y base de datos establecida a la que pueden acceder fácilmente los empleados para obtener una actualización rápida.

### Clonación del repositorio de inicio

````bash
git clone https://github.com/MicrosoftDocs/mslearn-django-admin-site
cd mslearn-django-admin-site/starter
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

### Creación de la base de datos

````bash
python manage.py migrate
````

### Inicio del servidor

````bash
python manage.py runserver
````

## Permisos para el sitio de administración

Django incluye un sitio de administración integrado que se puede usar para administrar los datos de la aplicación y la seguridad. Este sitio forma parte de la instalación predeterminada. Django también incluye una implementación completa de autenticación y autorización, que se puede usar para controlar el acceso al sitio de administración.

### Django y la seguridad

Django está diseñado para simplificar la creación de aplicaciones web controladas por datos, ya que proporciona la funcionalidad normal que requieren estos tipos de sitios. Django proporciona un mecanismo de autenticación y autorización, aunque el sistema incluido se puede expandir y modificar libremente. Puede incorporar autenticadores de terceros, autenticación multifactor o cualquier otro requisito de su organización.

### Exploración de los tipos de usuario

De forma predeterminada, Django cuenta con tres tipos principales de usuarios: usuarios, personal y superusuarios. Puede crear sus propios tipos mediante la creación de grupos o la configuración de permisos únicos.

#### Niveles de acceso

| Access	              | Usuario | Personal | Superusuario |
| ----------------------- | ------- | -------- | ------------ |
| Sitio de administración |	No      | Sí       | Sí           |
| Administración de datos |	No      | No	   | Sí           |
| Administrar usuarios	  | No      | No	   | Sí           |

#### Creación de usuarios

Para crear usuarios en Django, primero debe crear un superusuario. Use el comando `createsuperuser` de manage.py para crear un superusuario. Después de crear un superusuario, puede acceder al sitio de administración para crear los demás usuarios.

### El sitio de administración

La creación de una aplicación controlada por datos conlleva, por definición, trabajar con datos. Para permitir a usuarios profesionales internos acceso para modificar los datos según lo consideren oportuno, a menudo se requiere código entre la seguridad y la interfaz. Django proporciona un sitio de administración que controla este proceso automáticamente.

A través del sitio de administración, puede determinar qué usuarios tienen acceso a qué datos. Los usuarios pueden emplear el sitio de administración para agregar, actualizar y eliminar elementos. No necesitan acceder a la base de datos directamente ni omitir ninguna de las reglas de validación que haya implementado. El modelo de seguridad y la interfaz ya están creados como parte del marco. Lo único que debe hacer es activar los modelos para que aparezcan en el sitio, lo que tan solo requiere un par de líneas de código.

## Ejercicio: Creación de usuarios

Cuando cree proyectos, la interfaz de administración se creará automáticamente, pero no configurará ningún acceso de usuario. Para iniciar sesión en el sitio de administración de Django, ahora necesitamos crear el primer usuario, que es un superusuario.

### Creación de un superusuario

1. Ejecute el comando siguiente para crear un superusuario:
    ````bash
    python manage.py createsuperuser
    ````
2. Responda a las preguntas del asistente para completar el proceso. Escriba el nombre de usuario que quiera usar, una dirección de correo electrónico y una contraseña.

### Inicio de sesión en el sitio de administración

Una vez que haya creado el usuario administrador, es el momento de iniciar sesión por primera vez en la interfaz de administración de Django.

1. Vaya a http://127.0.0.1:8000/admin
2. Escriba el nombre de usuario y la contraseña que creó anteriormente.
3. Seleccione **Users**.

### Creación de un usuario de personal

1. En la esquina superior derecha, seleccione **ADD USER**.
2. Escriba un nombre de usuario para staffuser.
3. Escriba una contraseña que cumpla los requisitos de complejidad y confírmela.
4. En la pantalla siguiente, seleccione Staff status para que el nuevo usuario sea de personal.

## Ejercicio: Administración de datos

Como ya se indicó antes, el sitio de administración no proporciona acceso a los datos de forma predeterminada. Afortunadamente, solo necesita un par de líneas de código para registrar los modelos que quiera que se puedan editar a través de la herramienta.

### Registro de modelos

1. Abra **dog_shelters/admin.py**.
2. Debajo del comentario `# Register your models here.`, agregue el código siguiente para registrar los modelos.
    ````python
    # Register your models here.
    from .models import Shelter, Dog

    admin.site.register(Shelter)
    admin.site.register(Dog)
    ````
3. Guarde el archivo.
4. Vuelva al explorador y actualice la página.

### Acceso a los datos

Ahora que hemos registrado los modelos, podemos administrar los datos. Si ya había datos en la base de datos, podemos modificarlos, en caso de que sea necesario.

Vamos a crear un elemento Dog para explorar cómo funciona el sitio de administración en lo que respecta a los datos.

1. Seleccione **Add** junto a **Dogs**.
2. Seleccione el signo más (+).
3. Escriba un nombre y una ubicación para el refugio, como Contoso y Redmond, WA.
4. Escriba un nombre y una descripción para el perro.
5. Si selecciona el perro, se le dirigirá a la página de detalles, donde podrá actualizar los valores o eliminar la entrada.

## Ejercicio: Administración de permisos

Como se indicó anteriormente, puede agregar usuarios con permisos para modificar datos a través del sitio de administración. Vamos a actualizar el usuario staffuser que creamos en una unidad anterior para que tenga permisos para modificar perros.

### Establecimiento de los permisos de usuario

1. Vuelva al sitio de administración en el explorador.
2. Seleccione **Users**.
3. Seleccione **staffuser** para actualizar nuestro usuario de personal.
4. Desplácese hacia abajo hasta **Permissions**.
5. Seleccione los permisos siguientes:
    - dog_shelters | dog | Can add dog 
    - dog_shelters | dog | Can change dog
    - dog_shelters | dog | Can view dog

### Inicio de sesión como usuario de personal

Veamos ahora la diferencia entre un superusuario y un usuario de personal. Para ello, iniciaremos sesión como usuario de personal.

1. Inicie sesión como **staffuser** con la contraseña que creó anteriormente.
2. Seleccione **Dogs**.
3. Seleccione el perro que creó antes.
    Tenga en cuenta que puede modificar el perro, pero no eliminarlo.