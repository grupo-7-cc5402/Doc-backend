ReporteCampanas
===============

Descripción General
-------------------

`ReporteCampanas` es una vista de Django Rest Framework diseñada para generar un reporte detallado de las campañas y eventos relacionados, en formato de hoja de cálculo Excel.

Clase de Vista
--------------

.. code-block:: python

    class ReporteCampanas(views.APIView):
        queryset = Campaña.objects.all()
        permission_classes = [IsAdminUser | IsSupervisor]

        def get(self, request, format=None):
            # Implementación del método GET aquí

Atributos
---------

- **queryset**: Consulta todas las campañas `Campaña`.
- **permission_classes**: Restringe el acceso a usuarios administradores o supervisores.

Método GET
----------

El método `get` maneja solicitudes GET y realiza las siguientes operaciones:

1. **Filtrar Campañas por Fecha**: Recoge parámetros de fecha de inicio y fin de la solicitud GET para filtrar campañas dentro de este rango.
   
2. **Generación de Hojas de Cálculo**:
   - **Primera Hoja**: Resumen de las campañas, incluyendo ID, nombre, usuario creador, fecha de creación, total de llamados, estado y tipo.
   - **Segunda Hoja**: Detalle de cada campaña, incluyendo información de los eventos asociados.
   - **Tercera Hoja**: Detalles de llamados, con información de los registros de llamadas y estados de los eventos.
   - **Cuarta Hoja**: Reporte de SMS, detallando los envíos de SMS de las campañas.
   - **Quinta Hoja**: Reporte de Correos, con información sobre los correos enviados en el marco de las campañas.

3. **Respuesta HTTP**: Devuelve un archivo de Excel como respuesta HTTP con `Content-Disposition` para descarga.

Uso y Consideraciones
---------------------

- Este endpoint está diseñado para ser utilizado por administradores y supervisores que necesitan un informe completo y detallado de las actividades de las campañas.
- Se debe tener cuidado con la gestión de memoria y el rendimiento al generar grandes reportes.
- Las fechas de inicio y fin son opcionales; si no se proporcionan, el reporte incluirá todas las campañas.




Hojas del Reporte
-----------------

Hoja: Campañas Creadas
^^^^^^^^^^^^^^^^^^^^^^

Esta hoja del reporte generado por ``ReporteCampanas`` proporciona un resumen de todas las campañas creadas, incluyendo detalles clave de cada campaña. Los datos presentados en esta hoja están diseñados para ofrecer una visión general rápida y completa de las campañas en el rango de fechas especificado, si se proporcionan.

Contenido de la Hoja 'Campañas Creadas'
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- **ID Campaña**: Identificador único de cada campaña.
- **Nombre Campaña**: Nombre descriptivo de la campaña.
- **Usuario Creador**: Nombre de usuario del creador de la campaña.
- **Fecha Creación**: Fecha en que se creó la campaña, formateada en DD/MM/AAAA.
- **Total Llamados Campaña**: Número total de llamados realizados en el marco de la campaña.
- **Estado Campaña**: Estado actual de la campaña, como activo, inactivo, etc.
- **Tipo Campaña**: Tipo de la campaña, describiendo su categoría o propósito.

Generación de Datos
^^^^^^^^^^^^^^^^^^^

Los datos se recopilan de la base de datos de las campañas, filtrando por fechas de creación si se especifican ``fecha_inicio`` y ``fecha_fin``. Se calcula el total de llamados por campaña utilizando una agregación de la cantidad de registros de llamadas asociados con cada campaña. La hoja se genera dinámicamente en respuesta a una solicitud HTTP y se incluye como la primera hoja en el archivo de Excel generado.

