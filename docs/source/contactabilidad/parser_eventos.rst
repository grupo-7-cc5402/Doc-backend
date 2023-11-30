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