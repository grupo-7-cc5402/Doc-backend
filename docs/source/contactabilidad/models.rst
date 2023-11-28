.. _models:

TipoCampaña
===========

El modelo ``TipoCampaña`` se utiliza para definir los diferentes tipos de campañas, como confirmación o bloqueo.

Modelo
------

.. code-block:: python

    class TipoCampaña(models.Model):
        nombre = models.CharField(max_length=30, null=False, blank=False, unique=True)
        descripcion = models.TextField(blank=True)
        estados_permitidos = models.ManyToManyField(EstadoEvento, related_name='campañas')

        class Meta:
            verbose_name_plural = 'tipos de campaña'

        def __str__(self):
            return self.nombre

        @classmethod
        def get_default_pk(cls):
            # Implementación del método

Campos
-----

- **nombre** (:class:`CharField`): Nombre que describe el tipo de campaña. Es un campo obligatorio y único.

- **descripcion** (:class:`TextField`): Detalle explicativo del tipo de campaña. Este campo es opcional.

- **estados_permitidos** (:class:`ManyToManyField`): Relación de muchos a muchos con el modelo ``EstadoEvento``, que define los estados permitidos para la campaña.

Métodos
-------

- ``__str__(self)``: Devuelve el nombre del tipo de campaña.

- ``get_default_pk(cls)``: Método de clase para obtener o crear un tipo de campaña predeterminado y retornar su clave primaria.

Uso
---

Este modelo es importante para categorizar y administrar diferentes tipos de campañas en el sistema.





Campaña
=======

El modelo ``Campaña`` se utiliza para representar campañas que agrupan eventos y están asignadas a múltiples operadores.

Modelo
------

.. code-block:: python

    class Campaña(models.Model):
        nombre = models.CharField(max_length=100, unique=True)
        usuario_creador = models.ForeignKey(Usuario, related_name='campañas_creadas', on_delete=models.CASCADE)
        fecha_creacion = models.DateTimeField(auto_now_add=True)
        tipo = models.ForeignKey(TipoCampaña, related_name='campañas', on_delete=models.CASCADE, default=TipoCampaña.get_default_pk)
        instrucciones = models.TextField(blank=True)
        estado = models.CharField(max_length=20, choices=ESTADO_CAMPAÑAS)
        operadores = models.ManyToManyField(Usuario, related_name='campañas_operador')

        def __str__(self):
            return self.nombre

Estados de Campañas
-------------------

``ESTADO_CAMPAÑAS`` define los posibles estados de una campaña, como activo, inactivo, finalizado, entre otros.

.. code-block:: python

      ESTADO_CAMPAÑAS = [
          ('activo', 'Activo'),
          ('inactivo', 'Inactivo'),
          ('finalizado', 'Finalizado'),
          ('en_creacion', 'En Creación'),
      ]

Campos
------

- **nombre** (:class:`CharField`): Nombre único de la campaña.

- **usuario_creador** (:class:`ForeignKey`): Usuario que creó la campaña.

- **fecha_creacion** (:class:`DateTimeField`): Fecha y hora de creación de la campaña.

- **tipo** (:class:`ForeignKey`): Tipo de la campaña, vinculado al modelo ``TipoCampaña``.

- **instrucciones** (:class:`TextField`): Mensaje breve para los operadores.

- **estado** (:class:`CharField`): Estado actual de la campaña, basado en ``ESTADO_CAMPAÑAS``.

- **operadores** (:class:`ManyToManyField`): Usuarios asignados a operar la campaña.

Métodos
-------

- ``__str__(self)``: Devuelve el nombre de la campaña.

Uso
---

Este modelo se utiliza para crear y gestionar campañas dentro de la aplicación. Las campañas pueden tener diferentes estados, tipos, y estar asociadas a múltiples operadores.



Evento
======

El modelo ``Evento`` se utiliza para gestionar los diferentes tipos de eventos de contactabilidad dentro del sistema. Cada evento está asociado con una campaña y un paciente, y puede tener diferentes atributos relacionados con el tipo y estado del evento.

Modelo
------

