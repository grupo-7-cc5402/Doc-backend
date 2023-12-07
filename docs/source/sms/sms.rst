.. _sms:

SMS - Funciones 
=========

sms_message_format
-------------------

La función ``sms_message_format`` se utiliza para formatear un mensaje SMS con información del paciente, día, hora y médico.

Firma
~~~~~

.. code-block:: python

    def sms_message_format(message: str, paciente: Paciente, dia, hora, medico: Profesional) -> str:

Descripción
~~~~~~~~~~~

Esta función toma un mensaje de plantilla, un objeto ``Paciente``, información del día, hora y un objeto ``Profesional``. Reemplaza las placeholders en el mensaje con la información correspondiente y devuelve el mensaje formateado.

Parámetros
~~~~~~~~~

- **message** (:class:`str`): El mensaje de plantilla a formatear.
- **paciente** (:class:`Paciente`): Objeto paciente con información relevante.
- **dia**: Información sobre el día.
- **hora**: Información sobre la hora.
- **medico** (:class:`Profesional`): Objeto médico.

Retorno
~~~~~~~

- :class:`str`: El mensaje SMS formateado.

handle_response
-------------------

La función ``handle_response`` maneja la respuesta de CELCOM para posteriormente guardarla en los registros de la aplicación.

Firma
~~~~~

.. code-block:: python

    def handle_response(status, fecha_envio) -> tuple:

Descripción
~~~~~~~~~~~

Esta función toma el estado de la respuesta y la fecha de envío. Luego, analiza el estado y devuelve una tupla que contiene un mensaje de registro y el código de respuesta.

Parámetros
~~~~~~~~~

- **status** (:class:`str`): El estado de la respuesta de CELCOM.
- **fecha_envio**: Fecha y hora de envío.

Retorno
~~~~~~~

- :class:`tuple`: Una tupla que contiene el mensaje de registro y el código de respuesta.

diferencia_de_horas
--------------------

La función ``diferencia_de_horas`` calcula la diferencia de tiempo en horas entre la hora actual y una hora futura.

Firma
~~~~~

.. code-block:: python

    def diferencia_de_horas(hora_futura) -> float:

Descripción
~~~~~~~~~~~

Esta función toma una hora futura, calcula la diferencia de tiempo en horas y la devuelve.

Parámetros
~~~~~~~~~

- **hora_futura**: Hora futura para calcular la diferencia.

Retorno
~~~~~~~

- :class:`float`: La diferencia de tiempo en horas.

queueSms
--------

La función ``queueSms`` encola mensajes SMS y obtiene la información necesaria para su posterior envío.

Firma
~~~~~

.. code-block:: python

    def queueSms(campaña: Campaña):

Descripción
~~~~~~~~~~~

Esta función toma un objeto ``Campaña``, ordena los eventos relacionados y los recordatorios asociados. Luego, para cada recordatorio de tipo SMS, genera mensajes SMS formateados y los encola.

Parámetros
~~~~~~~~~

- **campaña** (:class:`Campaña`): Objeto campaña para el cual se encolarán los mensajes SMS.

Retorno
~~~~~~~

- :class:`None`: No tiene un valor de retorno explícito.

Uso
---

Esta función se utiliza para preparar y encolar mensajes SMS relacionados con una campaña específica.

.. _sms_functions:



Validación de Datos y Envío de Mensajes
----------------------------------------

En este apartado, se documentan las funciones relacionadas con la validación de datos y el envío de mensajes SMS a través de la API de CELCOM.

validar_datos
-------------

La función ``validar_datos`` valida la longitud y formato de un número de teléfono.

Firma
~~~~~

.. code-block:: python

    @staticmethod
    def validar_datos(telefono: str) -> Union[str, None]:

Descripción
~~~~~~~~~~~

Esta función estática toma un número de teléfono como parámetro y verifica su longitud y formato. Retorna un mensaje de error si el número no cumple con los requisitos especificados.

Parámetros
~~~~~~~~~

- **telefono** (:class:`str`): Número de teléfono a validar.

Retorno
~~~~~~~

- :class:`str` o :class:`None`: Mensaje de error si la validación falla, de lo contrario, retorna ``None``.

format_number
--------------

La función ``format_number`` formatea un número de teléfono, eliminando el signo '+' al principio.

Firma
~~~~~

.. code-block:: python

    def format_number(telefono: str) -> str:

Descripción
~~~~~~~~~~~

Esta función toma un número de teléfono como parámetro y elimina el signo '+' si está presente al principio del número.

Parámetros
~~~~~~~~~

- **telefono** (:class:`str`): Número de teléfono a formatear.

Retorno
~~~~~~~

- :class:`str`: Número de teléfono formateado.

enviar_mensaje
---------------

La función ``enviar_mensaje`` maneja el envío de mensajes a través de la API de CELCOM.

Firma
~~~~~

.. code-block:: python

    def enviar_mensaje(numero: str, mensaje: str) -> Tuple[str, bool, Optional[str]]:

Descripción
~~~~~~~~~~~

Esta función toma un número de teléfono y un mensaje como parámetros, valida el número y envía el mensaje a través de la API de CELCOM. Retorna un mensaje de estado, un indicador de éxito y, opcionalmente, la respuesta del servidor.

Parámetros
~~~~~~~~~

- **numero** (:class:`str`): Número de teléfono al cual enviar el mensaje.
- **mensaje** (:class:`str`): Mensaje a enviar.

Retorno
~~~~~~~

- :class:`Tuple`: Una tupla que contiene un mensaje de estado, un indicador de éxito y, opcionalmente, la respuesta del servidor.

Uso
---

Estas funciones son utilizadas para validar números de teléfono y enviar mensajes SMS a través de la API de CELCOM.


