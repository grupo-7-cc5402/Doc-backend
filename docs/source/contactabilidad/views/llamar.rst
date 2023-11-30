LlamarView
==========

.. code-block:: python

    import logging
    import requests
    from django.conf import settings
    from django.db import transaction
    from rest_framework import views, status
    from rest_framework.exceptions import ValidationError
    from rest_framework.response import Response
    from rest_framework.permissions import IsAuthenticated

    from ..exceptions import ServiceUnavailable
    from ..phonecall import call
    from ..models.log import LogEvento
    from ..models.evento import Evento

    logger = logging.getLogger(__name__)

    class LlamarView(views.APIView):
        permission_classes = [IsAuthenticated]

        @transaction.atomic()
        def post(self, request, format=None):
            # Implementación del método POST

Descripción General
-------------------

`LlamarView` es una vista API que permite realizar llamadas telefónicas a pacientes. Se activa mediante una petición POST que incluye el número de teléfono del paciente y el ID del evento relacionado.

Atributos y Métodos
-------------------

- **permission_classes**: Define que solo los usuarios autenticados pueden acceder a esta vista.

- **post(self, request, format=None)**: Este método maneja la solicitud POST. Realiza las siguientes acciones:
  - Valida el número de teléfono.
  - Busca el evento asociado y verifica que el operador autenticado esté asignado a él.
  - Crea un `LogEvento` para registrar la llamada.
  - Si la configuración lo permite, realiza la llamada telefónica utilizando la función `call`.
  - Maneja las excepciones que puedan surgir durante la llamada.

Excepciones
-----------

- **ValidationError**: Se lanza si el número de teléfono no es válido o si el ID del evento no es correcto.

- **ServiceUnavailable**: Se lanza si hay un error en la integración con la aplicación de llamadas.

Uso
---

Esta vista se utiliza para facilitar la comunicación con los pacientes a través de llamadas telefónicas, registrando cada interacción en el sistema.