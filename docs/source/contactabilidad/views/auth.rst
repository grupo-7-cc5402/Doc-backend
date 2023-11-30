LoginView
=========

.. code-block:: python

    from knox.views import LoginView as KnoxLoginView
    from rest_framework.authentication import BasicAuthentication

    class LoginView(KnoxLoginView):
        authentication_classes = [BasicAuthentication]

Descripción General
-------------------

`LoginView` es una clase de vista personalizada que extiende la funcionalidad de `KnoxLoginView` de la biblioteca Knox en Django Rest Framework. Está diseñada para manejar la autenticación de usuarios utilizando el mecanismo de autenticación básica.

Atributos y Métodos
-------------------

- **authentication_classes**: Define el método de autenticación que se utilizará para esta vista. En este caso, se ha configurado para usar `BasicAuthentication`, que es un esquema de autenticación simple incluido en Django Rest Framework.

Uso
---

`LoginView` se utiliza para proporcionar un punto de entrada para la autenticación de usuarios en la aplicación web. Al extender `KnoxLoginView`, aprovecha las capacidades de Knox para la gestión de tokens de autenticación, añadiendo una capa de seguridad adicional y facilitando el manejo de sesiones de usuario.