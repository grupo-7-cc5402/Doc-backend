GroupSerializer
===============

.. code-block:: python

    class GroupSerializer(serializers.HyperlinkedModelSerializer):
        class Meta:
            model = Group
            fields = ['id', 'name']

Descripción General
-------------------

`GroupSerializer` se utiliza para serializar los datos del modelo `Group` en Django REST Framework. Este modelo se refiere a los grupos de usuarios, comúnmente usados para gestionar permisos y roles.

Estructura y Comportamiento
---------------------------

- **id**: Identificador único para el grupo.
- **name**: Nombre del grupo.

UsuarioSerializer
=================

.. code-block:: python

    class UsuarioSerializer(serializers.HyperlinkedModelSerializer):
        groups = GroupSerializer(many=True, read_only=True)

        class Meta:
            model = Usuario
            fields = ['url', 'id', 'username', 'nombre_hcuch', 'rut', 'first_name', 'last_name', 'email', 'groups']

Descripción General
-------------------

`UsuarioSerializer` se utiliza para serializar y deserializar instancias del modelo `Usuario`. Extiende el modelo `AbstractUser` de Django, agregando campos adicionales y relaciones, como los grupos a los que pertenece el usuario.

Estructura y Comportamiento
---------------------------

- **url**: URL del recurso del usuario.
- **id**: Identificador único del usuario.
- **username**: Nombre de usuario utilizado para autenticación.
- **nombre_hcuch**: Nombre específico del usuario en el contexto de HCUCH (opcional).
- **rut**: Identificador único del usuario (RUT).
- **first_name**: Primer nombre del usuario.
- **last_name**: Apellido del usuario.
- **email**: Dirección de correo electrónico del usuario.
- **groups**: Grupos a los que pertenece el usuario, representados por `GroupSerializer`.

