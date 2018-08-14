Supervisor
##########

`Supervisor`_ - программулина для управления процессами.

Установка
*********

Устанавливаем как питон-пакет:

.. code-block:: shell

    pip install supervisor

Создаем конфиг, в котором, помимо настроек Supervisor, будут описываться управляемые процессы:

.. code-block:: shell

    echo_supervisord_conf >  /etc/supervisord.conf


Создание процесса
*****************

Чтобы добавить процесс, необходимо определить секцию `program` в ``supervisord.conf``:

.. code-block:: cfg

    [program:{program_name}]
    process_name = {program_name}
    command={command}
    directory={directory}

    redirect_stderr=true
    user=root
    autorestart=true

- `program_name` - название процесса
- `command` - выполняемая команда
- `directory` - рабочая директория


Больше опций `здесь`_.

Часто веб-фреймворки сами предоставляют инструкции для запуска.
Помимо запуска с помощью Supervisor, описывается также nginx-проксирование, например `aiohttp`_, `gunicorn`_.

Выполняемая команда
===================

Питон программы принято запускать из виртуальной среды. Чтобы не прописывать путь к виртуальной среде, можно воспользоваться `pipenv`_:

.. code-block:: shell

    pipenv run python scrip.py

Полный путь к запускаемому скрипту писать не нужно, так как рабочая директория определена в ``supervisord.conf``.

Запуск и мониторинг процессов
********************************

После добавления или изменения процессов, необходимо считать изменения конфига и перезапустить процессы:

.. code-block:: shell

    sudo supervisorctl reread
    sudo supervisorctl update

Для просмотра активных процессов:

.. code-block:: shell

    sudo supervisorctl status

Для перезапуска конкретного процесса:

.. code-block:: shell

    sudo supervisorctl restart {process_name}

Если запущено несколько процессов (опция `numprocs`), то необходимо перезапустить группу процессов:

.. code-block:: shell

    sudo supervisorctl restart {process_name}:*

Если все плохо
***************

Если возникают ошибки, типа:

.. code-block:: shell

    unix:///var/run/supervisor.sock no such file

Или что-нибудь такое:

.. code-block:: shell

    error: <class 'socket.error'>, [Errno 2] No such file or directory: file: /usr/lib/python2.7/socket.py line: 224

То, проще всего, перезапустить supervisor:

.. code-block:: shell

    sudo supervisord


.. _Supervisor: http://supervisord.org/
.. _здесь: http://supervisord.org/configuration.html
.. _pipenv: https://docs.pipenv.org
.. _aiohttp: https://aiohttp.readthedocs.io/en/stable/deployment.html
.. _gunicorn: http://docs.gunicorn.org/en/latest/deploy.html