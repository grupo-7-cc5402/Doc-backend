TelefonoSerializer
==================

.. code-block:: python

    class TelefonoSerializer(serializers.HyperlinkedModelSerializer):
        class Meta:
            model = Telefono
            fields = ['paciente', 'descripcion', 'numero']

Descripción General
-------------------

`TelefonoSerializer` se emplea para la representación de datos del modelo `Telefono` en Django REST Framework. Facilita la transformación de datos entre JSON y objetos del modelo para la gestión de números telefónicos de pacientes.

Campos
------

- **paciente**: Referencia al paciente asociado con el teléfono.
- **descripcion**: Descripción del número de teléfono.
- **numero**: Número telefónico del paciente.

CorreoSerializer
================

.. code-block:: python

    class CorreoSerializer(serializers.HyperlinkedModelSerializer):
        class Meta:
            model = Correo
            fields = ['paciente', 'descripcion', 'email']

Descripción General
-------------------

`CorreoSerializer` se utiliza en Django REST Framework para la representación del modelo `Correo`. Permite convertir datos de correos electrónicos entre formatos JSON y objetos de modelo para pacientes.

Campos
------

- **paciente**: Vinculación con el paciente.
- **descripcion**: Etiqueta de la dirección de correo.
- **email**: Correo electrónico del paciente.

PacienteSerializer
==================

.. code-block:: python

    class PacienteSerializer(serializers.HyperlinkedModelSerializer):
        telefonos = TelefonoSerializer(many=True, read_only=True)
        correos = CorreoSerializer(many=True, read_only=True)

        class Meta:
            model = Paciente
            fields = ['url', 'rut', 'nombre', 'telefonos', 'correos']

Descripción General
-------------------

`PacienteSerializer` es utilizado para representar datos del modelo `Paciente` en Django REST Framework. Combina la información básica del paciente con sus números telefónicos y correos electrónicos asociados.

Estructura y Comportamiento
---------------------------

- **url**: URL del recurso del paciente.
- **rut**: Identificador RUT del paciente.
- **nombre**: Nombre completo del paciente.
- **telefonos**: Lista de teléfonos del paciente (`TelefonoSerializer`).
- **correos**: Lista de correos electrónicos del paciente (`CorreoSerializer`).
