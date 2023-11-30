RecordatorioViewSet
===================

.. code-block:: python

    from rest_framework import viewsets
    from ..models.recordatorio import Recordatorio
    from ..serializers.recordatorio import RecordatorioSerializer

    class RecordatorioViewSet(viewsets.ModelViewSet):
        queryset = Recordatorio.objects.all()
        serializer_class = RecordatorioSerializer
        filterset_fields = ['campaña', 'medio_contacto', 'espera_respuesta']

Descripción General
-------------------

`RecordatorioViewSet` es una vista de conjunto de modelos para el manejo de objetos `Recordatorio` dentro de la aplicación en base a Django Rest Framework.

Atributos y Métodos
-------------------

- **queryset**: Define el conjunto de objetos `Recordatorio` que están disponibles a través de esta vista.

- **serializer_class**: Especifica `RecordatorioSerializer` como la clase utilizada para la serialización y deserialización de datos de los recordatorios.

- **filterset_fields**: Permite filtrar los recordatorios basados en los campos 'campaña', 'medio_contacto' y 'espera_respuesta'.

