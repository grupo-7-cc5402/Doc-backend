.. _views:

Vistas
======

CorreoHCUCHViewSet
==================

Vista asociada al modelo ``CorreoHCUCH``. Permite consultar y modificar las cuentas de correo electrónico almacenadas.

Definición
----------

.. code-block:: python

    class CorreoHCUCHViewSet(viewsets.ModelViewSet):
        queryset = CorreoHCUCH.objects.all()
        serializer_class = CorreoHCUCHSerializer
        filterset_fields = ['email', 'ultimo_uso']





MailQueueViewSet
================

Vista asociada al modelo ``MailQueueViewSet``. Permite consultar y modificar la cola de envío de correos.

Definición
----------

.. code-block:: python

    class MailQueueViewSet(viewsets.ModelViewSet):
        queryset = MailQueue.objects.all()
        serializer_class = MailQueueSerializer
        filterset_fields = ['paciente', 'subject', 'fecha_envio']