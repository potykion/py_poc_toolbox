Рандом
#############

Все что не попало в остальные разделы

Порты
========

Вывод списка заюзанных портов:

.. code-block:: shell

    netstat -anltp | grep "LISTEN"

Еще про порты:

- `Статья из вики про частоиспользуемые порты <https://ru.wikipedia.org/wiki/%D0%A1%D0%BF%D0%B8%D1%81%D0%BE%D0%BA_%D0%BF%D0%BE%D1%80%D1%82%D0%BE%D0%B2_TCP_%D0%B8_UDP>`_




IIS
=======

`IIS <https://www.iis.net/>`_ - веб-сервер аля Nginx от Microsoft

.. image:: _static/iis.png

Команды
*************

Перезапуск всех приложений:

.. code-block:: shell

    iisreset