.. code-block:: python

    class Evento(models.Model):
        campaña = models.ForeignKey(Campaña, related_name='eventos', on_delete=models.SET_NULL, null=True)
        id_externa = models.CharField(max_length=100, unique=False, null=True, blank=True)
        paciente = models.ForeignKey(Paciente, related_name='eventos', on_delete=models.CASCADE, null=False)
        centro = models.ForeignKey(CentroAtencion, related_name='eventos', on_delete=models.CASCADE, null=False, default=CentroAtencion.get_default_pk)
        especialidad = models.ForeignKey(Especialidad, related_name='eventos', on_delete=models.CASCADE, null=True)
        subespecialidad = models.ForeignKey(Subespecialidad, related_name='eventos', on_delete=models.CASCADE, null=True)
        procedimiento = models.CharField(max_length=350, null=True)
        profesional = models.ForeignKey(Profesional, related_name='eventos_asignados', on_delete=models.SET_NULL, null=True)
        fecha_programada = models.DateTimeField(null=True, blank=True)
        estado = models.ForeignKey(EstadoEvento, related_name='eventos', on_delete=models.CASCADE, null=False, default=EstadoEvento.get_default_pk)
        descripcion = models.TextField(blank=True)
        operador_asignado = models.ForeignKey(Usuario, related_name='eventos_asignados', on_delete=models.SET_NULL, null=True, blank=True)

        @classmethod
        def get_by_campaña(cls, campaña):
            eventos = cls.objects.filter(campaña=campaña)
            return eventos

Campos
------

- **campaña** (:class:`ForeignKey`): Relación con el modelo ``Campaña``. Un evento está asociado a una campaña específica. Este campo puede ser nulo.

  .. code-block:: python

      campaña = models.ForeignKey(Campaña, related_name='eventos', on_delete=models.SET_NULL, null=True)

- **id_externa** (:class:`CharField`): Identificador externo único para el evento. Este campo es opcional.

  .. code-block:: python

      id_externa = models.CharField(max_length=100, unique=False, null=True, blank=True)

- **paciente** (:class:`ForeignKey`): Relación con el modelo ``Paciente``. Indica el paciente asociado al evento.

  .. code-block:: python

      paciente = models.ForeignKey(Paciente, related_name='eventos', on_delete=models.CASCADE, null=False)

- **centro** (:class:`ForeignKey`): Relación con el modelo ``CentroAtencion``. Define el centro de atención asociado al evento.

  .. code-block:: python

      centro = models.ForeignKey(CentroAtencion, related_name='eventos', on_delete=models.CASCADE, default=CentroAtencion.get_default_pk)

- **especialidad** (:class:`ForeignKey`): Relación con el modelo ``Especialidad``. Especifica la especialidad médica relevante para el evento.

  .. code-block:: python

      especialidad = models.ForeignKey(Especialidad, related_name='eventos', on_delete=models.CASCADE, null=True)

- **subespecialidad** (:class:`ForeignKey`): Relación con el modelo ``Subespecialidad``. Define la subespecialidad médica, si aplica.

  .. code-block:: python

      subespecialidad = models.ForeignKey(Subespecialidad, related_name='eventos', on_delete=models.CASCADE, null=True)

- **procedimiento** (:class:`CharField`): Describe el procedimiento médico asociado con el evento, si corresponde.

  .. code-block:: python

      procedimiento = models.CharField(max_length=350, null=True)

- **profesional** (:class:`ForeignKey`): Relación con el modelo ``Profesional``. Asigna un profesional médico al evento.

  .. code-block:: python

      profesional = models.ForeignKey(Profesional, related_name='eventos_asignados', on_delete=models.SET_NULL, null=True)

- **fecha_programada** (:class:`DateTimeField`): Fecha y hora programadas para el evento. Este campo es opcional.

  .. code-block:: python

      fecha_programada = models.DateTimeField(null=True, blank=True)

- **estado** (:class:`ForeignKey`): Relación con el modelo ``EstadoEvento``. Define el estado actual del evento.

  .. code-block:: python

      estado = models.ForeignKey(EstadoEvento, related_name='eventos', on_delete=models.CASCADE, default=EstadoEvento.get_default_pk)

- **descripcion** (:class:`TextField`): Texto descriptivo adicional sobre el evento. Este campo es opcional.

  .. code-block:: python

      descripcion = models.TextField(blank=True)

