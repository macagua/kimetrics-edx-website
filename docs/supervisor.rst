.. _supervisor-managenment:

Gestión de procesos con Supervisor
===================================

Iniciar el servicio de Supervisor

.. code-block:: bash

    $ sudo /edx/app/supervisor/venvs/supervisor/bin/supervisord \
      -c /edx/app/supervisor/supervisord.conf


.. _supervisor-cli-commands:

Linea de comando de Supervisor
-------------------------------

Puede administrar por linea de comando el servicio de Supervisor, como 
a continuación se describe:

.. _supervisor-cli-status:

Ver que servicios están ejecutándose
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Debería ver algo como lo siguiente:

.. code-block:: bash

    $ sudo /edx/bin/supervisorctl status
    edxapp:cms        RUNNING   pid 30487, uptime 2 days, 4:26:10
    edxapp:lms        RUNNING   pid 30561, uptime 2 days, 4:25:40
    forum             FATAL     Exited too quickly (process log may have details)

Ver el estatus del proceso de la aplicaciones CMS:

.. code-block:: bash

    $ sudo /edx/bin/supervisorctl status edxapp:cms

..
    .. code-block:: bash

        $ sudo /edx/app/supervisor/venvs/supervisor/bin/supervisorctl \
          -c /edx/app/supervisor/supervisord.conf status edxapp:cms

Ver el estatus del proceso de la aplicaciones LMS:

.. code-block:: bash

    $ sudo /edx/bin/supervisorctl status edxapp:lms

..
    .. code-block:: bash

        $ sudo /edx/app/supervisor/venvs/supervisor/bin/supervisorctl \
          -c /edx/app/supervisor/supervisord.conf status edxapp:lms


.. _supervisor-cli-stop:

Detener los servicios
^^^^^^^^^^^^^^^^^^^^^^

.. _supervisor-cli-stop-edxapp:

Detener en conjunto las aplicaciones LMS/CMS

.. code-block:: bash

    $ sudo /edx/bin/supervisorctl stop edxapp:
    edxapp:lms: stopped
    edxapp:cms: stopped

**Nota:** ¡el carácter dos puntos **:** al final de la linea es obligatorio!


.. _supervisor-cli-stop-lms:

Detener en la aplicación LMS

.. code-block:: bash

    $ sudo /edx/bin/supervisorctl stop edxapp:lms
    edxapp:lms: stopped


.. _supervisor-cli-stop-cms:

Detener en la aplicación CMS

.. code-block:: bash

    $ sudo /edx/bin/supervisorctl stop edxapp:cms
    edxapp:cms: stopped


.. _supervisor-cli-start:

Iniciando los servicios
^^^^^^^^^^^^^^^^^^^^^^^^

.. _supervisor-cli-start-edxapp:

Iniciar en conjunto las aplicaciones LMS/CMS

.. code-block:: bash

    $ sudo /edx/bin/supervisorctl start edxapp:
    edxapp:cms: started
    edxapp:lms: started

**Nota:** ¡el carácter dos puntos **:** al final de la linea es obligatorio!


.. _supervisor-cli-start-lms:

Iniciar en la aplicación LMS

.. code-block:: bash

    $ sudo /edx/bin/supervisorctl start edxapp:lms
    edxapp:lms: started


.. _supervisor-cli-start-cms:

Iniciar en la aplicación CMS

.. code-block:: bash

    $ sudo /edx/bin/supervisorctl start edxapp:cms
    edxapp:cms: started


.. _supervisor-cli-start-forum:

Iniciar aplicación Forum

.. code-block:: bash

    $ sudo /edx/bin/supervisorctl start forum


.. _supervisor-cli-restart:

Reiniciando los servicios
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. _supervisor-cli-restart-edxapp:

Reiniciar en conjunto las aplicaciones LMS/CMS

.. code-block:: bash

    $ sudo /edx/bin/supervisorctl restart edxapp:
    edxapp:lms: stopped
    edxapp:cms: stopped
    edxapp:cms: started
    edxapp:lms: started

**Nota:** ¡el carácter dos puntos **:** al final de la linea es obligatorio!


