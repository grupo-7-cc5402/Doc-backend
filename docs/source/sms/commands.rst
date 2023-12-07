.. _commands

Comandos
========

Comandos para el envío de SMS.

enviar_sms
==========

Comando para enviar todos los SMS en cola, que están dentro de la fecha de envío.

Definición
----------

.. code-block:: python

    class Command(BaseCommand):

        help = "Send the SMS in queue"

        def handle(self, *args, **options):
            today = datetime.today()

            # Filtra los SMS en cola y envía los que tienen la etiqueta "no enviado",
            # y los envía ordenados por fecha, mientras esta fecha sea menor a la diferencia
            # de horas especificada
            for sms in SmsQueue.objects.filter(enviado="no enviado").order_by('fecha_programada'):
                try:
                    if 0 < diferencia_de_horas(sms.fecha_programada) < sms.horas_anticipacion:

                        response, error, response_celcom = enviar_mensaje(sms.number, sms.message)

                        if error:
                            # Añadir error a la cola del mensaje y este no se volverá a enviar
                            # Este error es debido a que Celcom no está activo o mal implementación
                            sms.enviado = "error"
                            sms.save()

                            # Creación de los Logs de envío
                            logs = LogEvento()
                            logs.id_evento = sms.evento

                            logs.contenido = response
                            logs.tipo = "envio_sms"

                            data_adicional = {
                                "telefono": sms.number,
                                "fecha_agendada": str(sms.fecha_programada),
                                "error": "Error en envío"
                            }
                            logs.data_adicional = json.dumps(data_adicional)
                            logs.save()

                        else:
                            # Se maneja la respuesta de Celcom y solo se guarda como enviado si realmente se envió
                            # Si no se vuelve a enviar hasta que la hora de la cita ya ha pasado
                            response_send, status = handle_response(response_celcom, today)

                            if status == "000":
                                sms.enviado = "enviado"
                                sms.save()

                            # Creación de los Logs de envío
                            logs = LogEvento()
                            logs.id_evento = sms.evento

                            logs.contenido = response_send
                            logs.tipo = "envio_sms"

                            data_adicional = {
                                "telefono": sms.number,
                                "fecha_agendada": str(sms.fecha_programada),
                                "mensaje": sms.message,
                                "codigo_respuesta_celcom": status,
                                "mensaje_respuesta_celcom": response_send,
                                "nro_corto_envio": settings.CELCOM_NRO_CORTO
                            }
                            logs.data_adicional = json.dumps(data_adicional)
                            logs.save()

                        # Espera de tiempo para no enviar más de 5 SMS por segundo
                        time.sleep(0.2)

                except Exception as e:
                    print(f'Error en implementación de envío: {str(e)}')
                    continue

Uso
---

Al ejecutar el siguiente comando en consola se envían todos los sms agendados hasta la fecha

>>> make enviarsms