.. _parser:

Parser de Eventos
=================

Parser para leer datos de csv con información de citas médicas y crear los eventos y pacientes correspondiente. Recibe los datos como una lista de diccionarios, donde cada diccionario representa una fila y sus campos las columnas. Para leer los datos primero revisa las llaves de los diccionarios e infiere a que campo del event corresponde cada una.

.. code-block:: python

    class ParserEvento():
        
        rut_names = ['rut', 'numero de registro medico', 'nu']
        dv_names = ['dv', 'digito verificador', 'numero verificador']
        paciente_names = ['paciente', 'nombre paciente', 'nombre completo']
        id_names = ['id', 'sid']
        telefono_names = ['telefono', 'fono', 'celular']
        correos_names = ['correo', 'mail', 'email']
        centro_names = ['hospital', 'centro', 'establecimiento']
        medico_names = ['medico', 'profesional', 'dr']
        especialidad_names = ['especialidad']
        subespecialidad_names = ['subespecialidad']
        procedimiento_names = ['procedimiento', 'precedimiento', 'cirugia']
        desc_names = ['descripcion', 'origen']

Campos
------

- **rut_names**: Lista de posibels nombres de la columna con el rut del paciente.

- **dv_names**: Lista de posibels nombres de la columna con el dígito verificador del rut del paciente.

- **paciente_names**: Lista de posibels nombres de la columna con el nombre del paciente.

- **id_names**: Lista de posibels nombres de la columna con el ID externo del evento.

- **telefono_names**: Lista de posibels nombres de las columnas con teléfonos del paciente.

- **correos_names**: Lista de posibels nombres de las columnas con correos del paciente.

- **centro_names**: Lista de posibels nombres de la columna con el centro médico del evento.

- **medico_names**: Lista de posibels nombres de la columna con el nombre del profesional del evento.

- **especialidad_names**: Lista de posibels nombres de la columna con la especialidad del evento.

- **subespecialidad_names**: Lista de posibels nombres de la columna con la subespecialidad del evento.

- **procedimiento_names**: Lista de posibels nombres de la columna con el procedimiento del evento.

- **desc_names**: Lista de posibels nombres de la columna con la descripción del evento.


Métodos
-------

- ``clean_str(self, x)``: Recive un string, lo pasa a minúsculas y elimina barras bajas y tildes.

.. code-block:: python

    def clean_str(self, x: str):
        x = x.lower().strip()
        x = x.replace('_', ' ')
        x = x.replace('á', 'a')
        x = x.replace('é', 'e')
        x = x.replace('í', 'i')
        x = x.replace('ó', 'o')
        x = x.replace('ú', 'u')
        return x

- ``get_rut_col(self, keys)``: Dados los nombres de las columnas del csv, retorna el que estime corresponda a la columna con el rut, según si el nombre está en la lista especificada en rut_names o si contiene la palabra rut. Luego elimina el nombre de la lista.

.. code-block:: python

    def get_rut_col(self, keys: List[str]):
        
        for i, key in enumerate(keys):
            key_ = self.clean_str(key)
            if key_ in self.rut_names or 'rut' in key_:
                keys.pop(i)
                return key
        
        return 'RUT'

- ``get_dv_col(self, keys)``: Dados los nombres de las columnas del csv, retorna el que estime corresponda a la columna con el dv, según si el nombre está en la lista especificada en dv_names o si contiene la palabra dv. Luego elimina el nombre de la lista.

.. code-block:: python

    def get_dv_col(self, keys: List[str]):
        
        for i, key in enumerate(keys):
            key_ = self.clean_str(key)
            if key_ in self.dv_names:
                keys.pop(i)
                return key
        
        return None


- ``get_subespecialidad_col(self, keys)``: Dados los nombres de las columnas del csv, retorna el que estime corresponda a la columna con la subespecialidad, según si el nombre está en la lista subespecificada en subespecialidad_names. Luego elimina el nombre de la lista.

.. code-block:: python
    
    def get_subespecialidad_col(self, keys: List[str]):
        
        for i, key in enumerate(keys):
            key_ = self.clean_str(key)
            if key_ in self.subespecialidad_names:
                keys.pop(i)
                return key
        
        return None


- ``get_especialidad_col(self, keys)``: Dados los nombres de las columnas del csv, retorna el que estime corresponda a la columna con la especialidad, según si el nombre está en la lista especificada en especialidad_names. Luego elimina el nombre de la lista.