.. _supervisor-cli-restart-lms:

Reiniciar en la aplicación LMS

.. code-block:: bash

    $ sudo /edx/bin/supervisorctl restart edxapp:lms
    edxapp:lms: stopped
    edxapp:lms: started


.. _supervisor-cli-restart-cms:

Reiniciar en la aplicación CMS

.. code-block:: bash

    $ sudo /edx/bin/supervisorctl restart edxapp:cms
    edxapp:cms: stopped
    edxapp:cms: started


.. _supervisor-cli-restart-forum:

Reiniciar aplicación Forum

.. code-block:: bash

    $ sudo /edx/bin/supervisorctl restart forum


.. _supervisor-cli-console:

Consola de comando Supervisor
------------------------------

También puede acceder a consola de comando administrativa de Supervisor

.. code-block:: bash

    $ sudo /edx/bin/supervisorctl -c /edx/app/supervisor/supervisord.conf

..
    .. code-block:: bash

        $ sudo /edx/app/supervisor/venvs/supervisor/bin/supervisorctl \
          -c /edx/app/supervisor/supervisord.conf


.. _supervisor-cli-console-status:

Ver que servicios están ejecutándose
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Ver que procesos se ejecutan desde la consola de comando administrativa, ejecute:

.. code-block:: bash

    supervisor> status
    edxapp:cms        RUNNING   pid 17000, uptime 1:50:45
    edxapp:lms        RUNNING   pid 27391, uptime 0:24:32
    forum             FATAL     Exited too quickly (process log may have details)

Detener los servicios
^^^^^^^^^^^^^^^^^^^^^^

.. _supervisor-cli-console-stop-cms:

Detener aplicación CMS, con el siguiente comando:

.. code-block:: bash

    supervisor> stop edxapp:cms
    edxapp:cms: stopped

.. _supervisor-cli-console-stop-lms:

Detener aplicación LMS, con el siguiente comando:

.. code-block:: bash

    supervisor> stop edxapp:lms
    edxapp:lms: stopped

Ver que procesos se ejecutan desde la consola de comando administrativa, ejecute:

.. code-block:: bash

    supervisor> status
    edxapp:cms        STOPPED   Mar 27 05:08 PM
    edxapp:lms        STOPPED   Mar 27 05:09 PM
    forum             FATAL     Exited too quickly (process log may have details)
    supervisor> 


Iniciando los servicios
^^^^^^^^^^^^^^^^^^^^^^^^
.. _supervisor-cli-console-start-cms:

Iniciar aplicación CMS, con el siguiente comando:

.. code-block:: bash

    supervisor> start edxapp:cms
    edxapp:cms: started

.. _supervisor-cli-console-start-lms:

Iniciar aplicación LMS, con el siguiente comando:

.. code-block:: bash

    supervisor> start edxapp:lms
    edxapp:lms: started

Ver que procesos se ejecutan desde la consola de comando administrativa, ejecute:

.. code-block:: bash

    supervisor> status
    edxapp:cms        RUNNING   pid 30487, uptime 0:00:37
    edxapp:lms        RUNNING   pid 30561, uptime 0:00:07
    forum             FATAL     Exited too quickly (process log may have details)
    supervisor> 

Mostrar el archivo de registro de error de la aplicación CMS, con el siguiente comando:

.. code-block:: bash

    supervisor> tail -10000 edxapp:cms stderr

Mostrar el archivo de registro de acceso de la aplicación CMS, con el siguiente comando:

.. code-block:: bash

    supervisor> tail -10000 edxapp:cms stdout


.. _supervisor-web-client:

Cliente web Supervisor
-----------------------

- Dirección URL: http://127.0.0.1:9001/

- Usuario: TODO.

- Contraseña: TODO.


.. code-block:: bash

    $ sudo /edx/app/supervisor/venvs/supervisor/bin/python \
      /edx/app/supervisor/venvs/supervisor/bin/supervisord \
      -c /edx/app/supervisor/supervisord.conf


.. _supervisor-cli-log:

Archivos de registros
----------------------

Los archivos de registros para eventos y errores están ubicados en 
el directorio :file:`/edx/var/log/supervisor/`.

