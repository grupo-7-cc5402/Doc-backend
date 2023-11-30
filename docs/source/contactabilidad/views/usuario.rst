UsuarioViewSet
==============

.. code-block:: python

    from rest_framework import viewsets
    from rest_framework.decorators import action
    from rest_framework.permissions import IsAuthenticated
    from rest_framework.response import Response

    from ..models.usuario import Usuario
    from ..serializers.usuario import UsuarioSerializer

    class UsuarioViewSet(viewsets.ModelViewSet):
        queryset = Usuario.objects.all()
        serializer_class = UsuarioSerializer
        filterset_fields = ['username', 'rut', 'first_name', 'last_name', 'email']

        @action(detail=False, permission_classes=[IsAuthenticated])
        def me(self, request):
            serializer = self.get_serializer(request.user)
            return Response(serializer.data)

Descripción General
-------------------

`UsuarioViewSet` es una vista de conjunto de modelos en Django Rest Framework para el manejo del modelo `Usuario`.

Atributos y Métodos
-------------------

- **queryset**: Conjunto de todos los objetos `Usuario`.

- **serializer_class**: Utiliza `UsuarioSerializer` para la serialización de datos.

- **filterset_fields**: Permite filtrar usuarios por 'username', 'rut', 'first_name', 'last_name' y 'email'.

Métodos Personalizados
----------------------

- **me**: Un método decorado con `@action`, accesible solo para usuarios autenticados. Retorna los datos del usuario que realiza la solicitud.
  
- **detail**: False, indica que es una acción que no opera sobre un objeto individual.

- **permission_classes**: `[IsAuthenticated]`, restringe el acceso a usuarios autenticados.


