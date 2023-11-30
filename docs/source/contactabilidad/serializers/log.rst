LogEventoSerializer
===================

Descripción General
-------------------

El `LogEventoSerializer` es un serializador en Django REST Framework utilizado para la representación y manejo de datos del modelo `LogEvento`. Este serializador convierte los registros de eventos entre instancias de modelo y formatos JSON, facilitando su uso en interfaces de programación de aplicaciones (APIs).

Estructura y Comportamiento
---------------------------

`LogEventoSerializer` extiende `serializers.HyperlinkedModelSerializer` y está vinculado al modelo `LogEvento`. Proporciona los siguientes campos para la serialización:

- **url**: URL hiperenlazada para el registro del evento.
- **id**: Identificador único del registro de evento.
- **id_evento**: Referencia al evento asociado con este registro.
- **fecha**: Fecha y hora en que se registró el evento.
- **tipo**: Tipo del evento registrado (ejemplo: cambio de estado, envío de SMS).
- **usuario_autor**: Usuario que generó el registro del evento.
- **contenido**: Detalles o información adicional relacionada con el registro del evento.
