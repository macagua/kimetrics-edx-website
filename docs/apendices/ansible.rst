.. _soporte-ansible-label:


Soporte a Ansible
=================


General
-------

- Los archivos **YAML**: todos los archivos **yaml** deben usar 2 sangrías de espacio y terminar 
  con ``.yml``.

- Las variables: use la sintaxis de la variable **jinja** sobre la sintaxis de la variable 
  obsoleta. ``{{ var }}`` no ``$var``.

- Use espacios alrededor de los nombres de las variables jinja. ``{{ var }}`` no ``{{var}}``.

- Las variables que son específicas del entorno y que deben sobrescribirse deben aparecer 
  en **MAYÚSCULAS**.

- Las variables que son internas para el rol deben ser **minúsculas**.

- Prefija todas las variables definidas en un rol con el nombre del rol. Ejemplo: ``EDXAPP_FOO``.

- Mantenga los roles independientes: las funciones deben evitar incluir tareas de otros 
  roles cuando sea posible.

- Los **Plays** o reproducciones no deberían hacer más que incluir una lista de roles, 
  excepto cuando se requieren ``pre_tasks`` y ``post_tasks`` (para gestionar un balanceador 
  de carga, por ejemplo).

- Los **Plays/Playbooks** que se aplican a la comunidad en general deben copiarse en la 
  carpeta ``configuration/playbooks``.

- Los **Plays/Playbooks** que se aplican solo a una organización específica (``edx-east``, 
  ``edx-west``) deben copiarse en un subdirectorio en ``configuration/playbooks``.

- Manejadores: no use manejadores. Si necesita reiniciar una aplicación cuando se ejecutan 
  tareas específicas, solo agregue una tarea para hacerlo al final del playbook. Si es necesario, 
  se puede omitir con tags (consulte ``Tags`` del ciclo de vida del rol)

- Separadores: use guiones bajos (por ejemplo, ``my_role``) y no guiones (``my-role``).

