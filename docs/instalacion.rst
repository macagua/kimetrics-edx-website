.. _instalacion-detallada-label:


Instalación detallada
=====================

Para ajustar las configuraciones del sitio usted DEBE:

.. _command-connect-server:


Paso 1
------

Debe haber iniciado una conexión al servidor hosting, ejecutando los siguientes comando:

.. code-block:: bash

    $ chmod 400 edx.pem
    $ ssh -v -i edx.pem ubuntu@ec2-54-152-79-203.compute-1.amazonaws.com


Paso 2
------

Debe actualizar el sistema operativo, ejecutando los siguientes comando:

.. code-block:: bash

    $ sudo apt-get update
    $ sudo apt-get upgrade


Paso 3
------

Debe instalar utilidades de gestión del sistema operativo:

.. code-block:: bash

    $ sudo apt-get install git etckeeper screen tree htop


Paso 4
------

Debe instalar el *Docker Community Edition*, ejecutando los siguientes comando:

.. code-block:: bash

    $ sudo apt-get remove docker docker-engine docker.io
    $ sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    $ sudo apt-key fingerprint 0EBFCD88
    $ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
        $(lsb_release -cs) \
        stable"
    $ sudo apt-get update
    $ sudo apt-get install docker-ce


Paso 5
------

Debe ejecutar contenedor Docker de ejemplo, para probar la instalación previa, con el 
siguiente comando:

.. code-block:: bash

    $ sudo docker run hello-world


Paso 6
------

Debe descargar imagen Docker para el stack completo de Open edX, con el 
siguiente comando:

.. code-block:: bash

    $ sudo docker pull appsembler/edx-full

.. note::

    Puede consultar más detalles de este comando :ref:`aquí <dockercli-image-download>`.


Paso 7
------

Debe descargar repositorio Git temporal de configuraciones del sitio web para Open edX, 
ejecutando los siguientes comando:

.. code-block:: bash

    # cd ~/
    # git clone https://macagua@bitbucket.org/macagua/kimetrics-edx-website.git configuration


Paso 8
------

Debe crear directorio de respaldo usando como volumen de contenedor Docker a crear para Open edX, 
con el siguiente comando:

.. code-block:: bash

    # mkdir ~/backups


Paso 9
------

Debe ejecutar un nuevo contenedor Docker para el stack completo de Open edX, ejecutando 
los siguientes comando:

.. code-block:: bash

    $ sudo docker run -ti --name my-edx-full -p 8000:80 -p 8010:18010 \
      -d -v ~/backups:/edx/backups -v ~/configuration:/configuration \
      appsembler/edx-full:latest

..
    $ sudo docker run -ti --name my-edx-full -p 80:80 -p 18010:18010 \ 
      -p 18090:18090 -p 18080:18080 -p 18130:18130 \
      -v ~/backups:/edx/backups -v ~/configuration:/configuration \
      -d appsembler/edx-full:latest

..
    "/sbin/my_init --ena…" 18040/tcp, 18060/tcp, 18080/tcp, 0.0.0.0:8000->80/tcp, 0.0.0.0:8010->18010/tcp

    -p 18090:18090

    LMS 80
    CMS 18010
    Certs   18090 
    Discovery   18381
    Ecommerce   18130
    edx-release 8099
    Forum   18080
    Xqueue  18040

.. note::

    Puede consultar más detalles de este comando :ref:`aquí <dockercli-container-run>`.


Paso 10
-------

Debe implementar configuraciones iniciales, entonces para facilitar el despliegue 
de configuraciones para el stack completo de Open edX DEBE: 


Paso 10.1
^^^^^^^^^

Debe ejecutar shell de comando de contenedor Docker, ejecutando el siguiente comando:

.. code-block:: bash

    $ sudo docker exec -it my-edx-full bash

.. note::

    Puede consultar más detalles de este comando :ref:`aquí <dockercli-container-run-shell>`.


Paso 10.2
^^^^^^^^^

Debe dentro del shell de comando del contenedor Docker, ejecutando los siguientes comando:

.. code-block:: bash

    # sudo -H -u edxapp bash

    $ mv /edx/app/edxapp/lms.auth.json /edx/app/edxapp/lms.auth.json.bnk
    $ ln -s /configuration/edx/app/edxapp/lms.auth.json /edx/app/edxapp/lms.auth.json

    $ mv /edx/app/edxapp/lms.env.json /edx/app/edxapp/lms.env.json.bnk
    $ ln -s /configuration/edx/app/edxapp/lms.env.json /edx/app/edxapp/lms.env.json

    $ mv /edx/app/edxapp/cms.auth.json /edx/app/edxapp/cms.auth.json.bnk
    $ ln -s /configuration/edx/app/edxapp/cms.auth.json /edx/app/edxapp/cms.auth.json

    $ mv /edx/app/edxapp/cms.env.json /edx/app/edxapp/cms.env.json.bnk
    $ ln -s /configuration/edx/app/edxapp/cms.env.json /edx/app/edxapp/cms.env.json
    
    $ ln -s /configuration/edx/app/edxapp/themes/kimetrics-edx-platform-theme /edx/var/edxapp/themes/kimetrics-edx-platform-theme

    $ exit


Paso 11
-------

Debe reiniciar las aplicaciones *Open edX*, ejecutando el siguiente comando:

.. code-block:: bash

    $ sudo /edx/bin/supervisorctl restart edxapp:

.. note::

    Puede consultar más detalles de este comando :ref:`aquí <supervisor-cli-restart-edxapp>`.


Paso 12
-------

Debe reiniciar la aplicación *Forum*, ejecutando el siguiente comando:

.. code-block:: bash

    $ sudo /edx/bin/supervisorctl restart forum

.. note::

    Puede consultar más detalles de este comando :ref:`aquí <supervisor-cli-restart-forum>`.


Paso 13
-------

Debe crear súper usuario para el *Django Admin Site*, ejecutando el siguiente comando:

.. code-block:: bash

    $ sudo -u www-data /edx/bin/python.edxapp /edx/app/edxapp/edx-platform/manage.py lms \
      --settings aws createsuperuser --username=USERNAME --email=YOUR_EMAIL@MAIL.com \
      --noinput

.. note::

    Puede consultar más detalles de este comando :ref:`aquí <django-managerpy-createsuperuser>`.


Paso 14
-------

Debe definir contraseña al súper usuario del *Django Admin Site*, ejecutando el siguiente comando:

.. code-block:: bash

    $ sudo -u www-data /edx/bin/python.edxapp /edx/app/edxapp/edx-platform/manage.py lms \
      --settings aws changepassword USERNAME

.. note::

    Puede consultar más detalles de este comando :ref:`aquí <django-managerpy-changepassword>`.


Referencia
----------

- `Open edX install <https://www.youtube.com/watch?v=Dy2lxgvkdQk>`_.
- `Multi language support on Open edX <http://learning.perpetualny.com/blog/multi-language-support-on-open-edx>`_.
- `Running Open EdX in Production <https://openedx.atlassian.net/wiki/spaces/OpenOPS/pages/60228120/Running+Open+EdX+in+Production>`_.
- `Invalid Django TIME_ZONE from Stack Overflow <https://stackoverflow.com/questions/5750150/invalid-django-time-zone/5750267#5750267>`_.
- `OpenEdx Django admin panel from Open edX operations maillist <https://groups.google.com/forum/#!msg/openedx-ops/M5ytgpw57EE/MZs41-yIFAAJ>`_.
