TipoCampañaSerializer
=====================

Descripción General
-------------------

`TipoCampañaSerializer` es una clase serializadora en Django REST framework que maneja la conversión de instancias del modelo `TipoCampaña` a representaciones JSON y viceversa.

Estructura y Comportamiento
---------------------------

- **Clase Base**: Hereda de `serializers.HyperlinkedModelSerializer`.
- **Modelo Asociado**: `TipoCampaña`.
- **Campos Serializados**: Incluye los campos `url`, `id`, `nombre`, `descripcion`, `estados_permitidos`.
- **Campo Especializado**: `estados_permitidos` usa `EstadoEventoSerializer` para representar los estados permitidos en un tipo de campaña.

Utilidad y Casos de Uso
-----------------------

- **APIs RESTful**: Facilita la representación y manipulación de datos del modelo `TipoCampaña` en la API.

CampañaSerializer
=================

Descripción General
-------------------
`CampañaSerializer` se encarga de serializar y deserializar instancias del modelo `Campaña`, convirtiéndolas en representaciones JSON y viceversa.

Estructura y Comportamiento
---------------------------

- **Clase Base**: Extiende `serializers.HyperlinkedModelSerializer`.
- **Modelo Asociado**: `Campaña`.
- **Campos Serializados**: Incluye `url`, `id`, `nombre`, `usuario_creador`, `fecha_creacion`, `tipo`, `estado`, `operadores`, `eventos`, `recordatorios`.
- **Campos Relacionales**:
  - `usuario_creador`, `eventos`, `operadores`: Utilizan `HyperlinkedRelatedField` para enlazar a otras vistas de detalle.
  - `recordatorios`: Utiliza `RecordatorioSerializer` para representar recordatorios asociados.
- **Método Customizado**: `get_estado` para obtener la representación en texto del estado de la campaña.

Utilidad y Casos de Uso
~~~~~~~~~~~~~~~~~~~~~~~

- **Gestión de Campañas**: Permite gestionar la información de las campañas en la API, incluyendo sus relaciones con usuarios, eventos y recordatorios.
- **Creación de Campañas**: La implementación del método `create` permite la creación de campañas junto con sus recordatorios relacionados.

