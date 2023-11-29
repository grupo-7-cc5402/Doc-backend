.. _commands

Comandos
========

Comandos para el envio de correos.

enviar_mails
============

Comando para enviar todos los correos de la cola de envio, cuyas fecha y hora de envio sean menores o iguales a las del momento en que ejecuta el comando. El total de correos enviados está limitado por el límite de envíos diarios de los correos. Cuando se envía un correo se genera un log (como objeto de la clase ``Log``) y se le remueve de la cola.

Definición
----------

.. code-block:: python

    class Command(BaseCommand):

        def handle(self, *args, **options):
                #today = datetime.today()
                today = timezone.now()

                correos = CorreoHCUCH.objects.all().order_by('ultimo_uso')
                n = MailQueue.objects.all().count()
                
                for correo in correos:
                    if (today - correo.ultimo_uso).days >= 1:
                        setattr(correo, 'usos_disponibles', correo.limite)
                        correo.save()

                    n_envios = min(n, correo.usos_disponibles)
                    envios = []

                    for mail in MailQueue.objects.all().order_by('fecha_envio')[:n_envios]:

                        # se detiene cuando ya se han enviado todos los correos agendados hasta la fecha
                        if mail.fecha_envio > today:
                            n = 0
                            break

                        try:
                            paciente = mail.paciente
                            correos_paciente = list(paciente.get_correos().values_list('email', flat=True))
                            correos_paciente = list(map(lambda x: str(x), correos_paciente))
                        except:
                            mail.delete()
                            continue

                        envio = EmailMessage(
                            mail.subject,
                            mail.body,
                            correo.email,
                            correos_paciente,
                            reply_to=REPLY_TO_EMAIL
                        )

                        LogEvento.objects.create(
                            id_evento=mail.id_evento,
                            tipo='envio_correo',
                            usuario_autor=mail.usuario_autor,
                            contenido=mail.body,
                            data_adicional={
                                'asunto': mail.subject,
                                'cuerpo': mail.body,
                                'correos': correos_paciente,
                                'enviado_desde': correo.email,
                                'REPLY_TO': REPLY_TO_EMAIL,
                                'fecha_agendada_de_envio': str(timezone.localtime(mail.fecha_envio)),
                                'fecha_de_envio': str(timezone.localtime(today))
                            }
                        )

                        envios.append(envio)
                        mail.delete()
                    
                    connection = get_connection(backend="django.core.mail.backends.smtp.EmailBackend",
                                                username=correo.email,
                                                password=correo.clave)
                    connection.open()
                    connection.send_messages(envios)
                    connection.close()

                    setattr(correo, 'usos_disponibles', correo.usos_disponibles - len(envios))
                    setattr(correo, 'ultimo_uso', today)
                    correo.save()

                    n -= n_envios
                    if n <= 0:
                        break

Uso
---

Al ejecutar el siguiente comando en consola se envían todos los correos agendados hasta la fecha

>>> make enviarmails