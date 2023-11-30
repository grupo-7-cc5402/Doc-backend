TipoProfesionalSerializer
=========================

.. code-block:: python

    class TipoProfesionalSerializer(serializers.HyperlinkedModelSerializer):
        class Meta:
            model = TipoProfesional
            fields = ['url', 'id', 'nombre']

Descripción General
-------------------

`TipoProfesionalSerializer` se utiliza para la representación y serialización del modelo `TipoProfesional` en Django REST Framework. Facilita la transformación de datos entre JSON y objetos del modelo para diferentes tipos de profesionales.

Campos
------

- **url**: URL del recurso del tipo de profesional.
- **id**: Identificador único del tipo de profesional.
- **nombre**: Nombre del tipo de profesional.

ProfesionalSerializer
=====================

.. code-block:: python

    class ProfesionalSerializer(serializers.HyperlinkedModelSerializer):
        tipo = TipoProfesionalSerializer(read_only=True)

        class Meta:
            model = Profesional
            fields = ['url', 'nombre', 'tipo']

Descripción General
-------------------

`ProfesionalSerializer` se emplea para la representación del modelo `Profesional` en Django REST Framework. Permite serializar la información de profesionales de la salud, incluyendo su tipo.

Estructura y Comportamiento
---------------------------

- **url**: URL del recurso del profesional.
- **nombre**: Nombre completo del profesional.
- **tipo**: Tipo del profesional, representado por `TipoProfesionalSerializer`.

