.. _models:

Modelos
=======

Los modelos en este proyecto representan las entidades clave en la base de datos.

TipoCampaña
===========

La clase ``TipoCampaña`` se utiliza para describir los diferentes tipos de campañas dentro de la aplicación. Estos tipos podrían incluir, por ejemplo, campañas de confirmación o bloqueo.

Modelo
------

.. code-block:: python

    class TipoCampaña(models.Model):
        # Definiciones de campos aquí

Campos
------

- **nombre** (:class:`CharField`): Este campo almacena el nombre del tipo de campaña. Es un campo obligatorio y único, que no puede estar en blanco. Por ejemplo, podría ser "Lista de espera FONASA". Tiene una longitud máxima de 30 caracteres.

  .. code-block:: python

      nombre = models.CharField(max_length=30, null=False, blank=False, unique=True)

- **descripcion** (:class:`TextField`): Proporciona detalles adicionales sobre lo que implica el tipo de campaña. Este campo es opcional y puede dejarse en blanco.

  .. code-block:: python

      descripcion = models.TextField(blank=True)

- **estados_permitidos** (:class:`ManyToManyField`): Una relación de muchos a muchos con el modelo ``EstadoEvento``, que define los estados permitidos para la campaña.

  .. code-block:: python

      estados_permitidos = models.ManyToManyField(EstadoEvento, related_name='campañas')

Meta
----

La clase ``Meta`` dentro de ``TipoCampaña`` define opciones adicionales para el modelo:

- **verbose_name_plural**: Define el nombre plural del modelo como "tipos de campaña".

  .. code-block:: python

      class Meta:
          verbose_name_plural = 'tipos de campaña'

Métodos
-------

- ``__str__(self)``: Método para la representación en forma de cadena del objeto. Retorna el nombre de la campaña.

  .. code-block:: python

      def __str__(self):
          return self.nombre

- ``get_default_pk(cls)``: Método de clase que obtiene o crea un ``TipoCampaña`` predeterminado y retorna su clave primaria (PK). Utilizado para generar un tipo de campaña por defecto si es necesario.

  .. code-block:: python

      @classmethod
      def get_default_pk(cls):
          # Código del método aquí

Uso
---

Este modelo es utilizado para definir distintos tipos de campañas en la aplicación.




Campaña
======

El modelo ``Campaña`` representa una campaña individual dentro de la aplicación, que puede estar compuesta por varios eventos y asignada a múltiples operadores.

Modelo
------

.. code-block:: python

    class Campaña(models.Model):
        # Definiciones de campos aquí

Estados de Campañas
-------------------

``ESTADO_CAMPAÑAS`` es una lista de tuplas que define los posibles estados de una campaña. Cada estado tiene un identificador y una descripción.

.. code-block:: python

    ESTADO_CAMPAÑAS = [
        ('activo', 'Activo'),
        ('inactivo', 'Inactivo'),
        ('finalizado', 'Finalizado'),
        ('en_creacion', 'En Creación'),
    ]

Campos
------

- **nombre** (:class:`CharField`): El nombre único de la campaña. Este campo es obligatorio y tiene una longitud máxima de 100 caracteres.

  .. code-block:: python

      nombre = models.CharField(max_length=100, unique=True)

- **usuario_creador** (:class:`ForeignKey`): Relación con el modelo ``Usuario``, indicando quién creó la campaña.

  .. code-block:: python

      usuario_creador = models.ForeignKey(Usuario, related_name='campañas_creadas', on_delete=models.CASCADE)

- **fecha_creacion** (:class:`DateTimeField`): Fecha y hora de creación de la campaña, establecida automáticamente al añadir una nueva campaña.

  .. code-block:: python

      fecha_creacion = models.DateTimeField(auto_now_add=True)

- **tipo** (:class:`ForeignKey`): Relación con el modelo ``TipoCampaña``, que define el tipo de la campaña.

  .. code-block:: python

      tipo = models.ForeignKey(TipoCampaña, related_name='campañas', on_delete=models.CASCADE, default=TipoCampaña.get_default_pk)

- **instrucciones** (:class:`TextField`): Mensaje breve que se muestra a los operadores al realizar una llamada. Este campo es opcional.

  .. code-block:: python

      instrucciones = models.TextField(blank=True)

- **estado** (:class:`CharField`): El estado actual de la campaña, que debe ser uno de los definidos en ``ESTADO_CAMPAÑAS``.

  .. code-block:: python

      estado = models.CharField(max_length=20, choices=ESTADO_CAMPAÑAS)

- **operadores** (:class:`ManyToManyField`): Relación de muchos a muchos con el modelo ``Usuario``, indicando los operadores asignados a la campaña.

  .. code-block:: python

      operadores = models.ManyToManyField(Usuario, related_name='campañas_operador')

Métodos
-------

- ``__str__(self)``: Método para la representación en forma de cadena del objeto. Retorna el nombre de la campaña.

  .. code-block:: python

      def __str__(self):
          return self.nombre

Uso
---

Este modelo se utiliza para crear y gestionar campañas dentro de la aplicación. Las campañas pueden tener diferentes estados, tipos, y estar asociadas a múltiples operadores.
