EspecialidadViewSet
===================

.. code-block:: python

    from rest_framework import viewsets
    from ..models.especialidad import Especialidad
    from ..serializers.especialidad import EspecialidadSerializer

    class EspecialidadViewSet(viewsets.ModelViewSet):
        queryset = Especialidad.objects.all()
        serializer_class = EspecialidadSerializer
        filterset_fields = ['nombre']

Descripción General
-------------------

`EspecialidadViewSet` es una vista de conjunto de modelos en Django Rest Framework para el manejo del modelo `Especialidad`.

Atributos y Métodos
-------------------

- **queryset**: Conjunto de todos los objetos `Especialidad`.

- **serializer_class**: Utiliza `EspecialidadSerializer` para la serialización de datos.

- **filterset_fields**: Permite filtrar las especialidades por el campo 'nombre'.

SubespecialidadViewSet
======================

.. code-block:: python

    from ..models.especialidad import Subespecialidad
    from ..serializers.especialidad import SubespecialidadSerializer

    class SubespecialidadViewSet(viewsets.ModelViewSet):
        queryset = Subespecialidad.objects.all()
        serializer_class = SubespecialidadSerializer
        filterset_fields = ['nombre', 'especialidad']

Descripción General
-------------------

`SubespecialidadViewSet` provee acciones para el modelo `Subespecialidad` en Django Rest Framework.

Atributos y Métodos
-------------------

- **queryset**: Incluye todos los objetos `Subespecialidad`.

- **serializer_class**: Define `SubespecialidadSerializer` como la clase de serialización.

- **filterset_fields**: Habilita el filtrado por 'nombre' y 'especialidad'.
