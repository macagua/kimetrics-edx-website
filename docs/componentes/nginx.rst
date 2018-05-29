.. _servidor-nginx-label:


Servidor Web nginx
==================

Ubicar los recursos del servidor Web nginx, ejecutando el siguiente comando:

.. code-block:: bash

    find / -name nginx
    /edx/app/edx_ansible/edx_ansible/playbooks/roles/nginx
    /edx/app/edx_ansible/edx_ansible/playbooks/roles/nginx/templates/edx/app/nginx
    /edx/app/edx_ansible/edx_ansible/playbooks/roles/nginx/templates/etc/nginx
    /edx/app/edx_ansible/edx_ansible/playbooks/roles/jenkins_master/templates/etc/nginx
    /edx/app/nginx
    /edx/var/nginx
    /edx/var/log/nginx
    /var/lib/nginx
    /var/log/nginx
    /etc/default/nginx
    /etc/logrotate.d/nginx
    /etc/init.d/nginx
    /etc/nginx
    /etc/service/nginx
    /etc/ufw/applications.d/nginx
    /usr/sbin/nginx
    /usr/share/doc/nginx
    /usr/share/nginx


..
  .. code-block:: bash

      root@862b36282858:/# ls -lhap /edx/var/nginx/
      total 8.0K
      drwxr-xr-x 2 www-data root 4.0K Sep 25  2014 ./
      drwxr-xr-x 1 root     root 4.0K Oct  3  2014 ../


Listar archivo de registro de errores y acceso, ejecutando el siguiente comando:

.. code-block:: bash

    root@862b36282858:/# ls -lhap /edx/var/log/nginx/
    total 196K
    drwxr-xr-x 1 www-data www-data 4.0K Oct  3  2014 ./
    drwxr-xr-x 1 syslog   syslog   4.0K Oct  3  2014 ../
    -rw-r--r-- 1 www-data root     178K Apr  1 21:56 access.log
    -rw-r--r-- 1 www-data root     1.8K Mar 27 17:09 error.log


Consutar el archivo de registro de acceso, ejecutando el siguiente comando:

.. code-block:: bash

    tail -f /edx/var/log/nginx/access.log


Consutar el archivo de registro de errores, ejecutando el siguiente comando:

.. code-block:: bash

    tail -f /edx/var/log/nginx/error.log

Listar configuraciones de virtual host disponible, ejecutando el siguiente comando:

.. code-block:: bash

    # ls -lhap /edx/app/nginx/sites-available/
    total 24K
    drwxr-xr-x 1 root www-data 4.0K Oct  2  2014 ./
    drwxr-xr-x 1 root www-data 4.0K Oct  2  2014 ../
    -rw-r----- 1 root www-data 2.6K Oct  2  2014 cms
    -rw------- 1 root root      209 Sep 25  2014 edx-release
    -rw-r----- 1 root www-data  706 Sep 25  2014 forum
    -rw-r----- 1 root www-data 2.6K Oct  2  2014 lms


Listar configuraciones de virtual host habilitados, ejecutando el siguiente comando:

.. code-block:: bash

    # ls -lhap /edx/app/nginx/sites-enabled/
    total 8.0K
    drwxr-xr-x 1 root www-data 4.0K Oct  2  2014 ./
    drwxr-xr-x 1 root www-data 4.0K Oct  2  2014 ../
    lrwxrwxrwx 1 root root       34 Sep 25  2014 cms -> /edx/app/nginx/sites-available/cms
    lrwxrwxrwx 1 root root       42 Sep 25  2014 edx-release -> /edx/app/nginx/sites-available/edx-release
    lrwxrwxrwx 1 root root       36 Sep 25  2014 forum -> /edx/app/nginx/sites-available/forum
    lrwxrwxrwx 1 root root       34 Sep 25  2014 lms -> /edx/app/nginx/sites-available/lms


Consulte la configuración del virtual host para el componente CMS, ejecutando el siguiente comando:

