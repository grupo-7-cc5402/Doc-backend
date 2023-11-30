CentroAtencionViewSet
=====================

.. code-block:: python

    from rest_framework import viewsets
    from ..models.centro import CentroAtencion
    from ..serializers.centro import CentroAtencionSerializer

    class CentroAtencionViewSet(viewsets.ModelViewSet):
        queryset = CentroAtencion.objects.all()
        serializer_class = CentroAtencionSerializer
        filterset_fields = ['nombre']

Descripción General
-------------------

`CentroAtencionViewSet` es una vista de conjunto de modelos en Django Rest Framework que proporciona un conjunto completo de acciones para manejar datos del modelo `CentroAtencion`.

Atributos y Métodos
-------------------

- **queryset**: Define el conjunto de objetos `CentroAtencion` que están disponibles en esta vista.

- **serializer_class**: Especifica `CentroAtencionSerializer` como la clase de serialización.

- **filterset_fields**: Permite filtrar los centros de atención basados en el campo 'nombre'.
