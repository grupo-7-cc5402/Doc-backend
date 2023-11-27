.. _models:

Modelos
=======

Los modelos en este proyecto representan las entidades clave en la base de datos.

TipoCampaña
-----------

El modelo "TipoCampaña" describe los diferentes tipos de campañas...

.. code-block:: python

    class TipoCampaña(models.Model):
        nombre = models.CharField(...)
        descripcion = models.TextField(...)
        ...

    Métodos y Funciones
    -------------------

    - "get_default_pk": Este método de clase ...

Campaña
-------

El modelo "Campaña" representa una campaña individual y contiene...

.. code-block:: python

    class Campaña(models.Model):
        nombre = models.CharField(...)
        usuario_creador = models.ForeignKey(...)
        ...

    Detalles de Atributos
    ---------------------

    - "nombre": El nombre de la campaña.
    - "usuario_creador": Usuario que creó la campaña.
    - "fecha_creacion": Fecha y hora de creación.
    - ...

