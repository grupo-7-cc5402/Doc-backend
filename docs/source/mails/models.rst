.. _models:

Mails - Modelos
=======



MailQueue
===========

La clase ``MailQueue`` se utiliza para almacenar la cola de envio de los correos.

Modelo
------

.. code-block:: python

    class MailQueue(models.Model):
        # Definiciones de campos aquí

Campos
------

- **subject** (:class:`CharField`): Este campo almacena el asunto del correo. Tiene una longitud máxima de 100 caracteres.

  .. code-block:: python

      subject = models.CharField(max_length=100, null=False)

- **body** (:class:`CharField`): Este campo almacena el cuerpo del correo. Tiene una longitud máxima de 500 caracteres.

  .. code-block:: python

      body = models.CharField(max_length=500, null=False)

- **paciente** (:class:`ForeignKey`): Relación con el modelo ``Paciente``, indicando paciente al que se le enviará el correo.

  .. code-block:: python

      paciente = models.ForeignKey(Paciente, related_name='correo_por_enviar', on_delete=models.CASCADE, null=False)

- **fecha_envio** (:class:`DateTimeField`): Fecha y hora a la que está establecido que se envíe el correo.

  .. code-block:: python

      fecha_envio = models.DateTimeField(auto_now_add=False)

- **horas_anticipacion** (:class:`IntegerField`): Este campo almacena el número de horas de anticipación con que se enviará el correo con respecto a la fecha de la cita. Tiene un valor por defecto de 48 horas.

  .. code-block:: python

      horas_anticipacion = models.IntegerField(default=48)

- **id_evento** (:class:`ForeignKey`): Relación con el modelo ``Evento``, indicando el evento al que corresponde el correo.

  .. code-block:: python

      id_evento = models.ForeignKey(Evento, related_name='mail_queue', on_delete=models.CASCADE)

- **usuario_autor** (:class:`ForeignKey`): Relación con el modelo ``Usuario``, indicando quién creó el correo.

  .. code-block:: python

      usuario_autor = models.ForeignKey(Usuario, related_name='correo_creado', on_delete=models.SET_NULL, null=True)

Uso
---

Este modelo se utiliza para almacenar una lista de correos por enviar.




CorreoHCUCH
===========

La clase ``CorreoHCUCH`` almacena la información de una cuenta de correo electrónico que se utilizará luego para contactar con los pacientes.

Modelo
------

.. code-block:: python

    class CorreoHCUCH(models.Model):
        # Definiciones de campos aquí

LIMITE_ENVIOS
-------------

``LIMITE_ENVIOS`` es una variable que corresponde al límite de envios diarios que se asumirá tienen las cuentas de correo por defecto.

.. code-block:: python

    LIMITE_ENVIOS = 500

Campos
------

- **subject** (:class:`EmailField`): Este campo almacena la dirección del correo.

  .. code-block:: python

      email = models.EmailField(null=False)

- **clave** (:class:`CharField`): Este campo almacena la clave para acceder a la cuenta del correo. Idealmente esta debe ser una _clave de aplicación_.

  .. code-block:: python

      clave = models.CharField(max_length=100, null=False)