.. code-block:: bash

    cat /edx/app/nginx/sites-available/cms
    upstream cms-backend {
                server 127.0.0.1:8010 fail_timeout=0;
        }

    server {
      # CMS configuration file for nginx, templated by ansible
          
        listen 18010 ;
      
      server_name ~^((stage|prod)-)?studio.*;

      access_log /edx/var/log/nginx/access.log;
      error_log /edx/var/log/nginx/error.log error;

      # CS184 requires uploads of up to 4MB for submitting screenshots. 
      # CMS requires larger value for course assest, values provided 
      # via hiera.
      client_max_body_size 100M;
      
      rewrite ^(.*)/favicon.ico$ /static/images/favicon.ico last;


      location @proxy_to_cms_app {
            proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-Port $server_port;
        proxy_set_header X-Forwarded-For $remote_addr;
            proxy_set_header Host $http_host;

        proxy_redirect off;
        proxy_pass http://cms-backend;
      }

      location / {
            try_files $uri @proxy_to_cms_app;
      }

      # No basic auth security on the github_service_hook url, so that github can use it for cms
      location /github_service_hook {
        try_files $uri @proxy_to_cms_app;
      }

      # No basic auth security on the heartbeat url, so that ELB can use it
      location /heartbeat {
        try_files $uri @proxy_to_cms_app;
      }

          location ~ ^/static/(?P<file>.*) {
        root /edx/var/edxapp;
        try_files /staticfiles/$file /course_static/$file =404;

        # return a 403 for static files that shouldn't be
        # in the staticfiles directory
        location ~ ^/static/(?:.*)(?:\.xml|\.json|README.TXT) {
            return 403;
        }

        # http://www.red-team-design.com/firefox-doesnt-allow-cross-domain-fonts-by-default
        location ~ "/static/(?P<collected>.*\.[0-9a-f]{12}\.(eot|otf|ttf|woff))" {
            expires max;
            add_header Access-Control-Allow-Origin *;
            try_files /staticfiles/$collected /course_static/$collected =404;
        }

        # Set django-pipelined files to maximum cache time
        location ~ "/static/(?P<collected>.*\.[0-9a-f]{12}\..*)" {
            expires max;
            # Without this try_files, files that have been run through
            # django-pipeline return 404s
            try_files /staticfiles/$collected /course_static/$collected =404;
        }

        # Set django-pipelined files for studio to maximum cache time
        location ~ "/static/(?P<collected>[0-9a-f]{7}/.*)" {
            expires max;

            # Without this try_files, files that have been run through
            # django-pipeline return 404s
            try_files /staticfiles/$collected /course_static/$collected =404;
        }

        # Expire other static files immediately (there should be very few / none of these)
        expires epoch;
      }

    }


Consulte la configuración del virtual host para el componente LMS, ejecutando el siguiente comando:

.. code-block:: bash

    # cat /edx/app/nginx/sites-available/lms
    upstream lms-backend {
                server 127.0.0.1:8000 fail_timeout=0;
        }

    server {
      # LMS configuration file for nginx, templated by ansible

        listen 80 default;
      
      access_log /edx/var/log/nginx/access.log;
      error_log /edx/var/log/nginx/error.log error;

      # CS184 requires uploads of up to 4MB for submitting screenshots.
      # CMS requires larger value for course assest, values provided
      # via hiera.
      client_max_body_size 4M;

      rewrite ^(.*)/favicon.ico$ /static/images/favicon.ico last;

      location @proxy_to_lms_app {
            proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-Port $server_port;
        proxy_set_header X-Forwarded-For $remote_addr;
            proxy_set_header Host $http_host;

        proxy_redirect off;
        proxy_pass http://lms-backend;
      }

      location / {
            try_files $uri @proxy_to_lms_app;
      }

      # No basic auth security on the github_service_hook url, so that github can use it for cms
      location /github_service_hook {
        try_files $uri @proxy_to_lms_app;
      }

      # No basic auth security on the heartbeat url, so that ELB can use it
      location /heartbeat {
        try_files $uri @proxy_to_lms_app;
      }

          location ~ ^/static/(?P<file>.*) {
        root /edx/var/edxapp;
        try_files /staticfiles/$file /course_static/$file =404;

        # return a 403 for static files that shouldn't be
        # in the staticfiles directory
        location ~ ^/static/(?:.*)(?:\.xml|\.json|README.TXT) {
            return 403;
        }

        # http://www.red-team-design.com/firefox-doesnt-allow-cross-domain-fonts-by-default
        location ~ "/static/(?P<collected>.*\.[0-9a-f]{12}\.(eot|otf|ttf|woff))" {
            expires max;
            add_header Access-Control-Allow-Origin *;
            try_files /staticfiles/$collected /course_static/$collected =404;
        }

        # Set django-pipelined files to maximum cache time
        location ~ "/static/(?P<collected>.*\.[0-9a-f]{12}\..*)" {
            expires max;
            # Without this try_files, files that have been run through
            # django-pipeline return 404s
            try_files /staticfiles/$collected /course_static/$collected =404;
        }

        # Set django-pipelined files for studio to maximum cache time
        location ~ "/static/(?P<collected>[0-9a-f]{7}/.*)" {
            expires max;

            # Without this try_files, files that have been run through
            # django-pipeline return 404s
            try_files /staticfiles/$collected /course_static/$collected =404;
        }

        # Expire other static files immediately (there should be very few / none of these)
        expires epoch;
      }


    }

