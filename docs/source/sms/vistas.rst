.. _sms_viewset:

Vistas - SMS
================

El `SmsQueueViewSet` es una clase de vista que proporciona funcionalidades CRUD (Crear, Leer, Actualizar, Eliminar) para el modelo `SmsQueue`.

Definición
----------

.. code-block:: python


    class SmsQueueViewSet(viewsets.ModelViewSet):
        queryset = SmsQueue.objects.all()
        serializer_class = SmsQueueSerializer
        filterset_fields = ['paciente', 'number', 'fecha_programada']

Atributos
---------

- `queryset`: Un conjunto de consultas que representa todos los objetos `SmsQueue` en la base de datos.
- `serializer_class`: La clase de serializador asociada al modelo `SmsQueue`.
- `filterset_fields`: Campos que se pueden utilizar para filtrar las instancias de `SmsQueue` en las consultas.

Uso
---

Este `SmsQueueViewSet` se utiliza en el contexto de una API REST para realizar operaciones CRUD en instancias de `SmsQueue`. Los filtros definidos en `filterset_fields` permiten realizar consultas específicas basadas en los valores de los campos especificados.