.. code-block:: python

    # Filtrar por rango de fechas
    fecha_inicio_str = request.GET.get('fecha_inicio', None)
    fecha_fin_str = request.GET.get('fecha_fin', None)
    fecha_inicio = parse_date(fecha_inicio_str) if fecha_inicio_str else None
    fecha_fin = parse_date(fecha_fin_str) if fecha_fin_str else None
    # Ajustar fechas para considerar el día completo
    if fecha_inicio:
        fecha_inicio = make_aware(datetime.combine(fecha_inicio, datetime.min.time()))
    if fecha_fin:
        fecha_fin = make_aware(datetime.combine(fecha_fin, datetime.max.time()))

    # Creación de la hoja de trabajo 'Campañas Creadas'
    wb = Workbook()
    ws1 = wb.active
    ws1.title = "Campañas Creadas"
    ws1.append(['ID CAMPAÑA', 'NOMBRE CAMPAÑA', 'USUARIO CREADOR', 'FECHA CREACIÓN', 'TOTAL LLAMADOS CAMPAÑA',
                'ESTADO CAMPAÑA', 'TIPO CAMPAÑA'])

    campañas = Campaña.objects.all()
    if fecha_inicio and fecha_fin:
        campañas = campañas.filter(fecha_creacion__range=(fecha_inicio, fecha_fin))
    elif fecha_inicio:
        campañas = campañas.filter(fecha_creacion__gte=fecha_inicio)
    elif fecha_fin:
        campañas = campañas.filter(fecha_creacion__lte=fecha_fin)
    campañas = campañas.annotate(total_llamados=Count('eventos__logs', filter=models.Q(eventos__logs__tipo='llamada')))

    for campaña in campañas:
        ws1.append([
            campaña.id,
            campaña.nombre,
            campaña.usuario_creador.username,
            campaña.fecha_creacion.strftime('%d/%m/%Y'),
            campaña.total_llamados,
            campaña.estado,
            campaña.tipo.nombre
        ])





Hoja: Detalle Campaña
^^^^^^^^^^^^^^^^^^^^^

Esta hoja del reporte generado por ``ReporteCampanas`` detalla cada evento individual asociado con las campañas. Proporciona una visión en profundidad de los eventos, incluyendo la información del centro médico, la especialidad, el profesional a cargo, y más.

Contenido de la Hoja 'Detalle Campaña'
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- **ID Evento**: Identificador único del evento.
- **ID Campaña**: Identificador de la campaña asociada al evento.
- **Tipo Campaña**: Tipo de la campaña asociada.
- **ID Centro**: Identificador del centro médico asociado al evento.
- **Nombre Centro Médico**: Nombre del centro médico.
- **ID Especialidad**: Identificador de la especialidad médica.
- **Nombre Especialidad**: Nombre de la especialidad médica.
- **Procedimiento**: Descripción del procedimiento asociado al evento.
- **Nombre Profesional**: Nombre del profesional médico asociado.
- **Fecha Atención**: Fecha programada para el evento.
- **Hora Atención**: Hora programada para el evento.
- **ID Externa**: Identificador externo del evento.
- **RUT-DV Paciente**: RUT del paciente con dígito verificador.
- **RUT Paciente**: RUT del paciente sin dígito verificador.
- **Fecha Captura**: Fecha de captura del último estado relevante del evento.
- **Hora Captura**: Hora de captura del último estado relevante del evento.
- **Estado Llamado Final**: Estado final del evento tras la última interacción.
- **Usuario Captura**: Nombre de usuario que realizó la última captura.
- **Usuario**: Nombre completo del usuario que realizó la última captura.
- **rut-Dv**: RUT con dígito verificador del usuario que realizó la última captura.
- **rut**: RUT sin dígito verificador del usuario que realizó la última captura.

Generación de Datos
^^^^^^^^^^^^^^^^^^^

Los datos se recopilan y se organizan para cada evento de las campañas, incluyendo información detallada sobre la atención médica y las interacciones con el sistema. La hoja se genera dinámicamente en respuesta a una solicitud HTTP y se incluye como la segunda hoja en el archivo de Excel generado.