- **operador_asignado** (:class:`ForeignKey`): Relación con el modelo ``Usuario``. Asigna un operador al evento.

  .. code-block:: python

      operador_asignado = models.ForeignKey(Usuario, related_name='eventos_asignados', on_delete=models.SET_NULL, null=True, blank=True)

Métodos
-------

- ``get_by_campaña(cls, campaña)``: Método de clase que retorna todos los eventos asociados a una campaña específica.

  .. code-block:: python

      @classmethod
      def get_by_campaña(cls, campaña):
          eventos = cls.objects.filter(campaña=campaña)
          return eventos

Uso
---

El modelo ``Evento`` facilita la organización y seguimiento de eventos de contactabilidad en el sistema, asegurando su correcta asignación y manejo.



EstadoEvento
============

El modelo ``EstadoEvento`` se utiliza para describir los diferentes estados que puede tener un evento (como confirmado, pendiente, etc.). Cada estado proporciona información clave sobre el progreso del evento.

Modelo
------

.. code-block:: python

    class EstadoEvento(models.Model):
        nombre = models.CharField(max_length=30, null=False, blank=False, unique=True)
        descripcion = models.TextField(blank=True)
        es_terminal = models.BooleanField(null=False, default=False)

        class Meta:
            verbose_name_plural = 'estados de evento'

        def __str__(self):
            return self.nombre

        @classmethod
        def get_default_pk(cls):
            tipo_evento, created = cls.objects.get_or_create(
                nombre='Pendiente',
                defaults=dict(descripcion='El paciente aún no ha sido contactado')
            )
            return tipo_evento.pk

Campos
------

- **nombre** (:class:`CharField`): Nombre del estado del evento. Es un campo obligatorio y único. Por ejemplo, "Confirmado". Tiene una longitud máxima de 30 caracteres.

  .. code-block:: python

      nombre = models.CharField(max_length=30, null=False, blank=False, unique=True)

- **descripcion** (:class:`TextField`): Detalle explicativo de lo que significa el estado. Este campo es opcional.

  .. code-block:: python

      descripcion = models.TextField(blank=True)

- **es_terminal** (:class:`BooleanField`): Indica si los eventos en este estado requieren seguimiento adicional. Por defecto, es `False`.

  .. code-block:: python

      es_terminal = models.BooleanField(null=False, default=False)

Meta
----

La clase ``Meta`` dentro de ``EstadoEvento`` define opciones adicionales para el modelo:

- **verbose_name_plural**: Define el nombre plural del modelo como "estados de evento".

  .. code-block:: python

      class Meta:
          verbose_name_plural = 'estados de evento'

Métodos
-------

- ``__str__(self)``: Método para la representación en forma de cadena del objeto. Retorna el nombre del estado.

  .. code-block:: python

      def __str__(self):
          return self.nombre

- ``get_default_pk(cls)``: Método de clase que obtiene o crea un ``EstadoEvento`` predeterminado y retorna su clave primaria (PK). Utilizado para generar un estado de evento por defecto si es necesario.

  .. code-block:: python

      @classmethod
      def get_default_pk(cls):
          # Código del método aquí

Uso
---

El modelo ``EstadoEvento`` es fundamental para categorizar y rastrear el progreso de los eventos en el sistema.


Recordatorio
============

El modelo ``Recordatorio`` se utiliza para representar recordatorios asociados a una campaña. Permite definir cómo y cuándo se enviarán los recordatorios a los destinatarios.

Modelo
------

.. code-block:: python

    class Recordatorio(models.Model):
        campaña = models.ForeignKey(Campaña, related_name='recordatorios', on_delete=models.CASCADE)
        medio_contacto = models.CharField(max_length=20, choices=MEDIO_CONTACTO)
        formato = models.TextField()
        asunto = models.TextField(blank=True, null=True)
        horas_anticipacion = models.IntegerField()
        espera_respuesta = models.BooleanField()

        @classmethod
        def get_by_campaña(cls, campaña):
            recordatorios = cls.objects.filter(campaña=campaña)
            return recordatorios

Opciones de Medio de Contacto
-----------------------------

``MEDIO_CONTACTO`` es una lista de opciones que define los posibles medios de contacto para enviar los recordatorios.

.. code-block:: python

    MEDIO_CONTACTO = [
        ('telefono', 'Telefono'),
        ('correo', 'Correo'),
        ('sms', 'SMS')
    ]

