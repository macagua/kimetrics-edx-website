.. _configuraciones-label:


Ajustar configuraciones del sitio
=================================

Para ajustar las configuraciones del sitio usted DEBE:

* Haber iniciado una :ref:`conexión al servidor <command-connect-server>`: hosting.

* :ref:`Ejecutar shell de comando <dockercli-container-run-shell>`: de contenedor Docker creado.


Configuraciones del LMS
------------------------

Para definir sus parámetros de configuración del LMS, ejecutando el siguiente comando:

.. code-block:: bash

    $ vi /edx/app/edxapp/lms.env.json

..
    Alternativamente para instalación de bitnami edx-ginkgo.2-1 puede 
    ejecutar con el siguiente comando:

    .. code-block:: bash

        $ vi ~/edx-ginkgo.2-1/apps/edx/conf/lms.env.json


Ajuste la siguiente configuración, ejecutando el siguiente comando:

::

    "EMAIL_HOST": "smtp.sendgrid.net",
    "EMAIL_PORT": 587,
    "LANGUAGE_CODE": "es_419",

Configure las credenciales de autenticación, ejecutando el siguiente comando:

.. code-block:: bash

    $ vi /edx/app/edxapp/lms.auth.json

..
    Alternativamente para instalación de bitnami edx-ginkgo.2-1 puede 
    ejecutar con el siguiente comando:

    .. code-block:: bash

        $ vi ~/edx-ginkgo.2-1/apps/edx/conf/lms.auth.json

Ajuste la siguiente configuración, ejecutando el siguiente comando:

::

    "EMAIL_HOST_PASSWORD": "erik_river",
    "EMAIL_HOST_USER": "Kpfp7eLt",


Configuraciones del CMS
------------------------

Para definir sus parámetros de configuración del CMS, ejecutando el siguiente comando:

.. code-block:: bash

    $ vi /edx/app/edxapp/cms.env.json
    $ vi /edx/app/edxapp/cms.auth.json

..
    Alternativamente para instalación de bitnami edx-ginkgo.2-1 puede 
    ejecutar con el siguiente comando:

    .. code-block:: bash

        $ vi ~/edx-ginkgo.2-1/apps/edx/conf/cms.env.json
        $ vi ~/edx-ginkgo.2-1/apps/edx/conf/cms.auth.json

Ajuste la siguiente configuración, ejecutando el siguiente comando:

::

    "LANGUAGE_CODE": "es_419",


Configuraciones de Supervisor
-----------------------------

.. code-block:: bash

    # ls -lhap /edx/etc/conf.d/
    total 8.0K
    drwxrwxr-x 1 supervisor www-data   4.0K Oct  3  2014 ./
    drwxr-xr-x 1 supervisor www-data   4.0K Mar 27 14:51 ../
    lrwxrwxrwx 1 supervisor supervisor   45 Sep 25  2014 cms.conf -> /edx/app/supervisor/conf.available.d/cms.conf
    lrwxrwxrwx 1 supervisor supervisor   48 Sep 25  2014 edxapp.conf -> /edx/app/supervisor/conf.available.d/edxapp.conf
    lrwxrwxrwx 1 supervisor supervisor   47 Sep 25  2014 forum.conf -> /edx/app/supervisor/conf.available.d/forum.conf
    lrwxrwxrwx 1 supervisor supervisor   45 Sep 25  2014 lms.conf -> /edx/app/supervisor/conf.available.d/lms.conf

.. code-block:: bash

    # cat /edx/etc/conf.d/edxapp.conf
    [group:edxapp]
    programs=lms,cms


.. _config-connect-server:

Conectar al servidor hosting
-----------------------------

Para conectarse al servidor de Amazon, ejecute el siguiente comando:

.. code-block:: bash

    $ chmod 400 edx.pem
    $ ssh -v -i edx.pem ubuntu@ec2-54-152-79-203.compute-1.amazonaws.com


Activación por Correos electrónicos
-----------------------------------

Permitir al servidor SMTP en la configuración de EDX es una parte de completar 
los pasos necesarios para los correos electrónicos de activación profesionales 
que los usuarios de los estudiantes y el futuro personal recibirán. Por defecto, 
el nombre del sitio de la aplicación EDX está establecido en ``localhost``. Esto 
significa que en los correos electrónicos de activación, sus futuros usuarios 
estudiantes recibirán una URL de activación con ``localhost`` en lugar de la URL 
preferida y configurada para su servidor. Para proporcionar al usuario futuro una 
URL con un nombre de sitio correcto, tendremos que cambiar tres variables para 
nuestra próxima actualización:

.. code-block:: yaml

    #Default Configuration EDXAPP_SITE_NAME: 'localhost'

    EDXAPP_LMS_SITE_NAME: "{{ EDXAPP_SITE_NAME }}"

    EDXAPP_CMS_SITE_NAME: 'localhost'

    #Example Configuration EDXAPP_SITE_NAME: 'www.myedxapp.com'

    EDXAPP_LMS_SITE_NAME: "www.myedxapp.com:80"

    EDXAPP_CMS_SITE_NAME: 'www.myedxapp.com:18010'

A continuación, agregue estas tres variables a archivo :file:`/edx/app/edx_ansible/server-vars.yml` 
Una vez hecho esto ejecute el siguiente comando ``sudo /edx/bin/update edx-platform release`` - actualice su plataforma edx utilizando el repositorio bifurcado con sus nuevas variables.


Configuración de correo electrónico y redes sociales
-----------------------------------------------------

Las configuraciones que nos preocupan en este momento se destacan a continuación:

