.. _forum-app:

Forum de mensajes
=================

Consola administrativa
-----------------------

Debe acceder a consola administrativa de Supervisor como se describe 
con el comando :ref:`aquí <supervisor-cli-console>`.

.. _forum-app-cli-status:

Consultar estatus de Forum
---------------------------

Consultar el estatus de la aplicación Forum

.. code-block:: bash

    supervisor> status forum
    forum             FATAL     Exited too quickly (process log may have details)
    supervisor> 

.. _forum-app-cli-start:

Iniciar aplicación Forum
-------------------------

Iniciar el servicio de la aplicación Forum

.. code-block:: bash

    supervisor> start forum
    forum: started

.. _forum-app-cli-error-log:

Registro de error aplicación Forum
-----------------------------------

Consultar el archivo log de error de la aplicación Forum

.. code-block:: bash

    supervisor> tail -100000 forum stderr

.. _forum-app-cli-access-log:

Registro de acceso aplicación Forum
------------------------------------

Consultar el archivo log de acceso de la aplicación Forum

.. code-block:: bash

    supervisor> tail -10000 forum stdout

..
    Mostrar el archivo de registro de error de la aplicación Forum, con el siguiente comando:

    .. code-block:: bash

        tail -f /edx/var/log/supervisor/forumtderr.log

    Mostrar el archivo de registro de acceso de la aplicación Forum, con el siguiente comando:

    .. code-block:: bash

        tail -f /edx/var/log/supervisor/forumtdout.log

.. _forum-app-api-test:

Probar Api Rest
----------------

.. todo:: Terminar la traducción de esta sección que actualmente esta comentada

..
    # accessing forums is not authorized without the key

    .. code-block:: bash

        curl -i http://localhost:18080/api/v1/threads
        > HTTP/1.1 401 Unauthorized

    # here it connects successfully

    .. code-block:: bash

        curl -i --header "X-Edx-Api-Key: password" http://localhost:18080/api/v1/threads
        > HTTP/1.1 200 OK

    $ sudo vi /edx/app/forum/forum_env
    export RACK_ENV = "production"
    export SINATRA_ENV = "production"
    # API_KEY should match COMMENTS_SERVICE_KEY (which can be found in /edx/app/edxapp/lms.env.json)


    $ sudo -u www-data /edx/bin/python.edxapp ./manage.py lms --settings aws create_user -e us...@example.com

    curl --header "X-Edx-Api-Key: PUT_YOUR_API_KEY_HERE" www.examplewebsite.com/courses/nameofthecourse/1/1/discussion/forum/

    If I try the curl with out "forum"
    curl --header "X-Edx-Api-Key: PUT_YOUR_API_KEY_HERE" www.examplewebsite.com/courses/nameofthecourse/1/1/discussion/

    I recieve data

    revised steps: 

    1. In the forum environment please set API_KEY to something like "test-api-key" and then restart the comments service
    2. curl the comments service directly to ensure it is working:
          > curl -v -X GET --header "X-Edx-Api-Key: test-api-key" "http://localhost:18080/selftest" # should output healthy-looking diagnostic JSON, if not something is wrong here. 
    3. Set COMMENTS_SERVICE_KEY in the edxapp environment to the same value e.g. "test-api-key" (and restart lms if it is running)
    4. try create_user again

    COMMENTS_SERVICE_KEY
    I have changed COMMENT_SERVICE_URL in lms.envs.json

    You are missing http:// (or https://) at the beginning of the comments service URL.


    La clave de la API debe coincidir en ambos lados. Se configura aquí:

    EDX-plataforma: COMMENTS_SERVICE_KEY en su (entorno de desarrollo) settings.py archivo o ENV_TOKENS (entorno prod)
    cs_comments_service: api_key en el archivo application.yml (entorno de desarrollo) o variable ENV (entorno prod)

    The API key must match on both sides. It is configured here:

    edx-platform: COMMENTS_SERVICE_KEY in your settings.py file (dev environment) or ENV_TOKENS (prod environment)
    cs_comments_service: api_key in the application.yml file (dev environment) or ENV variable (prod environment)



    A troubleshooting tip: 
    You should be able to verify that the comments service is up and running with curl or a browser using the API, like this: 
    https://<server>:<port>/api/v1/users/<usernumber>?api_key=<API-KEY>&complete=True

    One more note, on my previous post there was a typo where I had meant http instead of https for verifying via curl or browser. E.g. 
    http://<server>:<port>/api/v1/users/<usernumber>?api_key=<API-KEY>&complete=True

    The usernumber is the same userid as in the user table on the lms side. Try using 1 to get the first user's info.

    http://iitbombayx.cse.iitb.ac.in:4567/api/v1/users/124/api_key=<API-KEY>&complete=True

    Finally  discussion forum issue has resolved. 
    There were mainly two issues.

    1) My installation was missing forum folder under /edx/var, so I just created forum folder and added correct permissions.

    2) We were using February code where in the configuration file port used was 4567, which was not having unicorn_tcp.rb under forum/config which in turn redirecting to 4567 port in other releases. I have replaced this config folder with the December code which was using 18080 port. Modified the port in lms.env.json file. 
    Restarted forum and edxapp service, now everything is working well.

    Correct permission means all files and folders should have default owner and permission. 

    In /edx/app/forum/cs_comments_service
    sudo chown -R forum:forum *
    sudo chmod -R 755 *

    In /edx/var/
    sudo chown forum:www-data forum

    Restart forum service 
    sudo /edx/app/supervisor/venvs/supervisor/bin/supervisorctl -c /edx/app/supervisor/supervisord.conf restart forum

    Check status 
    sudo /edx/app/supervisor/venvs/supervisor/bin/supervisorctl -c /edx/app/supervisor/supervisord.conf 

    Also check in your mongodb, cs_comments_service_development has below collections(if development stack, cs_comment_service for production stack)

    >use cs_comments_service_development
    >switched to db cs_comments_service
    > show collections
    contents
    subscriptions
    system.indexes
    system.users
    users