Campos
------

- **campaña** (:class:`ForeignKey`): Relación con el modelo ``Campaña``. Indica a qué campaña pertenece el recordatorio.

  .. code-block:: python

      campaña = models.ForeignKey(Campaña, related_name='recordatorios', on_delete=models.CASCADE)

- **medio_contacto** (:class:`CharField`): Define el medio a través del cual se enviará el recordatorio, como teléfono, correo o SMS.

  .. code-block:: python

      medio_contacto = models.CharField(max_length=20, choices=MEDIO_CONTACTO)

- **formato** (:class:`TextField`): Especifica el formato o plantilla del mensaje del recordatorio.

  .. code-block:: python

      formato = models.TextField()

- **asunto** (:class:`TextField`): El asunto del mensaje de recordatorio, si es aplicable. Este campo es opcional.

  .. code-block:: python

      asunto = models.TextField(blank=True, null=True)

- **horas_anticipacion** (:class:`IntegerField`): Número de horas de anticipación para el envío del recordatorio.

  .. code-block:: python

      horas_anticipacion = models.IntegerField()

- **espera_respuesta** (:class:`BooleanField`): Indica si se espera una respuesta al recordatorio.

  .. code-block:: python

      espera_respuesta = models.BooleanField()

Métodos
-------

- ``get_by_campaña(cls, campaña)``: Método de clase que retorna todos los recordatorios asociados a una campaña específica.

  .. code-block:: python

      @classmethod
      def get_by_campaña(cls, campaña):
          recordatorios = cls.objects.filter(campaña=campaña)
          return recordatorios

Uso
---

El modelo ``Recordatorio`` es clave para la gestión de comunicaciones dentro de una campaña, permitiendo planificar y personalizar los mensajes de recordatorio para los destinatarios.


Paciente
========

El modelo ``Paciente`` se utiliza para gestionar la información básica de los pacientes en el sistema, incluyendo su identificación y nombre.

Modelo
------

.. code-block:: python

    class Paciente(models.Model):
        rut = models.CharField(max_length=20, primary_key=True)
        nombre = models.CharField(max_length=200, null=False)

        def __str__(self):
            return self.nombre

        def get_numero(self):
            numero = Telefono.objects.filter(paciente=self)
            return numero

        def get_correos(self):
            correos = Correo.objects.filter(paciente=self)
            return correos

Campos
------

- **rut** (:class:`CharField`): Identificador único (RUT) del paciente. Este campo es la clave primaria del modelo.

  .. code-block:: python

      rut = models.CharField(max_length=20, primary_key=True)

- **nombre** (:class:`CharField`): Nombre completo del paciente.

  .. code-block:: python

      nombre = models.CharField(max_length=200, null=False)

Métodos
-------

- ``__str__(self)``: Método para la representación en cadena del objeto, que devuelve el nombre del paciente.

  .. code-block:: python

      def __str__(self):
          return self.nombre

- ``get_numero(self)``: Método que retorna los números de teléfono asociados con el paciente.

  .. code-block:: python

      def get_numero(self):
          numero = Telefono.objects.filter(paciente=self)
          return numero

- ``get_correos(self)``: Método que devuelve los correos electrónicos asociados con el paciente.

  .. code-block:: python

      def get_correos(self):
          correos = Correo.objects.filter(paciente=self)
          return correos

Uso
---

El modelo ``Paciente`` es fundamental para identificar y mantener la información de contacto de los pacientes.

Telefono
========

El modelo ``Telefono`` se utiliza para almacenar y gestionar los números de teléfono asociados a los pacientes.

Modelo
------

.. code-block:: python

    class Telefono(models.Model):
        paciente = models.ForeignKey(Paciente, related_name='telefonos', on_delete=models.CASCADE)
        numero = models.CharField(max_length=25)
        descripcion = models.CharField(max_length=100)

Campos
------

- **paciente** (:class:`ForeignKey`): Relación con el modelo ``Paciente``. Indica el paciente al que pertenece el número de teléfono.

  .. code-block:: python

      paciente = models.ForeignKey(Paciente, related_name='telefonos', on_delete=models.CASCADE)

- **numero** (:class:`CharField`): Número de teléfono del paciente. Almacena hasta 25 caracteres.

  .. code-block:: python

      numero = models.CharField(max_length=25)