.. code-block:: python

    def get_especialidad_col(self, keys: List[str]):
        
        for i, key in enumerate(keys):
            key_ = self.clean_str(key)
            if key_ in self.especialidad_names:
                keys.pop(i)
                return key
        
        return None

- ``get_paciente_col(self, keys)``: Dados los nombres de las columnas del csv, retorna el que estime corresponda a la columna con el nombre del paciente, según si el nombre está en la lista especificada en paciente_names o si contiene las palabras nombre y paciente. Luego elimina el nombre de la lista.

.. code-block:: python
    
    def get_paciente_col(self, keys: List[str]):
        
        for i, key in enumerate(keys):
            key_ = self.clean_str(key)
            if key_ in self.paciente_names:
                keys.pop(i)
                return key
        for i, key in enumerate(keys):
            key_ = self.clean_str(key)
            if 'nombre' in key_ and 'paciente' in key_:
                keys.pop(i)
                return key
        
        return 'NOMBRE PACIENTE'

- ``get_telefono_cols(self, keys)``: Dados los nombres de las columnas del csv, retorna los que estime corresponda a columnas con los teléfonos, según si el nombre contiene alguna de las palabras especificadas en fono_names. Luego elimina el nombre de la lista.

.. code-block:: python
    
    def get_telefono_cols(self, keys: List[str]):
        
        tele_cols = []
        for fono_name in self.telefono_names:
            for i, key in enumerate(keys):
                key_ = self.clean_str(key)
                if fono_name in key_:
                    keys.pop(i)
                    tele_cols.append(key)
        
        return tele_cols

- ``get_correo_cols(self, keys)``: Dados los nombres de las columnas del csv, retorna los que estime corresponda a columnas con los correos, según si el nombre contiene alguna de las palabras especificadas en correos_names. Luego elimina el nombre de la lista.

.. code-block:: python
    
    def get_correo_cols(self, keys: List[str]):
        
        correo_cols = []
        for correo_name in self.correos_names:
            for i, key in enumerate(keys):
                key_ = self.clean_str(key)
                if correo_name in key_:
                    keys.pop(i)
                    correo_cols.append(key)
        
        return correo_cols


- ``get_id_col(self, keys)``: Dados los nombres de las columnas del csv, retorna el que estime corresponda a la columna con el id externo, según si el nombre contiene alguna de las palabras especificadas en id_names. Luego elimina el nombre de la lista.

.. code-block:: python

    def get_id_col(self, keys: List[str]):
        
        for i, key in enumerate(keys):
            key_ = self.clean_str(key).split()
            for id in self.id_names:
                if id in key_:
                    keys.pop(i)
                    return key
        
        return None


- ``get_centro_col(self, keys)``: Dados los nombres de las columnas del csv, retorna el que estime corresponda a la columna con el centro, según si el nombre contiene alguna de las palabras especificadas en centro_names. Luego elimina el nombre de la lista.

.. code-block:: python
    
    def get_centro_col(self, keys: List[str]):
        
        for centro_name in self.centro_names:
            for i, key in enumerate(keys):
                key_ = self.clean_str(key)
                if centro_name in key_:
                    keys.pop(i)
                    return key
        
        return None


- ``get_procedimiento_col(self, keys)``: Dados los nombres de las columnas del csv, retorna el que estime corresponda a la columna con el procedimiento, según si el nombre contiene alguna de las palabras especificadas en procedimiento_names. Luego elimina el nombre de la lista.

.. code-block:: python
    
    def get_procedimiento_col(self, keys: List[str]):
        
        for procedimiento_name in self.procedimiento_names:
            for i, key in enumerate(keys):
                key_ = self.clean_str(key)
                if procedimiento_name in key_:
                    keys.pop(i)
                    return key
        
        return None


- ``get_medico_col(self, keys)``: Dados los nombres de las columnas del csv, retorna el que estime corresponda a la columna con el nombre del profecional, según si el nombre contiene alguna de las palabras especificadas en medico_names. Luego elimina el nombre de la lista.

.. code-block:: python
    
    def get_medico_col(self, keys: List[str]):
        
        for medico_name in self.medico_names:
            for i, key in enumerate(keys):
                key_ = self.clean_str(key)
                if medico_name in key_:
                    keys.pop(i)
                    return key
        
        return None


