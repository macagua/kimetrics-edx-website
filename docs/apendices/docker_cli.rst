.. _dockercli-command:

Comando Docker para edX
=======================


.. _dockercli-image-download:

Descargar imagen Docker
-----------------------

Para este instalación se requiere el stack completo para entornos de producción, 
en Docker esta disponible como una imagen Docker, la cual requiere descargar 
para usar, ejecutando el siguiente comando:

.. code-block:: bash

    $ sudo docker pull appsembler/edx-full


.. _dockercli-image-inspect:

Inspeccionar imagen Docker
--------------------------

Si requiere saber que volumenes o puertos usa la imagen previamente descargando, 
usted puede inspeccionar imagen Docker, ejecutando el siguiente comando:

.. code-block:: bash

    $ sudo docker inspect appsembler/edx-full


.. _dockercli-container-run:

Ejecutar contenedor Docker
--------------------------

Cada vez que ejecuta por primera vez un contenedor este es creado y luego 
ejecutando, para hacer esto, ejecute el siguiente comando:

.. code-block:: bash

    $ sudo docker run -ti --name my-edx-full -p 8000:80 -p 8010:18010 \
      -d -v ~/configuration:/configuration appsembler/edx-full:latest

..
    $ sudo docker run -i -t -e MANDRILL_API_KEY=xxxx -p :80 -p :18010 -p :18020 -d -v ~/configuration:/configuration phusion/baseimage /sbin/my_init --enable-insecure-key

    $ sudo docker run -i -t -p :80 -p :18010 -p :18020 -d -v ~/configuration:/configuration appsembler/edx-full:latest /sbin/my_init --enable-insecure-key

    $ sudo docker run -i -t --name my-edx-full -p :80 -p :18010 -d -v ~/configuration:/configuration appsembler/edx-full:latest /sbin/my_init --enable-insecure-key

    $ sudo docker run -ti --name myContenedorMollejuo -p 8000:80 -d imagenMollejua:latest

    $ sudo docker run -it edxops/precise-common /bin/bash


.. _dockercli-container-list:

Lista contenedores Docker
-------------------------

Luego de haber creado contenedores Docker usted puede listar los mismos, 
ejecutando el siguiente comando:

.. code-block:: bash

    $ sudo docker container ls


.. _dockercli-container-top:

Supervisar contenedor Docker
----------------------------

Cuando un contenedor Docker esta en ejecución, usted puede monitorear los 
procesos que dicho contenedor esta consumiendo, ejecutando el siguiente comando:

.. code-block:: bash

    $ sudo docker container top my-edx-full


.. _dockercli-container-rm:

Remover contenedor Docker
-------------------------

Puede remover el contenedor Docker creado, ejecutando el siguiente comando:

.. code-block:: bash

    $ sudo docker container rm -f ae945743fd10
    ae945743fd10


.. _dockercli-container-run-shell:

Ejecutar shell de contenedor
----------------------------

Puede ejecutar el *shell de comando* del contenedor Docker creado, ejecutando 
el siguiente comando:

.. code-block:: bash

    $ sudo docker exec -it my-edx-full bash

De esta forma puede acceder vía *shell de comando* del contenedor Docker para 
ejecutar comando dentro del mismo.


Referencia
----------

- `Docker powered Open edX devstack <https://www.appsembler.com/blog/docker-powered-open-edx-devstack/>`_.
- `Open edX on Docker <https://openedx.atlassian.net/wiki/spaces/OpenOPS/pages/42599368/Open+edX+on+Docker>`_.