- **descripcion** (:class:`CharField`): Descripción o etiqueta para el número de teléfono. Útil para identificar el tipo de teléfono (ej. móvil, casa, trabajo).

  .. code-block:: python

      descripcion = models.CharField(max_length=100)

Uso
---

Este modelo es crucial para mantener información de contacto telefónico de los pacientes, permitiendo una gestión efectiva y organizada de sus números de teléfono.


Correo
======

El modelo ``Correo`` se encarga de almacenar y gestionar las direcciones de correo electrónico de los pacientes.

Modelo
------

.. code-block:: python

    class Correo(models.Model):
        paciente = models.ForeignKey(Paciente, related_name='correos', on_delete=models.CASCADE)
        email = models.EmailField()
        descripcion = models.CharField(max_length=100)

Campos
------

- **paciente** (:class:`ForeignKey`): Relación con el modelo ``Paciente``. Vincula cada dirección de correo electrónico con un paciente específico.

  .. code-block:: python

      paciente = models.ForeignKey(Paciente, related_name='correos', on_delete=models.CASCADE)

- **email** (:class:`EmailField`): Dirección de correo electrónico del paciente.

  .. code-block:: python

      email = models.EmailField()

- **descripcion** (:class:`CharField`): Descripción o etiqueta para la dirección de correo electrónico. Puede ser utilizado para indicar la naturaleza del correo electrónico (ej. personal, trabajo).

  .. code-block:: python

      descripcion = models.CharField(max_length=100)

Uso
---

Este modelo es importante para gestionar las comunicaciones por correo electrónico con los pacientes, asegurando que la información de contacto sea precisa y esté bien organizada.


TipoProfesional
===============

El modelo ``TipoProfesional`` se utiliza para definir los distintos tipos de profesionales de la salud, como médicos o enfermeros.

Modelo
------

.. code-block:: python

    class TipoProfesional(models.Model):
        nombre = models.CharField(max_length=30, null=False, blank=False, unique=True)

        class Meta:
            verbose_name_plural = 'tipos de profesional'

        def __str__(self):
            return self.nombre

        @classmethod
        def get_default_pk(cls):
            tipo_profesional, created = cls.objects.get_or_create(nombre='Médico')
            return tipo_profesional.pk

Campos
------

- **nombre** (:class:`CharField`): Nombre que describe el tipo de profesional. Es un campo obligatorio y único.

  .. code-block:: python

      nombre = models.CharField(max_length=30, null=False, blank=False, unique=True)

Meta
----

- **verbose_name_plural**: Define el nombre plural del modelo como "tipos de profesional".

Métodos
-------

- ``__str__(self)``: Método para la representación en cadena del objeto. Devuelve el nombre del tipo de profesional.

- ``get_default_pk(cls)``: Método de clase que retorna la clave primaria (PK) de un tipo de profesional predeterminado, creándolo si no existe.

Uso
---

Este modelo es importante para categorizar a los profesionales de la salud dentro de la aplicación y facilitar la gestión de sus roles y responsabilidades.


Profesional
===========

El modelo ``Profesional`` representa a los profesionales de la salud, como médicos o enfermeros, con sus respectivos tipos.

Modelo
------

.. code-block:: python

    class Profesional(models.Model):
        nombre = models.CharField(max_length=200, null=False)
        tipo = models.ForeignKey(TipoProfesional, related_name='profesionales', on_delete=models.CASCADE, default=TipoProfesional.get_default_pk)

        class Meta:
            verbose_name_plural = 'profesionales'

        def __str__(self):
            return self.nombre

Campos
------

- **nombre** (:class:`CharField`): Nombre completo del profesional. Es un campo obligatorio.

  .. code-block:: python

      nombre = models.CharField(max_length=200, null=False)

- **tipo** (:class:`ForeignKey`): Relación con el modelo ``TipoProfesional``. Define el tipo de profesional.

  .. code-block:: python

      tipo = models.ForeignKey(TipoProfesional, related_name='profesionales', on_delete=models.CASCADE, default=TipoProfesional.get_default_pk)

Meta
----

- **verbose_name_plural**: Define el nombre plural del modelo como "profesionales".

Métodos
-------

- ``__str__(self)``: Método para la representación en cadena del objeto. Devuelve el nombre del profesional.