- ``get_desc_col(self, keys)``: Dados los nombres de las columnas del csv, retorna el que estime corresponda a la columna con la descripción, según si el nombre contiene alguna de las palabras especificadas en desc_names. Luego elimina el nombre de la lista.

.. code-block:: python
    
    def get_desc_col(self, keys: List[str]):
        
        for desc_name in self.desc_names:
            for i, key in enumerate(keys):
                key_ = self.clean_str(key)
                if desc_name in key_:
                    keys.pop(i)
                    return key
        
        return None


- ``get_time_col(self, keys)``: Dados los nombres de las columnas del csv, retorna los que estime correspondan a la columna con la fecha y la hora, según si el nombre contiene las palabras 'fecha' y 'hora' respectivamente.

.. code-block:: python
    
    def get_time_col(self, keys: List[str]):
        
        fecha_col = 'FECHA'
        hora_col = None
        for key in keys:
            if 'fecha' in self.clean_str(key):
                fecha_col = key
                break
        for key in keys:
            if 'hora' in self.clean_str(key):
                hora_col = key
                break
        return fecha_col, hora_col


- ``time_parser(self, keys)``: Junta los strings de las columnas de fecha y hora en un string en el formato válido para datos temporales: 'yy-mm-dd hh:mm:ss'. Sólo soporta strings que ya vengan en este formato o en la formas: 'dd/mm/yy', 'dd-mm-yy'.

.. code-block:: python
    
    def time_parser(self, row: Dict, fecha_col: str, hora_col: str):
        
        if fecha_col == hora_col:
            date = row[fecha_col].strip().split()
            fecha, hora = date[0], date[1]
        else:
            fecha = row[fecha_col].strip()
            if hora_col:
                hora = row[hora_col].strip().split()[0]
            else:
                hora = None
        
        if '/' in fecha:
            dia, mes, año = fecha.split('/')
            fecha = '-'.join([año, mes, dia])
        elif '-' in fecha:
            dia, mes, año = fecha.split('-')
            if len(dia) > 2:
                dia, mes, año = año, mes, dia
                fecha = '-'.join([año, mes, dia])
        else:
            raise Exception('Fecha en formato inválido: ' + fecha)
        
        if hora:
            return fecha + ' ' + hora
        return fecha


- ``read_eventos(self, json_file)``: Lee el contenido de un json de eventos (lista de diccionarios) y crea una nueva versión con los campos válidos para el modelos de datos.

.. code-block:: python

    def read_eventos(self, json_file: List[Dict]) -> List[Dict]:
         
        eventos = []

        data_list = json_file
        columnas = list(data_list[0].keys())

        rut_col = self.get_rut_col(columnas)
        dv_col = self.get_dv_col(columnas)
        subespecialidad_col = self.get_subespecialidad_col(columnas)
        especialidad_col = self.get_especialidad_col(columnas)
        pac_col = self.get_paciente_col(columnas)
        id_col = self.get_id_col(columnas)
        telefono_cols = self.get_telefono_cols(columnas)
        correo_cols = self.get_correo_cols(columnas)
        centro_col = self.get_centro_col(columnas)
        medico_col = self.get_medico_col(columnas)
        procedimiento_col = self.get_procedimiento_col(columnas)
        desc_col = self.get_desc_col(columnas)
        fecha_col, hora_col = self.get_time_col(columnas)
        
        for row in data_list:

            if len(list(filter(lambda x: x != None, row.values()))) == 0:
                continue

            evento = {
                'fecha_programada': self.time_parser(row, fecha_col, hora_col),
                'paciente': {
                    'nombre': row[pac_col].replace(',', ''),
                    'rut': str(row[rut_col])
                }
            }
            if dv_col:
                evento['paciente']['rut'] = evento['paciente']['rut'] + '-' + str(row[dv_col])

            if especialidad_col:
                evento['especialidad'] = row[especialidad_col]
            else:
                evento['especialidad'] = None

            if subespecialidad_col:
                evento['subespecialidad'] = row[subespecialidad_col]
            else:
                evento['subespecialidad'] = None

            if id_col:
                id = row[id_col]
                if type(id) == int:
                    evento['id_externa'] = id
                else:
                    evento['id_externa'] = None
            else:
                evento['id_externa'] = None

            if centro_col:
                evento['centro'] = row[centro_col]

            evento['profesional'] = None
            if medico_col:
                profesional = row[medico_col]
                if type(profesional) == str:
                    evento['profesional'] = row[medico_col]

            if procedimiento_col:
                evento['procedimiento'] = row[procedimiento_col]

            if desc_col:
                evento['descripcion'] = row[desc_col]
            else:
                evento['descripcion'] = ''
            
            telefonos = []
            for telef_col in telefono_cols:
                numero = row[telef_col]
                if numero:
                    if type(numero) == str:
                        numero = numero.replace('+', '')
                        try:
                            numero = int(numero)
                        except:
                            continue
                    if numero / 1e7 < 1:
                        continue
                    telefonos.append({'numero': str(numero)})
            evento['telefonos'] = telefonos

            correos = []
            for correo_col in correo_cols:
                correo = row[correo_col]
                if correo:
                    correos.append({'email': correo})
            evento['correos'] = correos

            eventos.append(evento) 
        
        return eventos


