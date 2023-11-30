EventoViewSet
=============

Descripción General
-------------------

`EventoViewSet` es una vista de Django Rest Framework diseñada para gestionar las operaciones CRUD de los objetos `Evento`.

URLs
----

- `/api/eventos/`: Endpoint principal para operaciones con eventos.

Clase de Vista
--------------

.. code-block:: python

    class EventoViewSet(viewsets.ModelViewSet):
        queryset = Evento.objects.all()
        serializer_class = EventoSerializer
        permission_classes = [IsAssignedToEvento | IsAdminUser]
        filterset_fields = {'id_externa': ['exact'],
                            'campaña': ['exact', 'isnull'],
                            'paciente': ['exact'],
                            'estado': ['exact'],
                            'operador_asignado': ['exact', 'isnull']}

        def get_queryset(self):
            """
            En caso de ser admin, se retornan todos los eventos. Si no, se retornan
            solo los eventos en los que el operador está asignado.
            """

            if self.request.user.is_staff:
                return Evento.objects.all()

            return Evento.objects.filter(operador_asignado=self.request.user)

        def partial_update(self, request, *args, **kwargs):
            # Obtener el objeto y su estado inicial
            instance = self.get_object()

            # Heredar operación de PATCH original
            response = super().partial_update(request, *args, **kwargs)

            # Obtener el estado final
            instance.refresh_from_db()
            estado_final = instance.estado

            # Si la operación fue exitosa, crear un log
            if response.status_code == status.HTTP_200_OK:
                contenido = estado_final
                LogEvento.objects.create(
                    id_evento=instance,
                    tipo='cambio_estado',
                    usuario_autor=request.user,
                    contenido=contenido
                )

            return response

Atributos
---------

- **queryset**: Consulta todos los objetos `Evento`.
- **serializer_class**: Utiliza `EventoSerializer` para la serialización de datos.
- **permission_classes**: Restringe el acceso a usuarios asignados al evento o administradores.
- **filterset_fields**: Permite filtrar eventos por diferentes campos, `id_externa`, `campaña`, `paciente`, `estado`, `operador_asignado`.

Métodos Personalizados
----------------------

- `get_queryset`: Personaliza el conjunto de consultas en función del rol del usuario (administrador o asignado al evento).
- `partial_update`: Maneja la actualización parcial de un evento y registra los cambios en `LogEvento`.

Métodos HTTP Soportados
-----------------------

- GET: Obtiene una lista de todos los eventos o un evento específico.
- POST: Crea un nuevo evento.
- PUT/PATCH: Actualiza un evento existente.
- DELETE: Elimina un evento.

Uso
---

Se utiliza para la gestión y seguimiento de eventos, incluyendo la asignación a operadores y la actualización de su estado.



EstadoEventoViewSet
===================

Descripción General
-------------------

`EstadoEventoViewSet` es una vista de Django Rest Framework que gestiona las operaciones CRUD para los objetos `EstadoEvento`.

URLs
----

- `/api/estados_evento/`: Endpoint principal para operaciones con eventos.

Clase de Vista
--------------

.. code-block:: python

    class EstadoEventoViewSet(viewsets.ModelViewSet):
        queryset = EstadoEvento.objects.all()
        serializer_class = EstadoEventoSerializer
        filterset_fields = ['nombre', 'es_terminal']

Atributos
---------

- **queryset**: Consulta todos los objetos `EstadoEvento`.
- **serializer_class**: Utiliza `EstadoEventoSerializer` para serializar los datos.
- **filterset_fields**: Permite filtrar los resultados por 'nombre' y si el estado es 'terminal'.

Métodos HTTP Soportados
-----------------------

- GET: Obtiene una lista de todos los `EstadoEvento` o un `EstadoEvento` específico.
- POST: Crea un nuevo `EstadoEvento`.
- PUT/PATCH: Actualiza un `EstadoEvento` existente.
- DELETE: Elimina un `EstadoEvento`.

Uso
---

Esta vista es usada para administrar los estados de los eventos en el sistema, como 'Activo', 'Inactivo', 'Finalizado', etc.




ProxEventoViewSet
=================

Descripción General
-------------------

`ProxEventoViewSet` es una vista personalizada en Django Rest Framework diseñada para retornar el próximo evento que un operador autenticado debe atender, basado en un criterio de prioridad.

URLs
----

- `/api/prox_evento/`: Endpoint principal para operaciones con eventos.


Clase de Vista
--------------