Uso
---

Este modelo es crucial para gestionar la información de los profesionales de la salud, incluyendo su especialización y datos de contacto.


Usuario
=======

El modelo ``Usuario`` extiende el modelo de usuario predeterminado de Django, `AbstractUser`, añadiendo campos adicionales para gestionar información específica de los usuarios.

Modelo
------

.. code-block:: python

    class Usuario(AbstractUser):
        rut = models.CharField(max_length=20)
        nombre_hcuch = models.CharField(max_length=50, null=True, blank=True)

Campos Adicionales
------------------

- **rut** (:class:`CharField`): Identificador único (RUT) del usuario. Es un campo obligatorio.

  .. code-block:: python

      rut = models.CharField(max_length=20)

- **nombre_hcuch** (:class:`CharField`): Nombre opcional del usuario en el contexto específico del HCUCH (Hospital Clínico de la Universidad de Chile). Este campo es opcional y puede estar en blanco.

  .. code-block:: python

      nombre_hcuch = models.CharField(max_length=50, null=True, blank=True)

Uso
---

El modelo ``Usuario`` se utiliza para gestionar los usuarios del sistema, incluyendo autenticación y autorización, así como almacenar información adicional específica del usuario que no está cubierta por el modelo `AbstractUser` de Django.

Herencia de AbstractUser
------------------------

Al extender `AbstractUser`, el modelo `Usuario` hereda todos los campos y métodos de la clase base de Django para la gestión de usuarios. Esto incluye campos como `username`, `email`, `first_name`, `last_name`, y métodos para manejar contraseñas, grupos y permisos.

Personalización
---------------

- Al utilizar `Usuario` en lugar del modelo de usuario predeterminado de Django, se pueden añadir campos adicionales y métodos específicos para las necesidades del sistema.
- Es importante configurar `AUTH_USER_MODEL` en el archivo de configuración de Django (`settings.py`) para indicar que se use `Usuario` como el modelo de usuario personalizado.

  .. code-block:: python

      AUTH_USER_MODEL = 'contactabilidad.Usuario'

Especialidad
============

El modelo ``Especialidad`` representa las distintas especialidades médicas.

Modelo
------

.. code-block:: python

    class Especialidad(models.Model):
        nombre = models.CharField(max_length=200, unique=True)

        class Meta:
            verbose_name_plural = 'especialidades'

        def __str__(self):
            return self.nombre

        @classmethod
        def validar_tipo(cls, nombre):
            # Implementación del método validar_tipo

Campo
-----

- **nombre** (:class:`CharField`): Nombre de la especialidad. Es un campo único.

  .. code-block:: python

      nombre = models.CharField(max_length=200, unique=True)

Métodos
-------

- ``__str__(self)``: Método para la representación en cadena del objeto. Devuelve el nombre de la especialidad.

- ``validar_tipo(cls, nombre)``: Método de clase que valida si una especialidad existe en la base de datos, corrigiendo tildes y mayúsculas.

  .. code-block:: python

      @classmethod
      def validar_tipo(cls, nombre):
          # Implementación del método

Uso
---

El modelo ``Especialidad`` se utiliza para categorizar y almacenar información sobre las diferentes especialidades médicas en el sistema.


Subespecialidad
===============

El modelo ``Subespecialidad`` se utiliza para definir subespecialidades dentro de una especialidad médica.

Modelo
------

.. code-block:: python

    class Subespecialidad(models.Model):
        nombre = models.CharField(max_length=200, unique=True)
        especialidad = models.ForeignKey(Especialidad, related_name='sub_especialidades', on_delete=models.CASCADE)

        class Meta:
            verbose_name_plural = 'subespecialidades'

        def __str__(self):
            return self.nombre

Campos
------

- **nombre** (:class:`CharField`): Nombre de la subespecialidad. Es un campo único.

  .. code-block:: python

      nombre = models.CharField(max_length=200, unique=True)

- **especialidad** (:class:`ForeignKey`): Relación con el modelo ``Especialidad``. Indica a qué especialidad pertenece la subespecialidad.

  .. code-block:: python

      especialidad = models.ForeignKey(Especialidad, related_name='sub_especialidades', on_delete=models.CASCADE)

Uso
---