.. code-block:: python

    # SEGUNDA HOJA, CAMPAÑA
    ws2 = wb.create_sheet(title="Detalle Campaña")
    ws2.append(['ID EVENTO', 'ID CAMPAÑA', 'TIPO CAMPAÑA', 'ID CENTRO', 'NOMBRE CENTRO MEDICO',
                'ID ESPECIALIDAD', 'NOMBRE ESPECIALIDAD', 'PROCEDIMIENTO', 'NOMBRE PROFESIONAL',
                'FECHA ATENCION', 'HORA ATENCION', 'ID EXTERNA', 'RUT-DV PACIENTE', 'RUT PACIENTE',
                'FECHA CAPTURA', 'HORA CAPTURA',
                'ESTADO LLAMADO FINAL', 'USUARIO CAPTURA', 'Usuario', 'rut-Dv', 'rut'])
    
    eventos_campaña = Evento.objects.filter(campaña__in=campañas)
    logs_relevantes = LogEvento.objects.filter(id_evento__campaña__in=campañas, tipo='llamada').order_by('id_evento', '-fecha')
    # Crear un diccionario para mapear cada evento a su último llamado
    ultimo_llamado_por_evento = {}
    for log in logs_relevantes:
        if log.id_evento_id not in ultimo_llamado_por_evento:
            ultimo_llamado_por_evento[log.id_evento_id] = log

    # Se itera sobre los eventos y utilizar el diccionario para obtener el último llamado
    for evento in eventos_campaña:
        ultimo_llamado = ultimo_llamado_por_evento.get(evento.id)
        rut_usuario_captura = ultimo_llamado.usuario_autor.rut if ultimo_llamado else None
        rut_sin_dv = rut_usuario_captura.replace("-", "") if ultimo_llamado else None
        rut_sin_dv = rut_sin_dv[:-1] if ultimo_llamado else None

        ws2.append([
            evento.id,
            evento.campaña.id if evento.campaña else None,
            evento.campaña.tipo.nombre,
            evento.centro.id,
            evento.centro.nombre,
            evento.especialidad.id if evento.especialidad else None,
            evento.especialidad.nombre if evento.especialidad else None,
            evento.procedimiento,
            evento.profesional.nombre if evento.profesional else None,
            evento.fecha_programada.strftime('%d/%m/%Y') if evento.fecha_programada else None,
            evento.fecha_programada.strftime('%H:%M') if evento.fecha_programada else None,
            evento.id_externa,
            evento.paciente.rut,
            evento.paciente.rut.replace("-", "")[:-1],
            ultimo_llamado.fecha.strftime('%d/%m/%Y') if ultimo_llamado else None,
            ultimo_llamado.fecha.strftime('%H:%M') if ultimo_llamado else None,
            evento.estado.nombre,
            ultimo_llamado.usuario_autor.username if ultimo_llamado else None,
            f"{ultimo_llamado.usuario_autor.last_name} {ultimo_llamado.usuario_autor.first_name}" if ultimo_llamado else None,
            rut_usuario_captura,
            rut_sin_dv
        ])
    del logs_relevantes



Hoja: Detalle Llamados
^^^^^^^^^^^^^^^^^^^^^^

La hoja "Detalle Llamados" en el reporte generado por `ReporteCampanas` proporciona un análisis detallado de todas las llamadas realizadas en relación con las campañas. Esta hoja incluye datos cruciales como el ID de la llamada, el estado de la llamada, observaciones relevantes, y la identificación del usuario que gestionó la llamada.

Contenido de la Hoja
~~~~~~~~~~~~~~~~~~~~

La hoja contiene las siguientes columnas:

- **ID REGISTRO LLAMADOS**: Identificador único de cada llamada registrada.
- **ID DETALLE LLAMADOS**: Identificador detallado de la llamada.
- **FECHA_LLAMADO** y **HORA_LLAMADO**: Fecha y hora en que se realizó la llamada.
- **ID ESTADO LLAMADO** y **ESTADO LLAMADO**: Identificador y nombre del estado final de la llamada.
- **OBSERVACIONES LLAMADOS**: Notas u observaciones hechas durante la llamada.
- **TIPO**: Tipo de campaña asociada a la llamada.
- **USUARIO GESTION LLAMADO**: Información del usuario que gestionó la llamada.
- **USUARIO**, **rut-Dv**, **rut**: Datos personales del usuario que gestionó la llamada.