.. code-block:: yaml

    EDXAPP_FEEDBACK_SUBMISSION_EMAIL: 'feedback@email.ca'
    EDXAPP_TECH_SUPPORT_EMAIL: 'support@email.ca'
    EDXAPP_CONTACT_EMAIL: 'contact@email.ca'
    EDXAPP_BUGS_EMAIL: 'support@email.ca'
    EDXAPP_DEFAULT_FROM_EMAIL: 'registration@email.ca'
    EDXAPP_DEFAULT_FEEDBACK_EMAIL: 'feedback@email.ca'
    EDXAPP_DEFAULT_SERVER_EMAIL: 'server@email.ca'
    EDXAPP_BULK_EMAIL_DEFAULT_FROM_EMAIL: 'bulk@email.ca'
    EDXAPP_UNIVERSITY_EMAIL: 'university@email.ca'
    EDXAPP_PLATFORM_TWITTER_ACCOUNT: '@here'
    EDXAPP_PLATFORM_FACEBOOK_ACCOUNT: 'https://www.facebook.com/like'
    EDXAPP_PRESS_EMAIL: 'press@email.ca'
    EDXAPP_PAYMENT_SUPPORT_EMAIL: "billing@email.ca"


Habilitar SMTP para EDX
-----------------------

Más información consulte la siguiente wiki https://github.com/CDOT-EDX/ProductionStackDocs/wiki/Enable-SMTP-for-EDX-(Production-Stack)


Cambio de puerto para LMS y CMS
-------------------------------

Puede especificar los puertos para el LMS y el CMS utilizando el ``server-vars.yml`` que se encuentra en el directorio ``/edx/app/edx_ansible/``. El archivo ``main.yml`` en https://github.com/edx/configuration/blob/master/playbooks/roles/edxapp/defaults/main.yml especifica estas variables como:

::

  EDXAPP_LMS_NGINX_PORT: 18000
  EDXAPP_CMS_NGINX_PORT: 18010

Una vez que esté listo para cambiar estos puertos, puede hacer:

::

  $ sudo /edx/bin/update edx-platform master

O ::

  $ sudo rm -rf /edx/app/edxapp/edx-platform

  $ sudo /edx/bin/update configuration RELEASE (Where RELEASE is an available repository branch)

  $ cd /edx/app/edx_ansible/edx_ansible/playbooks/edx-east

  $ sudo /edx/app/edx_ansible/venvs/edx_ansible/bin/ansible-playbook -i localhost, -c local edxapp.yml -vvv -e@/edx/app/edx_ansible/server-vars.yml


También hay una forma manual de configurar los puertos LMS y CMS. La publicación del blog en http://iambusychangingtheworld.blogspot.ca/2014/03/edx-platform-to-run-cms-at-port-80.html menciona cómo hacer esto. En la instancia de la aplicación EDX en ``/edx/app/nginx/sites-available/``, los sitios se han alojado para la instancia de EDX; tanto LMS como CMS. Por ejemplo, en el bloque de servidor del archivo del servidor CMS, el puerto puede cambiarse a 80:

::

    upstream cms-backend {
      server 127.0.0.1:8010 fail_timeout=0;
    }

    server {
      # CMS configuration file for nginx, templated by ansible
      listen 80 ;
      server_name ~^((stage|prod)-)?studio\..*;
    }

próximo hacer :

::

  $ sudo service nginx restart

Esto aplica los cambios que ha realizado


MySQL Servidor
--------------

Cambiar la contraseña para el usuario de MySQL
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Cambiar la contraseña de MySQL es una tarea especialmente importante para el desarrollo 
de EDX porque no es seguro mantener las contraseñas predeterminadas de la instalación 
inicial. A continuación se detallan los siguientes pasos para cambiar la contraseña de 
un usuario de MySQL:

Debe iniciar sesión con nombre de usuario root:

.. code-block:: yaml

    mysql -u root -p -h localhost

Cambiar la contraseña de usuario de MySQL:

.. code-block:: sql

    UPDATE mysql.user SET Password=PASSWORD('newPassword***') WHERE User='root' AND Host='localhost';

Referencia
-----------

- `Open edX install <https://www.youtube.com/watch?v=Dy2lxgvkdQk>`_.
- `Running Open EdX in Production <https://openedx.atlassian.net/wiki/spaces/OpenOPS/pages/60228120/Running+Open+EdX+in+Production>`_.
- `OpenEdx Django admin panel from Open edX operations maillist <https://groups.google.com/forum/#!msg/openedx-ops/M5ytgpw57EE/MZs41-yIFAAJ>`_.
- `Activation Emails from Open edX Production Stack <https://github.com/CDOT-EDX/ProductionStackDocs/wiki/Activation-Emails(Production-Stack)>`_.
- `Email and Social Media Settings from Open edX Production Stack <https://github.com/CDOT-EDX/ProductionStackDocs/wiki/Email-and-Social-Media-Settings-(Production-Stack)>`_.
- `Enable SMTP for EDX from Open edX Production Stack <https://github.com/CDOT-EDX/ProductionStackDocs/wiki/Enable-SMTP-for-EDX-(Production-Stack)>`_.
- `Changing The Password for MySQL User from Open edX Production Stack <https://github.com/CDOT-EDX/ProductionStackDocs/wiki/Changing-The-Password-for-MySQL-User-(Production-Stack)>`_.
- `Changing LMS and CMS port from Open edX Production Stack <https://github.com/CDOT-EDX/ProductionStackDocs/wiki/Changing-LMS-and-CMS-port-(Production-Stack)>`_.
