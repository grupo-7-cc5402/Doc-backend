ProfesionalViewSet
==================

.. code-block:: python

    from rest_framework import viewsets
    from ..models.profesional import Profesional
    from ..serializers.profesional import ProfesionalSerializer

    class ProfesionalViewSet(viewsets.ModelViewSet):
        queryset = Profesional.objects.all()
        serializer_class = ProfesionalSerializer
        filterset_fields = ['nombre', 'tipo']

Descripción General
-------------------

`ProfesionalViewSet` es una vista de conjunto de modelos que proporciona funcionalidades para crear, listar, actualizar y eliminar objetos `Profesional`.

Atributos y Métodos
-------------------

- **queryset**: Define el conjunto de objetos `Profesional` a los que se puede acceder a través de esta vista.

- **serializer_class**: Especifica la clase `ProfesionalSerializer` que se usa para serializar y deserializar los objetos `Profesional`.

- **filterset_fields**: Define los campos sobre los cuales se pueden aplicar filtros, en este caso, 'nombre' y 'tipo'.

TipoProfesionalViewSet
======================

.. code-block:: python

    from rest_framework import viewsets
    from ..models.profesional import TipoProfesional
    from ..serializers.profesional import TipoProfesionalSerializer

    class TipoProfesionalViewSet(viewsets.ModelViewSet):
        queryset = TipoProfesional.objects.all()
        serializer_class = TipoProfesionalSerializer
        filterset_fields = ['nombre']

Descripción General
-------------------

`TipoProfesionalViewSet` es una vista de conjunto de modelos enfocada en la gestión de los `TipoProfesional`.

Atributos y Métodos
-------------------

- **queryset**: Define el conjunto de objetos `TipoProfesional` disponibles en la vista.

- **serializer_class**: Utiliza `TipoProfesionalSerializer` para la serialización de datos.

- **filterset_fields**: Permite filtrar los tipos de profesional por su 'nombre'.

Uso
---

Ambos conjuntos de vistas son componentes clave en un sistema de gestión de profesionales, donde `ProfesionalViewSet` gestiona los profesionales individuales y `TipoProfesionalViewSet` se ocupa de los distintos tipos de profesionales. Estas vistas facilitan la creación, visualización y modificación de estos datos a través de una API.