.. code-block:: python

    class ProxEventoViewSet(viewsets.ViewSet):
        """
        Retorna el siguiente evento (solo 1) que le corresponde atender al operador autenticado. Este evento se determina
        según un criterio de prioridad.

        Al hacer GET a este endpoint, primero se saca un evento sin asignar de la campaña, y se le asigna al operador,
        luego se retorna dicho evento. Si no quedan más eventos sin asignar, se retorna un evento que ya está asignado al
        operador, pero que aún no ha sido resuelto (no está en un estado terminal).
        """
        queryset = Evento.objects.all()
        permission_classes = [IsAuthenticated]

        def list(self, request):
            operador = request.user
            id_campaña = request.query_params.get('id_campana')

            # Primero revisamos si operador tiene evento pendiente en campaña actual:
            #################################################

            # Se obtienen todos los LogEvento del operador en la campaña
            logs_relevantes = LogEvento.objects.filter(
                usuario_autor=operador).order_by('-fecha')

            if id_campaña is not None:
                logs_relevantes = logs_relevantes.filter(
                    id_evento__campaña__id=id_campaña)

            # Se obtiene el último log de tipo 'asignacion_evento'
            ultimo_log_asignacion = logs_relevantes.filter(tipo='asignacion_evento').first()

            # Se verifica si hay logs de observacion o cambio estado después de la asignación
            if ultimo_log_asignacion:
                logs_post_asignacion = logs_relevantes.filter(
                    fecha__gt=ultimo_log_asignacion.fecha,
                    tipo__in=['observacion', 'cambio_estado']
                )

                if not logs_post_asignacion.exists():
                    serializer = EventoSerializer(ultimo_log_asignacion.id_evento, context={'request': request})
                    return Response(serializer.data)
                #Se desasigna el evento anterior 
                else:# logs_post_asignacion.exists():
                    evento_asociado = ultimo_log_asignacion.id_evento
                    if evento_asociado.estado.es_terminal:
                        Evento.objects.filter(pk=evento_asociado.pk).update(operador_asignado=None)
            ####################################################

            # Sacar eventos sin asignar o asignados al operador.
            queryset = Evento.objects.filter(models.Q(operador_asignado=operador) |
                                            models.Q(operador_asignado__isnull=True),
                                            estado__es_terminal=False)

            # La campaña debe estar activa y si se especifica, usar esa
            if id_campaña is not None:
                queryset = queryset.filter(campaña__estado='activo', campaña__id=id_campaña)
            else:
                queryset = queryset.filter(campaña__estado='activo')

            # Anotar cada evento con el número de veces que ha sido asignado
            queryset = queryset.annotate(
                num_asignaciones=models.Count(
                    models.Case(
                        models.When(logs__tipo='asignacion_evento', then=models.Value(1)),
                        output_field=models.IntegerField(),
                    )
                )
            )

            # Ordenar por operador asignado, número de asignaciones y fecha programada
            queryset = queryset.order_by('operador_asignado', 'num_asignaciones', 'fecha_programada')

            # Obtener solo un evento (el de mayor prioridad)
            evento = queryset.first()

            # Si encontró evento y si no estaba asignado, asignarlo ahora
            if evento and evento.operador_asignado is None:
                evento.operador_asignado = operador
                evento.save()

            # Crear un nuevo registro de log para la asignación del evento
            if evento:
                LogEvento.objects.create(
                    id_evento=evento,
                    tipo='asignacion_evento',
                    usuario_autor=operador
                )

            serializer = EventoSerializer(evento, context={'request': request})
            return Response(serializer.data)


Atributos
---------

- **queryset**: Consulta todos los objetos `Evento`.
- **permission_classes**: Restringe el acceso a usuarios autenticados.

Métodos Personalizados
----------------------

- `list(self, request)`: Método para manejar solicitudes GET. Determina y retorna el próximo evento a atender por el operador autenticado.

Funcionamiento del Método `list`
--------------------------------

1. Verifica si el operador tiene un evento pendiente en la campaña actual.
2. Si existe un evento pendiente, lo retorna; de lo contrario, busca un evento sin asignar.
3. Los eventos se filtran y ordenan por varios criterios, como el operador asignado, el número de asignaciones y la fecha programada.
4. Si se encuentra un evento sin asignar, se asigna al operador y se actualiza en la base de datos.
5. Se crea un registro en `LogEvento` para la asignación del evento.

Métodos HTTP Soportados
-----------------------

- GET: Retorna el próximo evento para el operador autenticado.

Uso
---

Esta vista es utilizada principalmente para la asignación dinámica de eventos a operadores en función de su disponibilidad y prioridad de los eventos.


