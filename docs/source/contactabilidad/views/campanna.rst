TipoCampañaViewSet
==================

.. code-block:: python

    from rest_framework import viewsets
    from ..models.campanna import TipoCampaña
    from ..serializers.campanna import TipoCampañaSerializer

    class TipoCampañaViewSet(viewsets.ModelViewSet):
        queryset = TipoCampaña.objects.all()
        serializer_class = TipoCampañaSerializer
        filterset_fields = ['nombre']

Descripción General
-------------------

La vista ``TipoCampañaViewSet`` es una clase de Django Rest Framework diseñada para gestionar las operaciones CRUD (Crear, Leer, Actualizar, Eliminar) en los objetos del modelo ``TipoCampaña``. Se implementa utilizando un ``ModelViewSet``, que ofrece una interfaz completa para la interacción con los datos del modelo.

Atributos
---------

- **queryset**: Especifica el conjunto de datos que se utilizará, en este caso, ``TipoCampaña.objects.all()``, que representa todos los objetos del modelo ``TipoCampaña``.

- **serializer_class**: Define la clase de serialización utilizada para convertir las instancias de ``TipoCampaña`` entre representaciones complejas y tipos de datos nativos de Python. En este caso, se utiliza ``TipoCampañaSerializer``.

- **filterset_fields**: Define los campos disponibles para filtrar las consultas. Aquí se permite filtrar por el campo ``nombre`` del modelo ``TipoCampaña``.



CampañaViewSet
==============

.. code-block:: python

    from rest_framework import viewsets
    from django.db import transaction
    from ..models.campanna import Campaña
    from ..serializers.campanna import CampañaSerializer
    from ..filters.campanna import CampañaFilter
    from ..parser_eventos import ParserEvento
    from ..permissions import IsAssignedToCampañaReadOnly
    from appcontacto.mails.mail import enqueue_mails, BLOQUEADO_O_SIN_DATOS
    from appcontacto.sms.sms import queueSms

    class CampañaViewSet(viewsets.ModelViewSet):
        queryset = Campaña.objects.all()
        serializer_class = CampañaSerializer
        filterset_class = CampañaFilter
        permission_classes = [IsAdminUser | IsAssignedToCampañaReadOnly]

        def get_queryset(self):
            if self.request.user.is_staff:
                return Campaña.objects.all()
            return Campaña.objects.filter(operadores=self.request.user)

        def create(self, request, *args, **kwargs):
            # Código del método create

Descripción General
-------------------

La vista ``CampañaViewSet`` es una clase de Django Rest Framework destinada a gestionar las operaciones CRUD para los objetos del modelo ``Campaña``.

Atributos y Métodos
-------------------

- **queryset**: Conjunto de todos los objetos ``Campaña``.

- **serializer_class**: La clase ``CampañaSerializer`` se utiliza para la serialización y deserialización de datos.

- **filterset_class**: ``CampañaFilter`` se utiliza para filtrar las consultas en base a criterios específicos.

- **permission_classes**: Define las clases de permisos que controlan el acceso a las operaciones del ViewSet.



- **get_queryset**: Retorna todas las campañas si el usuario es administrador. En caso contrario, devuelve solo las campañas a las que el operador está asignado.

- **create**: Método para crear una nueva campaña. Si se incluyen eventos en formato CSV, estos se parsean y asignan a la campaña. Además, se encolan correos y SMS relacionados con la campaña.

Uso
---

Este ViewSet se utiliza para crear, consultar, actualizar y eliminar campañas en el sistema, así como para asignar eventos a dichas campañas y gestionar las comunicaciones relacionadas.

