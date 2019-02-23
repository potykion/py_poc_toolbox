Питон-пакеты и PyPI
############################################

`PyPI`_ (Python Package Index) - хранилище питон-либ, установка которых производится с помощью ``pip install``.

PyPI пакет - python-пакет - директория с файлом `__init__.py`

Создание пакета
***************

Основные шаги прописаны `здесь`_:

1. Создаем репозиторий

2. Создаем питон-пакет - директория с **__init__.py** файлом

3. Создаем доп-файлы:

    - **README.md** - описание проекта
    - **setup.py** - модуль с описанием установки пакета для PyPI
    - **LICENSE** - лицензия, обычно MIT

4. Ставим `twine`_

5. Собираем все в архив: ``python .\setup.py sdist bdist_wheel``

6. Заливаем в PyPI: ``twine upload dist/*``

    - Для загрузки в `тестовый PyPI`_: ``twine upload --repository-url https://test.pypi.org/legacy/ dist/*``
    - Чтобы не вводить постоянно логин/пароль от PyPI, можно установить переменные среды ``TWINE_USERNAME``, ``TWINE_PASSWORD``

7. После загрузки на PyPI, пакет можно установить с помощью ``pip install``

Еще доп файлы
==============

- **MANIFEST.in** - для добавления непитон файлов, таких как jinja2-шаблонов, в пакет, пример: ``include {package}/templates/*``

- **CHANGELOG.md** - описание изменений в новых версиях пакета (`формат <https://keepachangelog.com/en/1.0.0/>`_). Версионирование пакетов хорошо делать соглано `правилам версионирования пакетов`_.


Обновление версии
*****************

Для автоматизации версионирования пакетов можно использовать `bump2version <https://github.com/c4urself/bump2version>`_:

1. Устанавливаем:

.. code-block:: shell

    pip install bump2version

2. Создаем конфиг - `.bumpversion.cfg`:

.. code-block:: ini

    [bumpversion]
    current_version = 0.2.0
    commit = True
    tag = True

    [bumpversion:file:setup.py]

3. Обновляем версию:

.. code-block:: shell

    bump2version patch

setup.py
========

- Если нужно добавить зависимости от других пакетов, необходимо прописать настройку ``install_requires`` в функции ``setuptools.setup`` в формате **"{package}{operator}{version}"** (например, ``"jinja2>=2.10"``)


Приватный PyPI
**************************


Установка сервера
=========================

Для того, чтобы залить пакет на приватный PyPI (например, если пакет содержит бизнес инфу, которую не надо шарить за пределы работки),
необходимо поднять приватный PyPI:

1. Создаем директорию, где будут находится пароль от pypi и сами пакеты:

.. code-block:: shell

    mkdir mypypi && cd mypypi

2. Устанавливаем `PyPI сервер`_ с `passlib-зависимостью`_, необходимой для доступа к PyPI с паролем:


.. code-block:: shell

    pipenv install pypiserver[passlib]

3. Устанавливаем `htpasswd`_ для создания пароля:

.. code-block:: shell

    sudo apt-get install apache2-utils

4. Создаем файл с паролем и заполняем его для юзера, по которому мы будем стучаться на PyPI (например, pypi) :

.. code-block:: shell

    touch pypipass.txt && htpasswd pypi

5. Создаем директорию ддя пакетов:

.. code-block:: shell

    mkdir packages

6. Можем запустить pypi-сервер:

.. code-block:: shell

    pipenv run pypi-server --port 8090 -i 127.0.0.1 -P pypipass.txt -a update,download,list -r packages

7. Если команда ничего не выводит, значит все ок, и теперь можно создать :doc:`supervisor-программу <supervisor>`:

.. code-block:: ini

    [program:mypypi]
    command=pipenv run pypi-server --port 8090 -i 127.0.0.1 -P pypipass.txt -a update,download,list -r packages
    directory=/root/mypypi
    user=root
    autorestart=true

8. Далее создаем nginx-директиву:

.. code-block:: nginx

    server {
        listen 8091;
        location / {
                proxy_pass http://localhost:8090/;
        }
    }

9.  Теперь, перейдя по `{айпи, где крутится nginx}:8091`, увидим сообщение о развернутом pypi-сервере:

.. image:: https://raw.githubusercontent.com/potykion/py_poc_toolbox/master/_static/pypi.PNG

Загрузка пакетов
====================

Для загрузки пакетов на pypi-сервер, необходимо указать его адрес с логином и паролем:

.. code-block:: shell

    twine upload --repository-url http://{user}:{password}@{айпи, где крутится nginx}:8091 dist/*

Или адрес без логина и пароля с установленными переменными среды: ``TWINE_USERNAME``, ``TWINE_PASSWORD``

Стягивание пакетов
====================

Для скачивания пакетов необходимо добавить ссылку на pypi-сервер. В случае с pipenv, необходимо в Pipfile добавить директиру source:

.. code-block:: toml

    [[source]]
    url = "http://{user}:{password}@{айпи, где крутится nginx}:8091"
    verify_ssl = false
    name = "mypypi"


Чтобы не палить логин и пароль в Pipfile, можно задать адрес сервера в виде переменной среды и заменить урл в Pipfile:

.. code-block:: toml

    [[source]]
    url = "${MYPYPI_URL}"
    verify_ssl = false
    name = "mypypi"

Теперь можно ставить пакеты как обычно:

.. code-block:: shell

    pipenv install {package-name}


Poetry
***********

Вместо создания setup.py, сборку через sdist, деплой через twine, ункремент через bump2version можно использовать Poetry

`Poetry <https://poetry.eustace.io/>`_ - альтернатива Pipenv и тулза для создания питон-пакетов

Создание пакетов
=============================================

1.a Создаем pyproject.toml - микс Pipfile и setup.py:

.. code-block:: shell

    poetry init

1.b Для установки зависимостей из существующего pyproject.toml:

.. code-block:: shell

    poetry install

2. Устанавливаем новые зависимости:

.. code-block:: shell

    poetry add {package_name}

3. Собираем и загружаем в PyPI:

.. code-block:: shell

    poetry publish --build

4. Инкремент версии

.. code-block:: shell

    poetry version (patch|minor|major)


Загрузка в приватный PyPI
=========================

Добавление приватного репозитория {host:port}, с именем {pypi_alias}:

.. code-block:: shell

    poetry config repositories.{pypi_alias} {host:port}


Добавление логина/пароля для доступа в приватный репо:

.. code-block:: shell

    poetry config http-basic.{pypi_alias} {username} {password}

Загрузка пакета:

.. code-block:: shell

    poetry publish -r {pypi_alias} --build




.. _PyPI: https://pypi.org
.. _здесь: https://packaging.python.org/tutorials/packaging-projects/
.. _twine: https://github.com/pypa/twine
.. _тестовый PyPI: https://test.pypi.org
.. _правилам версионирования пакетов: https://semver.org
.. _PyPI сервер: https://github.com/pypiserver/pypiserver
.. _passlib-зависимостью: https://passlib.readthedocs.io/en/stable/
.. _htpasswd: https://httpd.apache.org/docs/2.4/en/programs/htpasswd.html
