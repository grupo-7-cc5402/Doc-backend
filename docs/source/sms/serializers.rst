.. _sms_queue_serializer

Serializador SmsQueue
=======================

El `SmsQueueSerializer` es un serializador que convierte instancias del modelo `SmsQueue` en representaciones JSON y viceversa.

Definición
----------

.. code-block:: python

    from rest_framework import serializers
    from .models import SmsQueue

    class SmsQueueSerializer(serializers.HyperlinkedModelSerializer):
        url = serializers.HyperlinkedIdentityField(view_name='sms:smsqueue-detail')

        class Meta:
            model = SmsQueue
            fields = ['url', 'id', 'evento', 'number', 'message', 'paciente', 'fecha_programada', 'horas_anticipacion', "enviado"]

Atributos
---------

- `url`: Campo de enlace hipermedia que proporciona un enlace a la vista detallada de la instancia de `SmsQueue`.
- `id`: Identificador único de la instancia de `SmsQueue`.
- `evento`: Una clave externa que se relaciona con el modelo `Evento`.
- `number`: Número de teléfono del destinatario del SMS.
- `message`: Contenido del mensaje SMS.
- `paciente`: Una clave externa que se relaciona con el modelo `Paciente`.
- `fecha_programada`: Fecha y hora programada para enviar el SMS.
- `horas_anticipacion`: Número de horas de anticipación para enviar el SMS.
- `enviado`: Estado del SMS, con opciones como 'enviado', 'no enviado' y 'error'.

Uso
---

Este serializador se utiliza en el contexto de una API REST para convertir instancias del modelo `SmsQueue` y mensajes SMS en representaciones JSON y viceversa.


Serializador MessageSms (DEPRECADO)
======================================

**Nota: Este serializador está deprecado y se recomienda utilizar otras alternativas según las mejores prácticas actuales.**

El `MessageSmsSerializer` es un serializador específico para manejar mensajes SMS.

Definición
----------

.. code-block:: python

    from rest_framework import serializers

    class MessageSmsSerializer(serializers.Serializer):
        message = serializers.CharField(max_length=100)

Atributos
---------

- `message`: Campo de caracteres para almacenar mensajes SMS específicos con una longitud máxima de 100 caracteres.

Uso
---

Este serializador se utilizaba anteriormente en el contexto de una API REST para manejar mensajes SMS específicos. Sin embargo, se ha marcado como deprecado, y se recomienda utilizar alternativas más actualizadas según las prácticas actuales de desarrollo.