- Rutas: al definir rutas, no incluya barras diagonales (por ejemplo, ``my_path: /foo`` no 
  ``my_path: /foo/``. Al concatenar rutas, siga la misma convención (por ejemplo, 
  ``{{ my_path }}/bar`` no ``{{my_path}}bar``)

- Etiquetar tareas usando las etiquetas descritas en las ``Tags`` del ciclo de vida del rol.


Roles
-----


Variables de roles
^^^^^^^^^^^^^^^^^^

- Rol ``common``: contiene tareas que se aplican a todos los roles que son específicos de edX.

- Rol ``common_vars``: contiene variables que se aplican a todos los roles que son específicos de edX.

- Variables de roles: las variables específicas de un rol se deben definir en ``/vars/main.yml``. 
  Todas las variables deben tener un prefijo con el nombre del rol.

- Valores predeterminados de los roles: las variables predeterminadas deben configurar un rol 
  para instalar edx de modo que todos los servicios puedan ejecutarse en un único servidor.

- Las variables que son específicas del entorno y que deben ser reemplazadas deben estar en 
  mayúsculas.

- Cada rol debe tener un conjunto estándar de directorios de roles, ejemplo que incluye un 
  ``virtualenv`` de ``python`` y ``ruby``:

::

    edxapp_data_dir: "{{ COMMON_DATA_DIR }}/edxapp"
    edxapp_app_dir: "{{ COMMON_APP_DIR }}/edxapp"
    edxapp_log_dir: "{{ COMMON_LOG_DIR }}/edxapp"
    edxapp_venvs_dir: "{{ edxapp_app_dir }}/venvs"
    edxapp_venv_dir: "{{ edxapp_venvs_dir }}/edxapp"
    edxapp_venv_bin: "{{ edxapp_venv_dir }}/bin"
    edxapp_rbenv_dir: "{{ edxapp_app_dir }}"
    edxapp_rbenv_root: "{{ edxapp_rbenv_dir }}/.rbenv"
    edxapp_rbenv_shims: "{{ edxapp_rbenv_root }}/shims"
    edxapp_rbenv_bin: "{{ edxapp_rbenv_root }}/bin"
    edxapp_gem_root: "{{ edxapp_rbenv_dir }}/.gem"
    edxapp_gem_bin: "{{ edxapp_gem_root }}/bin"


.. code-block:: bash

    # sudo -H -u edx-ansible bash
    edx-ansible@862b36282858:/$ 

.. code-block:: bash

    $ /edx/app/edx_ansible/update
    ERROR: You must specify a repo and commit

        Usage: update <repo> <version>
                -v        add verbosity to edx_ansible run
                -h        this

        <repo> - must be one of edx-platform, xqueue, cs_comments_service, xserver, ease, edx-ora, configuration, read-only-certificate-code edx-analytics-data-api
        <version> - can be a commit or tag


.. code-block:: bash

    $ source /edx/app/edx_ansible/venvs/edx_ansible/bin/activate
    (edx_ansible)edx-ansible@862b36282858:/$ 


.. code-block:: bash

    $ /edx/bin/ansible-playbook /edx/app/edx_ansible/edx_ansible/playbooks/edx_sandbox.yml -i /edx/app/edx_ansible/edx_ansible/playbooks/inventory.ini -vvvv

    PLAY [Configure instance(s)] ************************************************** 

    GATHERING FACTS *************************************************************** 
    <127.0.0.1> ESTABLISH CONNECTION FOR USER: edx-ansible
    <127.0.0.1> REMOTE_MODULE setup
    <127.0.0.1> EXEC ['ssh', '-C', '-tt', '-vvv', '-o', 'ControlMaster=auto', '-o', 'ControlPersist=60s', '-o', 'ControlPath=/edx/app/edx_ansible/.ansible/cp/ansible-ssh-%h-%p-%r', '-o', 'KbdInteractiveAuthentication=no', '-o', 'PreferredAuthentications=gssapi-with-mic,gssapi-keyex,hostbased,publickey', '-o', 'PasswordAuthentication=no', '-o', 'ConnectTimeout=10', '127.0.0.1', "/bin/sh -c 'mkdir -p $HOME/.ansible/tmp/ansible-tmp-1522850254.32-24461405739716 && chmod a+rx $HOME/.ansible/tmp/ansible-tmp-1522850254.32-24461405739716 && echo $HOME/.ansible/tmp/ansible-tmp-1522850254.32-24461405739716'"]
    fatal: [127.0.0.1] => SSH encountered an unknown error. The output was:
    OpenSSH_6.6.1, OpenSSL 1.0.1f 6 Jan 2014
    debug1: Reading configuration data /etc/ssh/ssh_config
    debug1: /etc/ssh/ssh_config line 19: Applying options for *
    debug1: auto-mux: Trying existing master
    debug1: Control socket "/edx/app/edx_ansible/.ansible/cp/ansible-ssh-127.0.0.1-22-edx-ansible" does not exist
    debug2: ssh_connect: needpriv 0
    debug1: Connecting to 127.0.0.1 [127.0.0.1] port 22.
    debug2: fd 3 setting O_NONBLOCK
    debug1: fd 3 clearing O_NONBLOCK
    debug1: Connection established.
    debug3: timeout: 10000 ms remain after connect
    debug1: identity file /edx/app/edx_ansible/.ssh/id_rsa type -1
    debug1: identity file /edx/app/edx_ansible/.ssh/id_rsa-cert type -1
    debug1: identity file /edx/app/edx_ansible/.ssh/id_dsa type -1
    debug1: identity file /edx/app/edx_ansible/.ssh/id_dsa-cert type -1
    debug1: identity file /edx/app/edx_ansible/.ssh/id_ecdsa type -1
    debug1: identity file /edx/app/edx_ansible/.ssh/id_ecdsa-cert type -1
    debug1: identity file /edx/app/edx_ansible/.ssh/id_ed25519 type -1
    debug1: identity file /edx/app/edx_ansible/.ssh/id_ed25519-cert type -1
    debug1: Enabling compatibility mode for protocol 2.0
    debug1: Local version string SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2
    debug1: Remote protocol version 2.0, remote software version OpenSSH_6.6.1p1 Ubuntu-2ubuntu2
    debug1: match: OpenSSH_6.6.1p1 Ubuntu-2ubuntu2 pat OpenSSH_6.6.1* compat 0x04000000
    debug2: fd 3 setting O_NONBLOCK
    debug3: load_hostkeys: loading entries for host "127.0.0.1" from file "/edx/app/edx_ansible/.ssh/known_hosts"
    debug3: load_hostkeys: found key type ECDSA in file /edx/app/edx_ansible/.ssh/known_hosts:1
    debug3: load_hostkeys: loaded 1 keys
    debug3: order_hostkeyalgs: prefer hostkeyalgs: ecdsa-sha2-nistp256-cert-v01@openssh.com,ecdsa-sha2-nistp384-cert-v01@openssh.com,ecdsa-sha2-nistp521-cert-v01@openssh.com,ecdsa-sha2-nistp256,ecdsa-sha2-nistp384,ecdsa-sha2-nistp521
    debug1: SSH2_MSG_KEXINIT sent
    debug1: SSH2_MSG_KEXINIT received
    debug2: kex_parse_kexinit: curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1,diffie-hellman-group1-sha1
    debug2: kex_parse_kexinit: ecdsa-sha2-nistp256-cert-v01@openssh.com,ecdsa-sha2-nistp384-cert-v01@openssh.com,ecdsa-sha2-nistp521-cert-v01@openssh.com,ecdsa-sha2-nistp256,ecdsa-sha2-nistp384,ecdsa-sha2-nistp521,ssh-ed25519-cert-v01@openssh.com,ssh-rsa-cert-v01@openssh.com,ssh-dss-cert-v01@openssh.com,ssh-rsa-cert-v00@openssh.com,ssh-dss-cert-v00@openssh.com,ssh-ed25519,ssh-rsa,ssh-dss
    debug2: kex_parse_kexinit: aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-gcm@openssh.com,aes256-gcm@openssh.com,chacha20-poly1305@openssh.com,aes128-cbc,3des-cbc,blowfish-cbc,cast128-cbc,aes192-cbc,aes256-cbc,arcfour,rijndael-cbc@lysator.liu.se
    debug2: kex_parse_kexinit: aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-gcm@openssh.com,aes256-gcm@openssh.com,chacha20-poly1305@openssh.com,aes128-cbc,3des-cbc,blowfish-cbc,cast128-cbc,aes192-cbc,aes256-cbc,arcfour,rijndael-cbc@lysator.liu.se
    debug2: kex_parse_kexinit: hmac-md5-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-ripemd160-etm@openssh.com,hmac-sha1-96-etm@openssh.com,hmac-md5-96-etm@openssh.com,hmac-md5,hmac-sha1,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-ripemd160,hmac-ripemd160@openssh.com,hmac-sha1-96,hmac-md5-96
    debug2: kex_parse_kexinit: hmac-md5-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-ripemd160-etm@openssh.com,hmac-sha1-96-etm@openssh.com,hmac-md5-96-etm@openssh.com,hmac-md5,hmac-sha1,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-ripemd160,hmac-ripemd160@openssh.com,hmac-sha1-96,hmac-md5-96
    debug2: kex_parse_kexinit: zlib@openssh.com,zlib,none
    debug2: kex_parse_kexinit: zlib@openssh.com,zlib,none
    debug2: kex_parse_kexinit: 
    debug2: kex_parse_kexinit: 
    debug2: kex_parse_kexinit: first_kex_follows 0 
    debug2: kex_parse_kexinit: reserved 0 
    debug2: kex_parse_kexinit: curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1,diffie-hellman-group1-sha1
    debug2: kex_parse_kexinit: ssh-rsa,ssh-dss,ecdsa-sha2-nistp256
    debug2: kex_parse_kexinit: aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-gcm@openssh.com,aes256-gcm@openssh.com,chacha20-poly1305@openssh.com,aes128-cbc,3des-cbc,blowfish-cbc,cast128-cbc,aes192-cbc,aes256-cbc,arcfour,rijndael-cbc@lysator.liu.se
    debug2: kex_parse_kexinit: aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-gcm@openssh.com,aes256-gcm@openssh.com,chacha20-poly1305@openssh.com,aes128-cbc,3des-cbc,blowfish-cbc,cast128-cbc,aes192-cbc,aes256-cbc,arcfour,rijndael-cbc@lysator.liu.se
    debug2: kex_parse_kexinit: hmac-md5-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-ripemd160-etm@openssh.com,hmac-sha1-96-etm@openssh.com,hmac-md5-96-etm@openssh.com,hmac-md5,hmac-sha1,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-ripemd160,hmac-ripemd160@openssh.com,hmac-sha1-96,hmac-md5-96
    debug2: kex_parse_kexinit: hmac-md5-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-ripemd160-etm@openssh.com,hmac-sha1-96-etm@openssh.com,hmac-md5-96-etm@openssh.com,hmac-md5,hmac-sha1,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-ripemd160,hmac-ripemd160@openssh.com,hmac-sha1-96,hmac-md5-96
    debug2: kex_parse_kexinit: none,zlib@openssh.com
    debug2: kex_parse_kexinit: none,zlib@openssh.com
    debug2: kex_parse_kexinit: 
    debug2: kex_parse_kexinit: 
    debug2: kex_parse_kexinit: first_kex_follows 0 
    debug2: kex_parse_kexinit: reserved 0 
    debug2: mac_setup: setup hmac-md5-etm@openssh.com
    debug1: kex: server->client aes128-ctr hmac-md5-etm@openssh.com zlib@openssh.com
    debug2: mac_setup: setup hmac-md5-etm@openssh.com
    debug1: kex: client->server aes128-ctr hmac-md5-etm@openssh.com zlib@openssh.com
    debug1: sending SSH2_MSG_KEX_ECDH_INIT
    debug1: expecting SSH2_MSG_KEX_ECDH_REPLY
    debug1: Server host key: ECDSA 06:a3:46:f2:52:12:e7:d3:6a:42:14:46:17:3b:19:6c
    debug3: load_hostkeys: loading entries for host "127.0.0.1" from file "/edx/app/edx_ansible/.ssh/known_hosts"
    debug3: load_hostkeys: found key type ECDSA in file /edx/app/edx_ansible/.ssh/known_hosts:1
    debug3: load_hostkeys: loaded 1 keys
    debug1: Host '127.0.0.1' is known and matches the ECDSA host key.
    debug1: Found key in /edx/app/edx_ansible/.ssh/known_hosts:1
    debug1: ssh_ecdsa_verify: signature correct
    debug2: kex_derive_keys
    debug2: set_newkeys: mode 1
    debug1: SSH2_MSG_NEWKEYS sent
    debug1: expecting SSH2_MSG_NEWKEYS
    debug2: set_newkeys: mode 0
    debug1: SSH2_MSG_NEWKEYS received
    debug1: Roaming not allowed by server
    debug1: SSH2_MSG_SERVICE_REQUEST sent
    debug2: service_accept: ssh-userauth
    debug1: SSH2_MSG_SERVICE_ACCEPT received
    debug2: key: /edx/app/edx_ansible/.ssh/id_rsa ((nil)),
    debug2: key: /edx/app/edx_ansible/.ssh/id_dsa ((nil)),
    debug2: key: /edx/app/edx_ansible/.ssh/id_ecdsa ((nil)),
    debug2: key: /edx/app/edx_ansible/.ssh/id_ed25519 ((nil)),
    debug1: Authentications that can continue: publickey
    debug3: start over, passed a different list publickey
    debug3: preferred gssapi-with-mic,gssapi-keyex,hostbased,publickey
    debug3: authmethod_lookup publickey
    debug3: remaining preferred: ,gssapi-keyex,hostbased,publickey
    debug3: authmethod_is_enabled publickey
    debug1: Next authentication method: publickey
    debug1: Trying private key: /edx/app/edx_ansible/.ssh/id_rsa
    debug3: no such identity: /edx/app/edx_ansible/.ssh/id_rsa: No such file or directory
    debug1: Trying private key: /edx/app/edx_ansible/.ssh/id_dsa
    debug3: no such identity: /edx/app/edx_ansible/.ssh/id_dsa: No such file or directory
    debug1: Trying private key: /edx/app/edx_ansible/.ssh/id_ecdsa
    debug3: no such identity: /edx/app/edx_ansible/.ssh/id_ecdsa: No such file or directory
    debug1: Trying private key: /edx/app/edx_ansible/.ssh/id_ed25519
    debug3: no such identity: /edx/app/edx_ansible/.ssh/id_ed25519: No such file or directory
    debug2: we did not send a packet, disable method
    debug1: No more authentication methods to try.
    Permission denied (publickey).


    TASK: [user | debug var=user_info] ******************************************** 
    FATAL: no hosts matched or all hosts have already failed -- aborting


    PLAY RECAP ******************************************************************** 
               to retry, use: --limit @/edx/app/edx_ansible/edx_sandbox.retry

    127.0.0.1                  : ok=0    changed=0    unreachable=1    failed=0   


.. code-block:: bash

    $ /edx/bin/ansible-playbook /edx/app/edx_ansible/edx_ansible/playbooks/edx_sandbox.yml -i /edx/app/edx_ansible/edx_ansible/playbooks/inventory.ini -c local -vvvv

    PLAY [Configure instance(s)] ************************************************** 

    GATHERING FACTS *************************************************************** 
    <localhost> REMOTE_MODULE setup
    <localhost> EXEC ['/bin/sh', '-c', 'mkdir -p $HOME/.ansible/tmp/ansible-tmp-1522863587.39-267625052613775 && chmod a+rx $HOME/.ansible/tmp/ansible-tmp-1522863587.39-267625052613775 && echo $HOME/.ansible/tmp/ansible-tmp-1522863587.39-267625052613775']
    <localhost> PUT /tmp/tmpzaEyg6 TO /edx/app/edx_ansible/.ansible/tmp/ansible-tmp-1522863587.39-267625052613775/setup
    <localhost> EXEC /bin/sh -c 'sudo -k && sudo -H -S -p "[sudo via ansible, key=zqemyoctwviqpskmibdzilrsntmlpjqw] password: " -u root /bin/sh -c '"'"'echo SUDO-SUCCESS-zqemyoctwviqpskmibdzilrsntmlpjqw; /usr/bin/python /edx/app/edx_ansible/.ansible/tmp/ansible-tmp-1522863587.39-267625052613775/setup; rm -rf /edx/app/edx_ansible/.ansible/tmp/ansible-tmp-1522863587.39-267625052613775/ >/dev/null 2>&1'"'"''
    failed: [localhost] => {"failed": true, "parsed": false}
    invalid output was: [sudo via ansible, key=zqemyoctwviqpskmibdzilrsntmlpjqw] password: 


    TASK: [user | debug var=user_info] ******************************************** 
    FATAL: no hosts matched or all hosts have already failed -- aborting


    PLAY RECAP ******************************************************************** 
               to retry, use: --limit @/edx/app/edx_ansible/edx_sandbox.retry

    localhost                  : ok=0    changed=0    unreachable=0    failed=1

::

    (edx_ansible)$ /edx/app/edx_ansible/venvs/edx_ansible/bin/ansible-playbook

    (edx_ansible)$ /edx/bin/ansible-playbook

    (edx_ansible)$ /edx/bin/ansible-playbook /edx/app/edx_ansible/edx_ansible/playbooks/edx_sandbox.yml -i /edx/app/edx_ansible/edx_ansible/playbooks/inventory.ini

    (edx_ansible)$ /edx/bin/ansible-playbook /edx/app/edx_ansible/edx_ansible/playbooks/edx_sandbox.yml -i /edx/app/edx_ansible/edx_ansible/playbooks/ec2.py

    (edx_ansible)$ /edx/bin/ansible-playbook /edx/app/edx_ansible/edx_ansible/playbooks/edx_sandbox.yml -i "127.0.0.1,"

    (edx_ansible)$ /edx/bin/ansible-playbook /edx/app/edx_ansible/edx_ansible/playbooks/edx_sandbox.yml -i localhost,

    (edx_ansible)edx-ansible@862b36282858:/$ /edx/bin/ansible-playbook /edx/app/edx_ansible/edx_ansible/playbooks/edx_sandbox.yml
    ERROR: Unable to find an inventory file, specify one with -i ?

    (edx_ansible)edx-ansible@862b36282858:/$ /edx/bin/ansible-playbook -i /edx/app/edx_ansible/edx_ansible/playbooks/inventory.ini /edx/app/edx_ansible/edx_ansible/playbooks/edx_sandbox.yml 
    ERROR: parse error: playbooks must be formatted as a YAML list, got <type 'dict'>

    (edx_ansible)edx-ansible@862b36282858:/$ cat /edx/app/edx_ansible/edx_ansible/playbooks/inventory.ini
    [localhost]
    127.0.0.1


.. - `https://www.linkedin.com/in/feanil <https://www.linkedin.com/in/feanil>`_.

Referencia
----------

- `Feanil Patel, edX - Configuration Primer (10/13/2015) <https://www.youtube.com/watch?v=uOg33HUbZVM>`_.
- `Configuration primer <https://es.slideshare.net/feanil/configuration-primer>`_.
- `edx hosting architecture <https://open.edx.org/videos/edx-hosting-architecture>`_.
- `How edX uses Ansible <https://speakerdeck.com/jarv/how-edx-uses-ansible>`_.
- `edX deployment - AWS <https://speakerdeck.com/jarv/edx-deployment-aws>`_.
- `edX on the Raspberry Pi - A Portable Classroom <https://open.edx.org/blog/edx-raspberry-pi-–-portable-classroom>`_.
- `Ansible Stuff <https://openedx.atlassian.net/wiki/spaces/OpenOPS/pages/19661228/Ansible+Stuff>`_.
- `Ansible Code Conventions <https://openedx.atlassian.net/wiki/spaces/OpenOPS/pages/26837527/Ansible+Code+Conventions>`_.
- `Docker Images <https://openedx.atlassian.net/wiki/spaces/OpenDev/pages/159992239/Docker+Images>`_.
- `Rapid Ansible Iteration with Docker <https://openedx.atlassian.net/wiki/spaces/OpenOPS/pages/46793826/Rapid+Ansible+Iteration+with+Docker>`_.
- `overriding default variables using a custom yml file <https://groups.google.com/forum/#!topic/openedx-ops/tAJjTyn35-o>`_.
