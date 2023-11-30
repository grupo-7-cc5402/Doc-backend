PacienteViewSet
===============

.. code-block:: python

    from rest_framework import viewsets
    from ..models.paciente import Paciente
    from ..serializers.paciente import PacienteSerializer

    class PacienteViewSet(viewsets.ModelViewSet):
        queryset = Paciente.objects.all()
        serializer_class = PacienteSerializer
        filterset_fields = ['rut', 'nombre']
        permission_classes = [IsAdminUser | IsAssignedToPacienteReadOnly]

Descripción General
-------------------

`PacienteViewSet` es una vista de conjunto de modelos en Django Rest Framework que proporciona un conjunto completo de acciones para manipular datos del modelo `Paciente`.

Atributos y Métodos
-------------------

- **queryset**: Define el conjunto de objetos `Paciente` que están disponibles en esta vista.

- **serializer_class**: Especifica `PacienteSerializer` como la clase de serialización.

- **filterset_fields**: Permite filtrar los pacientes basados en los campos 'rut' y 'nombre'.

- **permission_classes**: Define las clases de permisos, permitiendo el acceso solo a usuarios administradores o a usuarios asignados a pacientes específicos.

- **get_queryset**: Método que personaliza el conjunto de datos devuelto. Si el usuario es administrador, retorna todos los pacientes. De lo contrario, solo retorna pacientes asignados al operador actual.

Uso
---

Facilita la creación, visualización, actualización y eliminación de registros de pacientes, además de permitir filtrar y buscar pacientes específicos.
