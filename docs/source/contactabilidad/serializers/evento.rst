EstadoEventoSerializer
======================

Descripción General
-------------------

`EstadoEventoSerializer` es una clase de serialización en Django REST framework para convertir instancias del modelo `EstadoEvento` en formatos JSON y viceversa.

Estructura y Comportamiento
---------------------------

- **Clase Base**: Hereda de `serializers.HyperlinkedModelSerializer`.
- **Modelo Asociado**: `EstadoEvento`.
- **Campos Serializados**: Incluye los campos `url`, `id`, `nombre`, `descripcion`, `es_terminal`.

Utilidad y Casos de Uso
~~~~~~~~~~~~~~~~~~~~~~~

- **APIs RESTful**: Utilizado para representar y manipular datos de estados de eventos en la API.
- **Interacción con Usuarios**: Permite mostrar y modificar información de estados de eventos en la interfaz basada en API.


EventoSerializer
================

Descripción General
-------------------

`EventoSerializer` gestiona la serialización de instancias del modelo `Evento`, facilitando la representación JSON de estas y su deserialización.

Estructura y Comportamiento
---------------------------

- **Clase Base**: Extiende `serializers.HyperlinkedModelSerializer`.
- **Modelo Asociado**: `Evento`.
- **Campos Serializados**: Incluye `url`, `id`, `campaña`, `id_externa`, `paciente`, `centro`, `especialidad`, `subespecialidad`, `procedimiento`, `profesional`, `fecha_programada`, `estado`, `descripcion`, `operador_asignado`, `logs`.
- **Campos Relacionales**:
  - `paciente`, `centro`, `especialidad`, `subespecialidad`, `profesional`: Utilizan serializadores específicos para representar relaciones de modelos relacionados.
  - `logs`: Usa `LogEventoSerializer` para representar múltiples instancias de log asociadas al evento.

Utilidad y Casos de Uso
-----------------------

- **Gestión de Eventos**: Permite la administración y presentación de eventos en la API.
- **Interconexión de Modelos**: Facilita la integración de datos relacionados como paciente, centro de atención, especialidades, y registros de log en la representación del evento.