.. _forum-app-updating:

Actualizar cs_comments_service
-------------------------------

Para actualizar la aplicación cs_comments_service desde el repositorio github, 
ejecute el siguiente comando:

.. code-block:: bash

    sudo /edx/bin/update cs_comments_service master


.. code-block:: bash

    $ sudo vi /edx/etc/conf.d/forum.conf 

.. code-block:: cfg

    [program:forum]
    command=/edx/app/forum/forum-supervisor.sh
    priority=999
    user=www-data
    stdout_logfile=/edx/var/log/supervisor/%(program_name)-stdout.log
    stderr_logfile=/edx/var/log/supervisor/%(program_name)-stderr.log
    killasgroup=true
    stopasgroup=true
    stopsignal=QUIT


.. code-block:: bash

    sudo cat /edx/app/forum/forum-supervisor.sh

.. code-block:: bash

    #!/bin/bash

    source /edx/app/forum/forum_env
    cd /edx/app/forum/cs_comments_service

    /edx/app/forum/.gem/bin/unicorn -c config/unicorn.rb

.. code-block:: bash

    ls /edx/app/forum/
    cs_comments_service  forum-supervisor.sh  forum_env  ruby_env

.. code-block:: bash

    cat /edx/app/forum/forum_env

.. code-block:: bash

    # Ansible managed: /configuration/playbooks/roles/forum/templates/forum_env.j2 modified on 2014-09-18 21:43:35 by root on bf530c094a19

    export LISTEN_HOST="0.0.0.0"
    export NEW_RELIC_APP_NAME="default_env-default_deployment-forum"
    export DATA_DIR="/edx/var/forum"
    export LISTEN_PORT="4567"
    export MONGOHQ_URL="mongodb://cs_comments_service:password@localhost:27017/cs_comments_service"
    export NEW_RELIC_LICENSE_KEY="new-relic-license-key"
    export GEM_PATH="/edx/app/forum/.gem"
    export RACK_ENV="development"
    export WORKER_PROCESSES="1"
    export NEW_RELIC_ENABLE="true"
    export SINATRA_ENV="development"
    export SEARCH_SERVER="http://localhost:9200"
    export PATH="/edx/app/forum/cs_comments_service/bin:/edx/app/forum/.rbenv/bin:/edx/app/forum/.rbenv/shims:/edx/app/forum/.gem/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
    export API_KEY="password"
    export HOME="/edx/app/forum"
    export GEM_HOME="/edx/app/forum/.gem"
    export RBENV_ROOT="/edx/app/forum/.rbenv"

    eval "$(rbenv init -)"

.. code-block:: bash

    cat /edx/app/forum/ruby_env 

.. code-block:: bash

    # Ansible managed: /configuration/playbooks/roles/rbenv/templates/ruby_env.j2 modified on 2014-09-18 21:43:35 by root on bf530c094a19

    export GEM_ROOT="/edx/app/forum/.gem"
                     
    export PATH="/edx/app/forum/.rbenv/bin:/edx/app/forum/.rbenv/shims:/edx/app/forum/.gem/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
                     
    export GEM_HOME="/edx/app/forum/.gem"
                     
    export RBENV_ROOT="/edx/app/forum/.rbenv"
                     
    export HOME="/edx/app/forum/.rbenv"
                     
    eval "$(rbenv init -)"

Referencia
-----------

- `discussion/comments service is dev mode <https://groups.google.com/forum/#!topic/edx-code/buClKdsPB9w>`_.
- `Discussion forum 500 error <https://groups.google.com/forum/#!topic/edx-code/euN7mbU8oWU>`_.
- `Fixing forums 401 responses <https://openedx.atlassian.net/wiki/spaces/OXA/pages/159073972/Fixing+forums+401+responses>`_.
