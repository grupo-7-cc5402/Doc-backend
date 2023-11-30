RecordatorioSerializer
======================

Descripción General
-------------------

`RecordatorioSerializer` es un serializador en Django REST Framework diseñado para representar y manejar datos del modelo `Recordatorio`. Permite la conversión entre la representación de instancias de `Recordatorio` y formatos JSON, facilitando su integración en APIs.

Estructura y Comportamiento
---------------------------

El serializador hereda de `serializers.HyperlinkedModelSerializer` y está asociado al modelo `Recordatorio`. Define los siguientes campos para serialización:

- **medio_contacto**: Tipo de medio utilizado para el recordatorio (ejemplo: teléfono, correo).
- **asunto**: Asunto o tema del recordatorio.
- **formato**: Formato o plantilla de texto del mensaje del recordatorio.
- **horas_anticipacion**: Número de horas de anticipación para el envío del recordatorio.
- **espera_respuesta**: Booleano que indica si se espera una respuesta al recordatorio.