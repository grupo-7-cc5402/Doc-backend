.. _model:

SMS - Modelo
===============

SmsQueue
===========

El modelo `SmsQueue` representa los mensajes SMS en cola para ser enviados.

Definición
----------

.. code-block:: python

    from django.db import models
    from your_app.models import Evento, Paciente

    ESTADO_SMS = [
        ('enviado', 'Enviado'),
        ('no enviado', 'No enviado'),
        ('error', 'Error')
    ]

    class SmsQueue(models.Model):
        evento = models.ForeignKey(Evento, on_delete=models.CASCADE, null=False)
        number = models.CharField(max_length=15, null=False)
        message = models.CharField(max_length=500, null=False)
        paciente = models.ForeignKey(Paciente, on_delete=models.CASCADE, null=False)
        fecha_programada = models.DateTimeField(auto_now_add=False)
        horas_anticipacion = models.IntegerField()
        enviado = models.CharField(max_length=20, choices=ESTADO_SMS)

Atributos
---------

- `evento`: Una clave externa que se relaciona con el modelo `Evento`.
- `number`: Un campo de caracteres que almacena el número de teléfono del destinatario.
- `message`: Un campo de caracteres que almacena el contenido del mensaje SMS.
- `paciente`: Una clave externa que se relaciona con el modelo `Paciente`.
- `fecha_programada`: Un campo de fecha y hora que representa la fecha y hora programada para enviar el SMS.
- `horas_anticipacion`: Un campo entero que representa las horas de anticipación para enviar el SMS.
- `enviado`: Un campo de opciones que representa el estado del SMS, con opciones como 'enviado', 'no enviado' y 'error'.

Uso
---

Este modelo se utiliza para almacenar información sobre los mensajes SMS en cola, incluyendo el contenido del mensaje, el destinatario, la fecha programada y el estado de envío.

