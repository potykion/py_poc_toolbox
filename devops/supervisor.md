# Supervisor

[Supervisor](http://supervisord.org/) - программулина для управления процессами.

### Установка

Устанавливаем как питон-пакет:

```text
pip install supervisor
```

Создаем конфиг, в котором, помимо настроек Supervisor, будут описываться управляемые процессы:

```text
echo_supervisord_conf >  /etc/supervisord.conf
```

### Создание процесса

Чтобы добавить процесс, необходимо определить секцию program в `supervisord.conf`:

```text
[program:{program_name}]
process_name = {program_name}
command={command}
directory={directory}

redirect_stderr=true
user=root
autorestart=true
```

* program\_name - название процесса
* command - выполняемая команда
* directory - рабочая директория

Больше опций [здесь](http://supervisord.org/configuration.html).

Часто веб-фреймворки сами предоставляют инструкции для запуска. Помимо запуска с помощью Supervisor, описывается также nginx-проксирование, например [aiohttp](https://aiohttp.readthedocs.io/en/stable/deployment.html), [gunicorn](http://docs.gunicorn.org/en/latest/deploy.html).

#### Выполняемая команда

Питон программы принято запускать из виртуальной среды. Чтобы не прописывать путь к виртуальной среде, можно воспользоваться [pipenv](https://docs.pipenv.org/):

```text
pipenv run python scrip.py
```

Полный путь к запускаемому скрипту писать не нужно, так как рабочая директория определена в `supervisord.conf`.

### Запуск и мониторинг процессов

После добавления или изменения процессов, необходимо считать изменения конфига и перезапустить процессы:

```text
sudo supervisorctl reread
sudo supervisorctl update
```

Для просмотра активных процессов:

```text
sudo supervisorctl status
```

Для перезапуска конкретного процесса:

```text
sudo supervisorctl restart {process_name}
```

Если запущено несколько процессов \(опция numprocs\), то необходимо перезапустить группу процессов:

```text
sudo supervisorctl restart {process_name}:*
```

#### Логи

Логи supervisor находятся по такому пути: `/var/log/supervisor` - там же можно посмотреть почему процесс имеет статус FATAL

### Если все плохо

Если возникают ошибки, типа:

```text
unix:///var/run/supervisor.sock no such file
```

Или что-нибудь такое:

```text
error: <class 'socket.error'>, [Errno 2] No such file or directory: file: /usr/lib/python2.7/socket.py line: 224
```

То, проще всего, перезапустить supervisor:

```text
sudo supervisord
```