Generación de Datos
~~~~~~~~~~~~~~~~~~~

.. code-block:: python

    # Generación de la hoja "Detalle Llamados" y recopilación de datos.
    ws3 = wb.create_sheet(title="Detalle Llamados")
    ws3.append([...])  # Encabezados de la hoja

    # Obtención de llamadas y logs relevantes
    todas_llamadas = LogEvento.objects.filter(...)
    todos_logs = LogEvento.objects.filter(...)

    # Agrupación de llamadas y logs por evento
    llamadas_por_evento = {k: list(g) for k, g in groupby(todas_llamadas, key=attrgetter('id_evento'))}
    logs_por_evento = {k: list(g) for k, g in groupby(todos_logs, key=attrgetter('id_evento'))}

    estados_evento = {estado.nombre: estado for estado in EstadoEvento.objects.all()}

    for evento in eventos_campaña:
        llamadas = llamadas_por_evento.get(evento, [])
        logs_evento = logs_por_evento.get(evento, [])

        for i, llamada in enumerate(llamadas):
            # Lógica para determinar el último estado y observaciones de la llamada
            # Agregar fila con datos de la llamada a la hoja Excel
            ws3.append([...])


Hoja: Reporte SMS
^^^^^^^^^^^^^^^^^

La hoja "Reporte SMS" en el reporte generado por `ReporteCampanas` se dedica a compilar información detallada sobre los mensajes SMS enviados en el contexto de las campañas. Esta hoja incluye detalles como el ID del SMS, el recordatorio asociado, la información del evento, y el estado del envío.

Contenido de la Hoja
~~~~~~~~~~~~~~~~~~~~

La hoja se estructura con las siguientes columnas:

- **Sistema**: Identifica el sistema desde el cual se envió el SMS.
- **SMS ID**: Identificador único del mensaje SMS.
- **RECORDATORIO ID**: ID del recordatorio relacionado con el SMS.
- **ID EVENTO**: Identificador del evento al que está asociado el SMS.
- **ID EXTERNA**: Identificador externo asociado con el evento.
- **RUT PACIENTE**: RUT del paciente objetivo del SMS.
- **Nro Telefono**: Número de teléfono al que se envió el SMS.
- **Fecha y Hora Programada de Envio**: Fecha y hora en que se programó el envío del SMS.
- **Estado**: Indica si el SMS ha sido enviado o está pendiente.
- **Mensaje Programado de Envio**: Contenido del mensaje SMS.
- **Fecha y Hora Recepcion Paciente**: Fecha y hora en que se recibió el SMS, si aplica.
- **Codigo y Mensaje Respuesta CELCOM**: Respuesta del operador de telefonía.
- **Nro Corto**: Número corto desde el cual se envió el SMS.

Generación de Datos
~~~~~~~~~~~~~~~~~~~

.. code-block:: python

    ws4 = wb.create_sheet(title="Reporte SMS")
    ws4.append([...])  # Encabezados de la hoja

    eventos = Evento.objects.filter(...).distinct()
    todos_recordatorios = Recordatorio.objects.filter(...).order_by('campaña', '-horas_anticipacion')
    todos_log_eventos = LogEvento.objects.filter(...).order_by('id_evento', 'fecha')

    recordatorios_por_campanna = {...}
    logs_por_evento = {...}
    telefonos_pacientes = {...}

    for evento in eventos:
        recordatorios = recordatorios_por_campanna.get(evento.campaña_id, [])
        log_eventos = logs_por_evento.get(evento.id, [])
        for recordatorio in recordatorios:
            log_evento_correspondiente = ...
            # Lógica para recopilar y añadir la información a la hoja
            ws4.append([...])

