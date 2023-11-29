.. _serializers:

Serializers
===========

CorreoHCUCHSerializer
=====================

Serializer asociado al modelo ``CorreoHCUCH``.

Definición
----------

.. code-block:: python

    class CorreoHCUCHSerializer(serializers.HyperlinkedModelSerializer):
        url = serializers.HyperlinkedIdentityField(view_name='mails:correohcuch-detail')

        class Meta:
            model = CorreoHCUCH
            fields = ['url', 'id', 'email', 'limite', 'ultimo_uso', 'clave']

        extra_kwargs = {'clave': {'write_only': True}}




MailQueueSerializer
=====================

Serializer asociado al modelo ``MailQueue``.

Definición
----------

.. code-block:: python

    class MailQueueSerializer(serializers.HyperlinkedModelSerializer):
        url = serializers.HyperlinkedIdentityField(view_name='mails:mailqueue-detail')

        class Meta:
            model = MailQueue
            fields = ['url', 'id', 'subject', 'body', 'paciente', 'fecha_envio', 'horas_anticipacion']




MessageSerializer
=====================

**[DEPRECADO]** Serializer diseñado para añadir correos manualmente a la cola de envío.

Definición
----------

.. code-block:: python

    class MessageSerializer(serializers.Serializer):
        subject = serializers.CharField(max_length=100)
        body = serializers.CharField(max_length=500)