.. _traducciones-label:

Traducción de Open edX
=======================

Internationalización (i18n) and Localización (l10n) para la plataforma edX

1) Ejecutar el shell del contenedor creado:

.. code-block:: bash

    $ sudo docker exec -it my-edx-full bash

2) Cambiar al entorno edxapp:

Iniciar sesión de usuario **edxapp**:

.. code-block:: bash

    $ sudo -H -u edxapp bash

Activar entorno virtual

.. code-block:: bash

    $ source /edx/app/edxapp/venvs/edxapp/bin/activate

..
    $ source ~/edx-ginkgo.2-1/apps/edx/venvs/edxapp/bin/activate

Acceder al directorio de la plataforma:

.. code-block:: bash

    $ cd /edx/app/edxapp/edx-platform

..
    $ cd ~/edx-ginkgo.2-1/apps/edx/edx-platform

3) Configurar en su archivo conexión con Transifex

.. code-block:: bash

    $ vi ~/.transifexrc

Ajuste la siguiente configuración:

.. code-block:: cfg

    [https://www.transifex.com]
    hostname = https://www.transifex.com
    username = USUARIO
    password = CONTRASENA
    token = 

Token dejar en blanco. Tiene que tener permisos para el proyecto (**edx-platform**) 
https://www.transifex.com/projects/p/edx-platform/ (es libre a inscribirse y unirse 
a este proyecto como un traductor).

Para más ayuda con la configuración Transifex, vea la documentación del cliente 
Transifex: http://docs.transifex.com/developer/client/setup#configuration

4) Asegúrese que todos los idiomas que usted desea descargar este presentes y 
descomentados en el archivo :file:`conf/locale/config.yaml`. Por ejemplo, si 
usted desea descargar el Español (Latino América), asegúrese que en su archivo 
:file:`config.yaml` luzca como este:

.. code-block:: bash

    $ vi /edx/app/edxapp/edx-platform/conf/locale/config.yaml

..
    $ vi ~/edx-ginkgo.2-1/apps/edx/edx-platform/conf/locale/config.yaml

.. code-block:: yaml

     locales:
        - es_419  # Spanish (Latin America)

5) Configure la variable ``LANGUAGE_CODE`` en su archivo :file:`lms/envs/common.py`. 

.. code-block:: bash

    $ vi /edx/app/edxapp/edx-platform/lms/envs/common.py

..
    $ vi ~/edx-ginkgo.2-1/apps/edx/edx-platform/lms/envs/common.py

Ajuste la siguiente configuración:

.. code-block:: python

    # Locale/Internationalization
    TIME_ZONE = 'America/Mexico'  # http://en.wikipedia.org/wiki/List_of_tz_zones_by_name
    LANGUAGE_CODE = 'es-419'  # http://www.i18nguy.com/unicode/language-identifiers.html
    
    # Sourced from http://www.localeplanet.com/icu/ and wikipedia
    LANGUAGES = ( ('es-419', u'Español (Latinoamérica)'), )  # Spanish (Latin America)
    """
    LANGUAGES = (
    ...
    """

Lo mismo para el archivo :file:`cms/envs/common.py`:

.. code-block:: bash

    $ vi /edx/app/edxapp/edx-platform/cms/envs/common.py

..
    $ vi ~/edx-ginkgo.2-1/apps/edx/edx-platform/cms/envs/common.py

Ajuste la siguiente configuración:

.. code-block:: python

    # Locale/Internationalization
    TIME_ZONE = lms.envs.common.TIME_ZONE  # http://en.wikipedia.org/wiki/List_of_tz_zones_by_name
    LANGUAGE_CODE = lms.envs.common.LANGUAGE_CODE  # http://www.i18nguy.com/unicode/language-identifiers.html

    LANGUAGES = lms.envs.common.LANGUAGES
    USE_I18N = True

O, para propósitos de desarrollo, cree un archivo dev llamado :file:`dev_LANGCODE.py` 
- ej. :file:`dev_es.py` - en los directorios :file:`lms/envs` y :file:`cms/envs`, con el siguiente:

.. code-block:: python

    from .dev import *

    USE_I18N = True
    LANGUAGES = ( ('es-419', u'Español (Latinoamérica)'), )  # Spanish (Latin America)
    TIME_ZONE = 'America/Mexico'
    LANGUAGE_CODE = 'es-419'


Los idiomas necesitan ser especificados con los códigos Django, entonces un código 
eso es especifico en Transifex como el "de_DE" debe ser especificado como "de-de" 
en esa configuración files. 

**Consejo:** Consulte https://groups.google.com/forum/#!topic/openedx-translation/vrOpMKzA0kU

Salir del usuario **edxapp**:

.. code-block:: bash

    $ exit

6) Configure la variable ``EDXAPP_LANGUAGE_CODE`` en sus archivos de configuración: 

https://github.com/edx/configuration/blob/master/playbooks/roles/edxapp/defaults/main.yml#L329

Iniciar sesión de usuario **edx-ansible**:

.. code-block:: bash

    $ sudo -H -u edx-ansible bash

Editar el :file:`main.yml`:

.. code-block:: bash

    $ vi /edx/app/edx_ansible/edx_ansible/playbooks/roles/edxapp/defaults/main.yml

Ajuste la siguiente configuración:

.. code-block:: python

    #EDXAPP_LANG: 'en_US.UTF-8'
    EDXAPP_LANG: 'es_419.UTF-8'
    #EDXAPP_LANGUAGE_CODE : 'en'
    EDXAPP_LANGUAGE_CODE : 'es-419'
    #EDXAPP_TIME_ZONE: 'America/New_York'
    EDXAPP_TIME_ZONE: 'America/Mexico'