- ``parse_eventos(self, tipo_eventos, eventos_json)``: Recibe un json de eventos junto a su tipo y crea una campaña con los mismos. Si hay pacientes o profesionales nuevos los añade a la base de datos. Si una especialidad, subespecialidad o centro no se encuentran en la base de datos, se añada a la descripción en vez del campo correspondiente.

.. code-block:: python

    def parse_eventos(self, tipo_eventos: str, eventos_json: List[Dict]):

        eventos_list = self.read_eventos(eventos_json)

        eventos = []
        for evento_data in eventos_list:
            paciente_data = evento_data.pop('paciente', [])
            telefonos_data = evento_data.pop('telefonos', [])
            correos_data = evento_data.pop('correos', [])
            rut = paciente_data['rut']

            if evento_data['especialidad']:
                especialidad = evento_data['especialidad']
                try:
                    especialidad = Especialidad.validar_tipo(especialidad)
                    especialidad_instance = Especialidad.objects.get(nombre=especialidad)
                    evento_data['especialidad'] = especialidad_instance
                except Especialidad.DoesNotExist:
                    evento_data['especialidad'] = Especialidad.objects.get(nombre='ESPECIALIDAD INVALIDA')
                    evento_data['descripcion'] = especialidad +'\n'+ evento_data['descripcion']
                    
            if evento_data['subespecialidad']:
                subespecialidad = evento_data['subespecialidad']
                try:
                    subespecialidad = Subespecialidad.validar_tipo(subespecialidad)
                    subespecialidad_instance = Subespecialidad.objects.get(nombre=subespecialidad)
                    evento_data['subespecialidad'] = subespecialidad_instance
                except Subespecialidad.DoesNotExist:
                    evento_data['subespecialidad'] = Subespecialidad.objects.get(nombre='SUBESPECIALIDAD INVALIDA')
                    evento_data['descripcion'] = subespecialidad +'\n'+ evento_data['descripcion']

            centro = evento_data['centro']
            try:
                centro_ = CentroAtencion.validar_tipo(centro)
                centro_instance = CentroAtencion.objects.get(nombre=centro_)
                evento_data['centro'] = centro_instance
            except CentroAtencion.DoesNotExist:
                evento_data['centro'] = CentroAtencion.objects.get(nombre='CENTRO EXTERNO')
                evento_data['descripcion'] = centro +'\n'+ evento_data['descripcion']
            
            if evento_data['profesional']:
                profesional_instance, _ = Profesional.objects.get_or_create(nombre=evento_data['profesional'])
                evento_data['profesional'] = profesional_instance

            try:
                paciente_instance = Paciente.objects.get(rut=rut)
                evento_data['paciente'] = paciente_instance
            except Paciente.DoesNotExist:
                paciente_instance, _ = Paciente.objects.update_or_create(
                    rut=rut,
                    defaults=paciente_data
                )
                evento_data['paciente'] = paciente_instance

                for telefono_data in telefonos_data:
                    telefono_instance, _ = Telefono.objects.update_or_create(
                        paciente=paciente_instance,
                        numero=telefono_data['numero'],
                        defaults=telefono_data
                    )
                
                for correo_data in correos_data:
                    correo_instance, _ = Correo.objects.update_or_create(
                        paciente=paciente_instance,
                        email=correo_data['email'],
                        defaults=correo_data
                    )

            # Crea nuevo evento ignorando campos que no están en el modelo
            evento = Evento()
            for key, value in evento_data.items():
                setattr(evento, key, value)

            eventos.append(evento)
        
        return eventos