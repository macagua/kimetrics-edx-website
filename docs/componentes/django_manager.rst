.. _django-manager:


Administrar servidor Django
===========================

En el servidor que hospeda el stack de la aplicación Open edX, uno 
de sus componentes son las aplicaciones Django las cuales son 
administradas de la siguiente forma:


.. _django-adminsite:

Django Admin Site
------------------

El Django Admin Site es una Django webapp que incorpora para realizar muchas labores de 
gestión del stack de producción de Open edX.

Para usar debe comprobar si en el modulo :file:`/edx/app/edxapp/edx-platform/lms/envs/common.py` 
tiene la variable de entorno ``ENABLE_DJANGO_ADMIN_SITE`` definida a ``True``.

::

    'ENABLE_DJANGO_ADMIN_SITE': True,

Si es así usted puede acceder al *Django Admin Site* en la siguiente dirección URL: 

    http://dominio/admin/

Si aun esto no funciona y, dado que usted es responsable de la administración de la base de datos, 
compruebe la tabla ``edxapp.auth_user`` existe en su base de datos por defecto llamada ``edxapp`` 
y actualice los valores ``is_staff``, ``is_active`` e ``is_superuser`` mientras encuentra lo que está fallando.


Inicie sesión en el servidor y cambie al directorio /edx/app/edxapp/edx-platform para usar manage.py

Crear super-usuario (con perfil): reemplace "us ... @ example.com" con la dirección de correo electrónico que desea usar y reemplace "usuario" con la primera parte de la dirección de correo electrónico que utilizó. Entonces, por ejemplo, si usa "staf ... @ yourdomain.com" para la dirección de correo electrónico, el nombre de usuario será "staff + 87".


.. code-block:: bash

    $ sudo -u edxapp bash

.. code-block:: bash

    $ source /edx/app/edxapp/edxapp_env

.. code-block:: bash

    $ cd /edx/app/edxapp/edx-platform


.. code-block:: bash

    $ /edx/bin/python.edxapp ./manage.py lms --settings aws


.. _django-managerpy:

Módulo `manage.py`
------------------

Uno de sus componentes del stack Open edX instalado en su contenedor 
Docker, son las aplicaciones Django las cuales son administradas vía 
consola de comando mediante el módulo ``manage.py``. Entonces debe 
iniciar sesión de usuario en el servidor, luego ejecutar el *shell* 
de su contenedor Docker que hospeda el stack Open edX y ejecutar los 
siguientes comando:


.. _django-managerpy-help:

Comando de ayuda
^^^^^^^^^^^^^^^^

Este comando de ayuda lista todos los comando disponibles en el módulo 
``manage.py``, ejecutando el siguiente comando:

.. code-block:: bash

    $ sudo -u www-data /edx/bin/python.edxapp /edx/app/edxapp/edx-platform/manage.py \
      lms --settings aws help


.. _django-managerpy-createsuperuser:

Crear o actualizar súper usuario
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Para crear un usuario nuevo como *súper usuario*, ejecute el siguiente 
comando:

**Consejo:** Sustituya el *nombre de usuario* y la *cuenta del correo 
electrónico* apropiadamente.

.. code-block:: bash

    $ sudo -u www-data /edx/bin/python.edxapp /edx/app/edxapp/edx-platform/manage.py \
      lms --settings aws createsuperuser --username=USERNAME \
      --email=YOUR_EMAIL@MAIL.com --noinput

..
    $ sudo -u www-data /edx/bin/python.edxapp 
      /edx/app/edxapp/edx-platform/manage.py lms \
      --settings aws createsuperuser --username=leonardo \
      --email=leonardocaballero@gmail.com --noinput


.. _django-managerpy-changepassword:

Definir o cambiar contraseña
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Luego de crear un *súper usuario* nuevo debe definir su contraseña, ejecutando 
el siguiente comando:

**Consejo:** Sustituya el *nombre de usuario* apropiadamente.

.. code-block:: bash

    $ sudo -u www-data /edx/bin/python.edxapp /edx/app/edxapp/edx-platform/manage.py \
      lms --settings aws changepassword USERNAME

..
    $ sudo -u www-data /edx/bin/python.edxapp 
      /edx/app/edxapp/edx-platform/manage.py lms \
      --settings aws changepassword leonardo


.. _django-managerpy-shell:

Lanzar el shell de django
^^^^^^^^^^^^^^^^^^^^^^^^^

Alternativamente puede lanzar el *shell de django* para alterar datos del 
usuario, ejecutando por ejemplo el comando:

.. code-block:: bash

    $ sudo -u www-data /edx/bin/python.edxapp /edx/app/edxapp/edx-platform/manage.py \
      lms --settings aws shell

Al abrir el *shell de django* ingrese la siguiente sintaxis Python:

.. code-block:: python

    >>> from django.contrib.auth.models import User
    >>> me = User.objects.get(username="USER")
    >>> me.is_superuser = False
    >>> me.is_staff = True
    >>> me.save()


.. _django-managerpy-createuser:

Crear o actualizar usuario de aplicación
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Para crear un *usuario nuevo* o *actualizar un usuario* a que sea 
**súper usuario**, ejecute los siguiente comando:

**Consejo:** Sustituya el *nombre de usuario* y la *cuenta del correo 
electrónico* apropiadamente.

.. code-block:: bash

    $ sudo -u www-data /edx/bin/python.edxapp /edx/app/edxapp/edx-platform/manage.py \
      lms --settings aws help create_user -s -p PASSWORD \
      -e YOUR_EMAIL@MAIL.com -c edX/Open_DemoX/edx_demo_course


.. _django-managerpy-regenerateuser:

Aprobación final y generar certificado de un solo usuario
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Para crear una calificación final y generar un certificado para un **solo** usuario

.. code-block:: bash

    $ sudo -u www-data /edx/bin/python.edxapp /edx/app/edxapp/edx-platform/manage.py \
      lms --settings aws regenerate_user -u honor@example.com \
      -c edX/Open_DemoX/edx_demo_course


Referencia
----------

- `Running Open EdX in Production <https://openedx.atlassian.net/wiki/spaces/OpenOPS/pages/60228120/Running+Open+EdX+in+Production>`_.
- `OpenEdx Django admin panel from Open edX operations maillist <https://groups.google.com/forum/#!msg/openedx-ops/M5ytgpw57EE/MZs41-yIFAAJ>`_.
