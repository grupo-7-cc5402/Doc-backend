LogEventoViewSet
================

.. code-block:: python

    class LogEventoViewSet(viewsets.ModelViewSet):
        queryset = LogEvento.objects.all()
        serializer_class = LogEventoSerializer
        filterset_class = LogEventoFilter
        permission_classes = [IsAssignedToEvento | IsAdminUser]
        filter_backends = (DjangoFilterBackend,)

        def create(self, request, *args, **kwargs):
            if request.user.is_authenticated:
                response = super(LogEventoViewSet, self).create(request, *args, **kwargs)
                if response.status_code == status.HTTP_201_CREATED:
                    log_evento = LogEvento.objects.get(pk=response.data['id'])
                    log_evento.usuario_autor = request.user
                    log_evento.save()
                return response
            else:
                return Response({'error': 'Usuario no autenticado'}, status=status.HTTP_401_UNAUTHORIZED)

Descripción General
-------------------

`LogEventoViewSet` es una clase de vista del modelo `LogEvento` en Django Rest Framework. Gestiona las operaciones CRUD (Crear, Leer, Actualizar, Eliminar) de los registros de eventos (logs) en el sistema.

Atributos y Métodos
-------------------

- **queryset**: Define el conjunto de registros de `LogEvento` que serán manejados por la vista.
- **serializer_class**: Especifica el serializador utilizado para la conversión de datos del modelo `LogEvento`.
- **filterset_class**: Clase de filtro para aplicar filtros personalizados a las consultas.
- **permission_classes**: Define las clases de permisos requeridas para acceder a la vista. Se utilizan `IsAssignedToEvento` y `IsAdminUser` para controlar el acceso.
- **filter_backends**: Establece el mecanismo de filtrado a utilizar.

Método `create`
---------------

- **Descripción**: Sobrescribe el método `create` para personalizar la creación de registros de eventos (logs). Asocia automáticamente el usuario autor del log si el usuario está autenticado.
- **Parámetros**:
    - `request`: La solicitud HTTP.
    - `*args`: Argumentos adicionales.
    - `**kwargs`: Argumentos con palabras clave.
- **Retorno**: Un objeto `Response` con el estado de la creación del registro.

Uso
---

El `LogEventoViewSet` se utiliza en el sistema para gestionar los registros de eventos, asegurando que cada acción realizada en el sistema quede debidamente registrada con su respectivo autor.