Mostrar el archivo de registro del servicio supervisor, con el siguiente comando:

.. code-block:: bash

    tail -f /edx/var/log/supervisor/supervisord.log


.. _supervisor-configs:

Configuraciones de Supervisor
------------------------------

.. code-block:: bash

    $ sudo cat /edx/etc/supervisord.conf

.. code-block:: cfg

    ; supervisor config file

    [unix_http_server]
    file=/edx/var/supervisor/supervisor.sock   ; (the path to the socket file)
    chmod=0700                       ; sockef file mode (default 0700)

    [supervisord]
    logfile=/edx/var/log/supervisor/supervisord.log ; (main log file;default $CWD/supervisord.log)
    pidfile=/edx/var/supervisor/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
    childlogdir=/edx/var/log/supervisor            ; ('AUTO' child log dir, default $TEMP)
    nodaemon=true

    ; the below section must remain in the config file for RPC
    ; (supervisorctl/web interface) to work, additional interfaces may be
    ; added by defining them in separate rpcinterface: sections
    [rpcinterface:supervisor]
    supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

    [supervisorctl]
    serverurl=unix:///edx/var/supervisor/supervisor.sock ; use a unix:// URL  for a unix socket

    ; The [include] section can just contain the "files" setting.  This
    ; setting can list multiple files (separated by whitespace or
    ; newlines).  It can also contain wildcards.  The filenames are
    ; interpreted as relative to this file.  Included files *cannot*
    ; include files themselves.

    [inet_http_server]
    port = 127.0.0.1:9001

    [include]
    files = /edx/app/supervisor/conf.d/*.conf


.. code-block:: bash

    $ cat /edx/app/supervisor/conf.available.d/workers.conf

.. code-block:: cfg

    [program:cms_low_1]

    environment=CONCURRENCY=1,LOGLEVEL=info,DJANGO_SETTINGS_MODULE=aws,PYTHONPATH=/edx/app/edxapp/edx-platform,SERVICE_VARIANT=cms
    user=www-data
    directory=/edx/app/edxapp/edx-platform
    stdout_logfile=/edx/var/log/supervisor/%(program_name)-stdout.log
    stderr_logfile=/edx/var/log/supervisor/%(program_name)-stderr.log

    command=/edx/app/edxapp/venvs/edxapp/bin/python /edx/app/edxapp/edx-platform/manage.py cms --settings=aws celery worker --loglevel=info --queues=edx.cms.core.low --hostname=edx.cms.core.low.%%h --concurrency=1
    killasgroup=true
    stopasgroup=true
    stopwaitsecs=432000

    [program:cms_default_1]

    environment=CONCURRENCY=1,LOGLEVEL=info,DJANGO_SETTINGS_MODULE=aws,PYTHONPATH=/edx/app/edxapp/edx-platform,SERVICE_VARIANT=cms
    user=www-data
    directory=/edx/app/edxapp/edx-platform
    stdout_logfile=/edx/var/log/supervisor/%(program_name)-stdout.log
    stderr_logfile=/edx/var/log/supervisor/%(program_name)-stderr.log

    command=/edx/app/edxapp/venvs/edxapp/bin/python /edx/app/edxapp/edx-platform/manage.py cms --settings=aws celery worker --loglevel=info --queues=edx.cms.core.default --hostname=edx.cms.core.default.%%h --concurrency=1
    killasgroup=true
    stopasgroup=true
    stopwaitsecs=432000

    [program:cms_high_1]

    environment=CONCURRENCY=1,LOGLEVEL=info,DJANGO_SETTINGS_MODULE=aws,PYTHONPATH=/edx/app/edxapp/edx-platform,SERVICE_VARIANT=cms
    user=www-data
    directory=/edx/app/edxapp/edx-platform
    stdout_logfile=/edx/var/log/supervisor/%(program_name)-stdout.log
    stderr_logfile=/edx/var/log/supervisor/%(program_name)-stderr.log

    command=/edx/app/edxapp/venvs/edxapp/bin/python /edx/app/edxapp/edx-platform/manage.py cms --settings=aws celery worker --loglevel=info --queues=edx.cms.core.high --hostname=edx.cms.core.high.%%h --concurrency=1
    killasgroup=true
    stopasgroup=true
    stopwaitsecs=432000

    [program:lms_low_1]

    environment=CONCURRENCY=1,LOGLEVEL=info,DJANGO_SETTINGS_MODULE=aws,PYTHONPATH=/edx/app/edxapp/edx-platform,SERVICE_VARIANT=lms
    user=www-data
    directory=/edx/app/edxapp/edx-platform
    stdout_logfile=/edx/var/log/supervisor/%(program_name)-stdout.log
    stderr_logfile=/edx/var/log/supervisor/%(program_name)-stderr.log

    command=/edx/app/edxapp/venvs/edxapp/bin/python /edx/app/edxapp/edx-platform/manage.py lms --settings=aws celery worker --loglevel=info --queues=edx.lms.core.low --hostname=edx.lms.core.low.%%h --concurrency=1
    killasgroup=true
    stopasgroup=true
    stopwaitsecs=432000

    [program:lms_default_1]

    environment=CONCURRENCY=1,LOGLEVEL=info,DJANGO_SETTINGS_MODULE=aws,PYTHONPATH=/edx/app/edxapp/edx-platform,SERVICE_VARIANT=lms
    user=www-data
    directory=/edx/app/edxapp/edx-platform
    stdout_logfile=/edx/var/log/supervisor/%(program_name)-stdout.log
    stderr_logfile=/edx/var/log/supervisor/%(program_name)-stderr.log

    command=/edx/app/edxapp/venvs/edxapp/bin/python /edx/app/edxapp/edx-platform/manage.py lms --settings=aws celery worker --loglevel=info --queues=edx.lms.core.default --hostname=edx.lms.core.default.%%h --concurrency=1
    killasgroup=true
    stopasgroup=true
    stopwaitsecs=432000

    [program:lms_high_1]

    environment=CONCURRENCY=1,LOGLEVEL=info,DJANGO_SETTINGS_MODULE=aws,PYTHONPATH=/edx/app/edxapp/edx-platform,SERVICE_VARIANT=lms
    user=www-data
    directory=/edx/app/edxapp/edx-platform
    stdout_logfile=/edx/var/log/supervisor/%(program_name)-stdout.log
    stderr_logfile=/edx/var/log/supervisor/%(program_name)-stderr.log

    command=/edx/app/edxapp/venvs/edxapp/bin/python /edx/app/edxapp/edx-platform/manage.py lms --settings=aws celery worker --loglevel=info --queues=edx.lms.core.high --hostname=edx.lms.core.high.%%h --concurrency=1
    killasgroup=true
    stopasgroup=true
    stopwaitsecs=432000

    [program:lms_high_mem_1]

    environment=CONCURRENCY=1,LOGLEVEL=info,DJANGO_SETTINGS_MODULE=aws,PYTHONPATH=/edx/app/edxapp/edx-platform,SERVICE_VARIANT=lms
    user=www-data
    directory=/edx/app/edxapp/edx-platform
    stdout_logfile=/edx/var/log/supervisor/%(program_name)-stdout.log
    stderr_logfile=/edx/var/log/supervisor/%(program_name)-stderr.log

    command=/edx/app/edxapp/venvs/edxapp/bin/python /edx/app/edxapp/edx-platform/manage.py lms --settings=aws celery worker --loglevel=info --queues=edx.lms.core.high_mem --hostname=edx.lms.core.high_mem.%%h --concurrency=1
    killasgroup=true
    stopasgroup=true
    stopwaitsecs=432000


    [group:edxapp_worker]
    programs=cms_low_1,cms_default_1,cms_high_1,lms_low_1,lms_default_1,lms_high_1,lms_high_mem_1


.. code-block:: bash

    $ ls /edx/app/supervisor/conf.d/*.conf
    /edx/app/supervisor/conf.d/cms.conf     /edx/app/supervisor/conf.d/forum.conf
    /edx/app/supervisor/conf.d/edxapp.conf  /edx/app/supervisor/conf.d/lms.conf

