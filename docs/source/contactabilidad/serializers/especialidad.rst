EspecialidadSerializer
======================

.. code-block:: python

    class EspecialidadSerializer(serializers.HyperlinkedModelSerializer):
        class Meta:
            model = Especialidad
            fields = ['url', 'id', 'nombre']

Descripción General
-------------------

`EspecialidadSerializer` se emplea para serializar los datos del modelo `Especialidad`. Este modelo representa las distintas especialidades médicas dentro del sistema, facilitando su manejo y categorización.

Estructura y Comportamiento
---------------------------

- **url**: URL del recurso de la especialidad.
- **id**: Identificador único para la especialidad.
- **nombre**: Nombre de la especialidad médica.

SubespecialidadSerializer
=========================

.. code-block:: python

    class SubespecialidadSerializer(serializers.HyperlinkedModelSerializer):
        class Meta:
            model = Subespecialidad
            fields = ['url', 'id', 'nombre', 'especialidad']

Descripción General
-------------------

`SubespecialidadSerializer` se utiliza para serializar los datos del modelo `Subespecialidad`. Este modelo es empleado para detallar las subespecialidades dentro de una especialidad médica, proporcionando una clasificación más específica.

Campos
------

- **url**: URL del recurso de la subespecialidad.
- **id**: Identificador único de la subespecialidad.
- **nombre**: Nombre de la subespecialidad.
- **especialidad**: Campo que relaciona la subespecialidad con su especialidad principal.