Salir del usuario **edx-ansible**:

.. code-block:: bash

    $ exit

6) Ejecute el siguiente comando en su directorio :file:`edx-platform` 
con su virtualenv de ``edx-platform``:

Iniciar sesión de usuario **edxapp**:

.. code-block:: bash

    $ sudo -H -u edxapp bash

Normalmente con los siguientes comando:

.. code-block:: bash

    $ cd /edx/app/edxapp && source edxapp_env && cd edx-platform

..
    $ cd ~/edx-ginkgo.2-1/apps/edx && source scripts/edxapp_env && cd ./edx-platform

Asegúrese de que en este punto esté configurado su repositorio de git, 
ya que el script intentará confirmar automáticamente los cambios.

.. code-block:: bash

    $ paver i18n_robot_pull


Si falla con el siguiente error:

::

    $ paver i18n_robot_pull
    ---> pavelib.i18n.i18n_robot_pull
    ---> pavelib.i18n.i18n_transifex_pull
    ---> pavelib.i18n.i18n_validate_transifex_config
    i18n_tool transifex pull
    Pulling languages from transifex...
    New translations found for the following languages: el, gu, fi_FI, vi, ca, it_IT, et_EE, sv, nl_NL, ar, en_US, cs, pt_PT, gl, id, bn_IN, ru, sr, kk_KZ, nb, tr, lv, zh-Hans, lt_LT, th, es_AR, es_ES, ro, is, pl, ta, bg_BG, fr, bg, bn_BD, ms, pt_BR, sl, hr, zh_TW, hu, ko_KR, hi, tr_TR, bs, de_DE, es_MX, cy, da, sw_KE, ja, fr_CA, he, ka, te, fa_IR, zh_CN, ml, sq, mn, ko, ja_JP, ur, zh_HK, de, es_PE, eu_ES, sk, uk, hy_AM, km_KH, es_EC, es_419
    Pulling new translations for resource edx-platform.django-partial (source: conf/locale/en/LC_MESSAGES/django-partial.po)
     -> el: conf/locale/el/LC_MESSAGES/django-partial.po
    Exception: Forbidden
    Traceback (most recent call last):
      File "/edx/app/edxapp/venvs/edxapp/bin/i18n_tool", line 9, in <module>
        load_entry_point('i18n-tools==0.1', 'console_scripts', 'i18n_tool')()
      File "/edx/app/edxapp/venvs/edxapp/src/i18n-tools/i18n/main.py", line 38, in main
        return module.main()
      File "/edx/app/edxapp/venvs/edxapp/src/i18n-tools/i18n/__init__.py", line 36, in __call__
        self.run(args)
      File "/edx/app/edxapp/venvs/edxapp/src/i18n-tools/i18n/transifex.py", line 107, in run
        pull()
      File "/edx/app/edxapp/venvs/edxapp/src/i18n-tools/i18n/transifex.py", line 21, in pull
        execute('tx pull --mode=reviewed --all')
      File "/edx/app/edxapp/venvs/edxapp/src/i18n-tools/i18n/execute.py", line 21, in execute
        sp.check_call(command, cwd=working_directory, stderr=stderr, shell=True)
      File "/usr/lib/python2.7/subprocess.py", line 540, in check_call
        raise CalledProcessError(retcode, cmd)
    subprocess.CalledProcessError: Command 'tx pull --mode=reviewed --all' returned non-zero exit status 1


    Captured Task Output:
    ---------------------

    ---> pavelib.i18n.i18n_robot_pull
    ---> pavelib.i18n.i18n_transifex_pull
    ---> pavelib.i18n.i18n_validate_transifex_config
    i18n_tool transifex pull

    Build failed running pavelib.i18n.i18n_robot_pull: Subprocess return code: 1

**SOLUCIÓN**: Actualice desde la versión ``transifex-client 0.10`` a la versión ``transifex-client 0.13.1`` 

- https://pypi.python.org/pypi/transifex-client/0.10
- https://pypi.python.org/pypi/transifex-client/0.13.1

Tenga en cuenta que este comando extraerá traducciones revisadas para todos 
los idiomas que se enumeran en el archivo :file:`conf/locale/config.yaml`. 
Para desplegar solo algunos idiomas, edite el archivo :file:`conf/locale/config.yaml` 
de forma adecuada.

Para obtener traducciones no revisadas junto con traducciones revisadas, 
edite el siguiente modulo Python :py:mod:`/edx/app/edxapp/venvs/edxapp/src/i18n-tools/i18n/transifex.py`. 
Particularmente, en la linea: ``execute('tx pull --mode=reviewed -l ' + lang)`` debería 
ser cambiada a: ``execute('tx pull -l' + lang)``

ALTERNATIVA:

Según el vídeo `Open edX install <https://www.youtube.com/watch?v=Dy2lxgvkdQk>`_

.. code-block:: bash

    $ tx pull -l es_419

    $ paver i18n_generate

    $ paver update_assets lms --settings=devstack
    $ paver devstack --fast lms
    $ paver devstack --fast studio

    $ paver update_assets lms --settings=aws
    $ paver update_assets cms --settings=aws
    $ paver aws --fast lms
    $ paver aws --fast studio

Referencia
----------

- `Open edX install <https://www.youtube.com/watch?v=Dy2lxgvkdQk>`_.
- `Multi language support on Open edX <http://learning.perpetualny.com/blog/multi-language-support-on-open-edx>`_.
- `Internationalization and localization <https://github.com/edx/edx-platform/wiki/Internationalization-and-localization>`_.
- `Internationalization from edx developer guide <http://edx.readthedocs.io/projects/edx-developer-guide/en/latest/internationalization/i18n.html>`_.
