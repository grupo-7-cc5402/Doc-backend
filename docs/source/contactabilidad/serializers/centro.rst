CentroAtencionSerializer
========================

.. code-block:: python

    class CentroAtencionSerializer(serializers.HyperlinkedModelSerializer):
        class Meta:
            model = CentroAtencion
            fields = ['url', 'id', 'nombre']

Descripción General
-------------------

`CentroAtencionSerializer` se utiliza para serializar los datos del modelo `CentroAtencion`. Este modelo representa los centros de atención médica en el sistema, proporcionando una manera de gestionar y referenciar estos centros en la aplicación.

Estructura y Comportamiento
---------------------------

- **url**: URL del recurso del centro de atención.
- **id**: Identificador único para el centro de atención.
- **nombre**: Nombre del centro de atención.