.. code-block:: bash

    # cat /edx/app/nginx/sites-available/edx-release 

.. code-block:: bash

    
    server {
        listen 8099 default_server;

        location = /versions.html {
            alias /edx/app/nginx/versions.html;
        }

        location = /versions.json {
            alias /edx/app/nginx/versions.json;
        }
    }


Consulte la configuración del virtual host para el componente Forum, ejecutando el siguiente comando:

.. code-block:: bash

    # cat /edx/app/nginx/sites-available/forum

.. code-block:: cfg

    #
    # Ansible managed: /configuration/playbooks/roles/nginx/templates/edx/app/nginx/sites-available/forum.j2 modified on 2014-09-18 21:43:35 by root on bf530c094a19
    #



    upstream forum_app_server {
      server unix:/edx/var/forum/forum.sock fail_timeout=0;
    }

    server {

      server_name forum.*;
      listen 18080 ;
      client_max_body_size 1M;
      keepalive_timeout 5;

      location / {
        try_files $uri @proxy_to_app;
      }

      
    location @proxy_to_app {
            proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-Port $server_port;
        proxy_set_header X-Forwarded-For $remote_addr;
            proxy_set_header Host $http_host;

        proxy_redirect off;
        proxy_pass http://forum_app_server;
      }
    }


.. code-block:: bash

    # ls -lhap /edx/app/edx_ansible/edx_ansible/playbooks/roles/nginx/templates/edx/app/nginx/sites-available/
    total 76K
    drwxr-xr-x 2 edx-ansible edx-ansible 4.0K Sep 25  2014 ./
    drwxr-xr-x 3 edx-ansible edx-ansible 4.0K Sep 25  2014 ../
    -rw-r--r-- 1 edx-ansible edx-ansible  986 Sep 25  2014 analytics-api.j2
    -rw-r--r-- 1 edx-ansible edx-ansible  321 Sep 25  2014 basic-auth.j2
    -rw-r--r-- 1 edx-ansible edx-ansible  186 Sep 25  2014 certs.j2
    -rw-r--r-- 1 edx-ansible edx-ansible 2.5K Sep 25  2014 cms.j2
    -rw-r--r-- 1 edx-ansible edx-ansible  464 Sep 25  2014 devpi.j2
    -rw-r--r-- 1 edx-ansible edx-ansible  877 Sep 25  2014 discern.j2
    -rw-r--r-- 1 edx-ansible edx-ansible  209 Sep 25  2014 edx-release.j2
    -rw-r--r-- 1 edx-ansible edx-ansible 1.5K Sep 25  2014 forum.j2
    -rw-r--r-- 1 edx-ansible edx-ansible  638 Sep 25  2014 gh_mirror.j2
    -rw-r--r-- 1 edx-ansible edx-ansible 2.7K Sep 25  2014 lms-preview.j2
    -rw-r--r-- 1 edx-ansible edx-ansible 2.5K Sep 25  2014 lms.j2
    -rw-r--r-- 1 edx-ansible edx-ansible  693 Sep 25  2014 nginx_redirect.j2
    -rw-r--r-- 1 edx-ansible edx-ansible 1.2K Sep 25  2014 ora.j2
    -rw-r--r-- 1 edx-ansible edx-ansible  146 Sep 25  2014 robots.j2
    -rw-r--r-- 1 edx-ansible edx-ansible 1.4K Sep 25  2014 static-files.j2
    -rw-r--r-- 1 edx-ansible edx-ansible  805 Sep 25  2014 xqueue.j2
    -rw-r--r-- 1 edx-ansible edx-ansible 1.1K Sep 25  2014 xserver.j2


Referencia
----------

- `Configuring SSL for NGINX <https://github.com/CDOT-EDX/ProductionStackDocs/wiki/Configuring-SSL-for-NGINX>`_.
