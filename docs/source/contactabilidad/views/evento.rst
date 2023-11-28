EventoViewSet
=============

Descripción Breve
-----------------

`EventoViewSet` maneja las operaciones CRUD para los eventos, incluyendo filtros específicos y permisos.

URLs
----

- `/api/eventos/`: Endpoint principal para operaciones con eventos.

Métodos HTTP
------------

- GET: Retorna una lista de eventos o un evento específico.
- POST: Crea un nuevo evento.
- PUT/PATCH: Actualiza un evento existente.
- DELETE: Elimina un evento.

Clase de Vista
--------------

.. code-block:: python

    class EventoViewSet(viewsets.ModelViewSet):
        # Incluir aquí la implementación relevante de la vista.

Atributos
---------

- **queryset**: `Evento.objects.all()`
- **serializer_class**: `EventoSerializer`
- **permission_classes**: `[IsAssignedToEvento | IsAdminUser]`
- **filterset_fields**: Filtros para `id_externa`, `campaña`, `paciente`, `estado`, `operador_asignado`.

Métodos Personalizados
----------------------

- `get_queryset`: Personaliza el queryset en función del usuario.
- `partial_update`: Implementación personalizada para actualizar parcialmente un evento y registrar el cambio en `LogEvento`.

Ejemplos de Uso
---------------

(Ejemplos de cómo interactuar con la API, incluyendo solicitudes y respuestas típicas.)
