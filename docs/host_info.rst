Información del Host
=====================

Información del host para portal edX en el servidor de Amazon:

Puertos expuestos
-----------------

- "80/tcp", Aplicación LMS.

- "18010/tcp", Aplicación CMS.

- "18040/tcp", Servicio Xqueue. https://open.edx.org/xblocks

- "18060/tcp", .

- "18080/tcp", Aplicación Forum.

- "18130/tcp", Aplicación Ecommerce.

- "18381/tcp", Aplicación Discovery.

- "18090/tcp", Aplicación Certs.

- "8099/tcp", edx-release.

..
            "ExposedPorts": {
                "18010/tcp": {},
                "18040/tcp": {},
                "18060/tcp": {},
                "18080/tcp": {},
                "80/tcp": {}
            },

IP y dominios
-------------

- **IP del host:** 54.152.79.203.

- **Dominio URL:** http://ec2-54-152-79-203.compute-1.amazonaws.com

Una vez que el stack esta ejecutándose, usted puede acceder a la aplicación LMS y Studio 
en estas siguientes direcciones URLS:

**Open edX LMS**
    - **Dominio URL:** http://ec2-54-152-79-203.compute-1.amazonaws.com:8000/
      La ultima versión del fullstack tiene un demo del curso cargado previamente 
      y cuentas de usuarios dummy, usted puede iniciar sesión de usuario como:

      - **usuario:** staff@example.com y **contraseña:** edx.

      - **usuario:** verified@example.com y **contraseña:** edx.

      - **usuario:** audit@example.com y **contraseña:** edx.

      - **usuario:** honor@example.com y **contraseña:** edx.

**Django Admin**
    - **Dominio URL:** http://ec2-54-152-79-203.compute-1.amazonaws.com:8000/admin/
    
    - **usuario:** TODO y **contraseña:** TODO.

**Open edX Studio**
    - **Dominio URL:** http://ec2-54-152-79-203.compute-1.amazonaws.com:18010/


Referencia
----------

- `Running Fullstack <https://openedx.atlassian.net/wiki/spaces/OpenOPS/pages/60227777/Running+Fullstack>`_.
