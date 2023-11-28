.. _mails:

Mails - Funciones
=================

Funciones para el manejo de los correos.

get_format_args
===============

Función que recibe un string con campos para formatear y retorna una lista con los nombres de estos.

Parámetros
----------

- **message** (:class:`string`): String del cual se extraerán sus campos para formatear.

Definición
----------

.. code-block:: python
      
    def get_format_args(message: str):

        inb = False
        args = []
        w = ''
        for c in message:
            if inb:
                if c == '}':
                    inb = False
                    args.append(w)
                    w = ''
                else:
                    w += c
            elif c == '{':
                inb = True
        
        return args

Uso
---

>>> get_format_args("Today is {month} {day} of {year}")
['month', 'day', 'year']




message_format
==============

Función para formatear los correos. Formatea un string con campos de eventos y pacientes definidos como: ``{modelo@attr}``, donde ``modelo`` puede ser paciente o evento, y ``attr`` es el nombre del campo que se desea extraer.

parámetros
----------

- **message** (:class:`string`): String con el mensaje a formatear.

- **paciente** (:class:`Paciente`): Objeto del modelo ``Paciente``. Contiene la información del paciente al que se le enviará el correo.

- **evento** (:class:`Evento`): Objeto del modelo ``Evento``. Contiene la información del evento sobre el cual se notifica al paciente.

definición
----------

.. code-block:: python

    def message_format(message: str, paciente: Paciente, evento: Evento):
       
        args = get_format_args(message)
        
        if len(args) == 0:
            return message
        
        values = []
        for arg in args:
            get_from, attr = arg.split('@')
            if get_from == 'paciente':
                value = str(getattr(paciente, attr))
            elif get_from == 'evento':
                if attr == 'dia':
                    date = timezone.localtime(evento.fecha_programada)
                    value = str(date.day) + ' de ' + meses[date.month-1] + ' de ' + str(date.year)
                elif attr == 'hora':
                    date = timezone.localtime(evento.fecha_programada)
                    minutes = str(date.minute)
                    if len(minutes) < 2:
                        minutes = '0' + minutes
                    value = str(date.hour) + ':' + minutes
                else:
                    value = str(getattr(evento, attr))
            else:
                value = arg
            values.append(value)

Uso
---

>>> message_format("{paciente@nombre} tiene cita asignada para el día {evento@dia} a las {evento@hora}")
"Rodrigo Gonzalez tiene cita asignada para el día 2023-11-28 a las 19:30"