El modelo ``Subespecialidad`` permite una clasificación más detallada de las áreas de práctica médica, asociándolas con su especialidad correspondiente.


CentroAtencion
==============

El modelo ``CentroAtencion`` representa los centros de atención médica en el sistema.

Modelo
------

.. code-block:: python

    class CentroAtencion(models.Model):
        nombre = models.CharField(max_length=200, unique=True)

        class Meta:
            verbose_name_plural = 'centros de atencion'

        def __str__(self):
            return self.nombre

        @classmethod
        def get_default_pk(cls):
            # Implementación del método

        @classmethod
        def validar_tipo(cls, nombre):
            # Implementación del método

Campo
-----

- **nombre** (:class:`CharField`): Nombre del centro de atención. Es un campo único.

Métodos
-------

- ``__str__(self)``: Devuelve el nombre del centro de atención.

- ``get_default_pk(cls)``: Retorna la clave primaria de un centro de atención predeterminado.

- ``validar_tipo(cls, nombre)``: Valida si un nombre de centro de atención existe en la base de datos, corrigiendo tildes y mayúsculas.

Uso
---

Este modelo es utilizado para almacenar y gestionar información sobre diferentes centros de atención médica, facilitando su identificación y manejo en el sistema.


LogEvento
========

El modelo ``LogEvento`` se utiliza para registrar distintos tipos de actividades o acciones asociadas a un evento específico en el sistema.

Modelo
------

.. code-block:: python

    class LogEvento(models.Model):
        id_evento = models.ForeignKey(Evento, related_name='logs', on_delete=models.CASCADE)
        fecha = models.DateTimeField(auto_now_add=True)
        tipo = models.CharField(max_length=100, choices=LOG_TIPO)
        usuario_autor = models.ForeignKey(Usuario, related_name='logs_creados', on_delete=models.SET_NULL, null=True)
        contenido = models.TextField(blank=True)
        data_adicional = JSONField(blank=True, null=True)

        class Meta:
            verbose_name_plural = 'logs de eventos'

Tipos de Log
------------

``LOG_TIPO`` es una lista de tuplas que define los posibles tipos de registros de log, como cambios de estado, envíos de SMS, respuestas a llamadas, entre otros.

.. code-block:: python

      LOG_TIPO = [
          ('cambio_estado', 'Cambio de Estado'),
          ('envio_sms', 'Envío SMS'),
          ('respuesta_sms', 'Respuesta SMS'),
          ('envio_correo', 'Envío Correo'),
          ('respuesta_correo', 'Respuesta Correo'),
          ('llamada', 'Llamada Paciente'),
          ('respuesta_llamada', 'Respuesta Paciente'),
          ('observacion', 'Registro Observación'),
          ('asignacion_evento', 'Asignación de Paciente')
      ]

Campos
------

- **id_evento** (:class:`ForeignKey`): Relación con el modelo ``Evento``. Indica a qué evento pertenece el log.

  .. code-block:: python

      id_evento = models.ForeignKey(Evento, related_name='logs', on_delete=models.CASCADE)

- **fecha** (:class:`DateTimeField`): Fecha y hora de creación del log. Se establece automáticamente.

  .. code-block:: python

      fecha = models.DateTimeField(auto_now_add=True)

- **tipo** (:class:`CharField`): Tipo de actividad registrada en el log. Debe ser uno de los definidos en ``LOG_TIPO``.

  .. code-block:: python

      tipo = models.CharField(max_length=100, choices=LOG_TIPO)

- **usuario_autor** (:class:`ForeignKey`): Usuario que creó el log. Puede ser nulo si el usuario no está disponible.

  .. code-block:: python

      usuario_autor = models.ForeignKey(Usuario, related_name='logs_creados', on_delete=models.SET_NULL, null=True)

- **contenido** (:class:`TextField`): Detalles adicionales o información relevante sobre la actividad registrada.

  .. code-block:: python

      contenido = models.TextField(blank=True)

- **data_adicional** (:class:`JSONField`): Campo opcional para almacenar datos adicionales en formato JSON.

  .. code-block:: python

      data_adicional = JSONField(blank=True, null=True)

Uso
---

Este modelo es fundamental para llevar un registro detallado de todas las acciones relacionadas con los eventos, permitiendo un seguimiento exhaustivo y una auditoría efectiva de las actividades realizadas.