Explicación de la Generación de Datos
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. **Configuración Inicial**: Se crea una nueva hoja en el libro de Excel y se añaden los encabezados correspondientes.
2. **Selección de Eventos y Recordatorios**: Se seleccionan los eventos y recordatorios relacionados con SMS.
3. **Agrupación de Datos**: Los recordatorios y logs se agrupan por ID de evento y campaña.
4. **Recopilación de Información**: Para cada evento, se recopila información de los recordatorios y logs correspondientes, incluyendo datos del paciente y detalles del envío.
5. **Adición de Filas a la Hoja**: Se añaden los datos recopilados a la hoja Excel, respetando la estructura definida.


Hoja: Reporte Correos
^^^^^^^^^^^^^^^^^^^^^

La hoja "Reporte Correos" del reporte generado por `ReporteCampanas` está dedicada a recopilar información sobre los correos electrónicos enviados en el contexto de las campañas. Esta hoja abarca detalles como el ID del correo, el recordatorio asociado, la información del evento, y el estado del envío.

Contenido de la Hoja
~~~~~~~~~~~~~~~~~~~~

La hoja está estructurada con las siguientes columnas:

- **Sistema**: Indica el sistema desde el cual se envió el correo electrónico.
- **Correo ID**: Identificador único del correo electrónico enviado.
- **Recordatorio ID**: ID del recordatorio asociado al correo.
- **ID CAMPAÑA**: Identificador de la campaña relacionada.
- **ID EVENTO**: Identificador del evento al que está asociado el correo.
- **ID EXTERNA**: Identificador externo vinculado con el evento.
- **RUT PACIENTE**: RUT del paciente destinatario del correo.
- **CORREO**: Dirección de correo electrónico del destinatario.
- **Fecha y Hora Programada de Envio**: Fecha y hora en que se programó el envío del correo.
- **Estado**: Indica si el correo ha sido enviado o está pendiente.
- **Mensaje Programado de Envio**: Contenido del mensaje de correo electrónico.
- **Asunto Programado de Envio**: Asunto del correo electrónico.
- **CORREO REMITENTE**: Dirección de correo electrónico del remitente.
- **Fecha y Hora de Envio**: Fecha y hora en que se envió el correo.

Generación de Datos
~~~~~~~~~~~~~~~~~~~

.. code-block:: python

    ws5 = wb.create_sheet(title="Reporte Correos")
    ws5.append([...])  # Encabezados de la hoja

    eventos = Evento.objects.filter(...).distinct()
    todos_recordatorios = Recordatorio.objects.filter(...).order_by('campaña', '-horas_anticipacion')
    todos_log_eventos = LogEvento.objects.filter(...).order_by('id_evento', 'fecha')

    recordatorios_por_campanna = {...}
    logs_por_evento = {...}
    correos_pacientes = {...}

    for evento in eventos:
        recordatorios = recordatorios_por_campanna.get(evento.campaña_id, [])
        log_eventos = logs_por_evento.get(evento.id, [])
        for recordatorio in recordatorios:
            log_evento_correspondiente = ...
            # Lógica para recopilar y añadir la información a la hoja
            ws5.append([...])

Explicación de la Generación de Datos
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. **Configuración Inicial**: Se crea una nueva hoja en el libro de Excel y se añaden los encabezados correspondientes.
2. **Selección de Eventos y Recordatorios**: Se seleccionan los eventos y recordatorios relacionados con correos electrónicos.
3. **Agrupación de Datos**: Los recordatorios y logs se agrupan por ID de evento y campaña.
4. **Recopilación de Información**: Para cada evento, se recopila información de los recordatorios y logs correspondientes, incluyendo datos del paciente y detalles del envío.
5. **Adición de Filas a la Hoja**: Se añaden los datos recopilados a la hoja Excel, respetando la estructura definida.



