# Питон-пакеты и PyPI

[PyPI](https://pypi.org/) \(Python Package Index\) - хранилище питон-либ, установка которых производится с помощью `pip install`.

PyPI пакет - python-пакет - директория с файлом \_\_init\_\_.py

### Создание пакета

Основные шаги прописаны [здесь](https://packaging.python.org/tutorials/packaging-projects/):

1. Создаем репозиторий
2. Создаем питон-пакет - директория с **\_\_init\_\_.py** файлом
3. Создаем доп-файлы:

   > * **README.md** - описание проекта
   > * **setup.py** - модуль с описанием установки пакета для PyPI
   > * **LICENSE** - лицензия, обычно MIT

4. Ставим [twine](https://github.com/pypa/twine)
5. Собираем все в архив: `python .\setup.py sdist bdist_wheel`
6. Заливаем в PyPI: `twine upload dist/*`

   > * Для загрузки в [тестовый PyPI](https://test.pypi.org/): `twine upload --repository-url https://test.pypi.org/legacy/ dist/*`
   > * Чтобы не вводить постоянно логин/пароль от PyPI, можно установить переменные среды `TWINE_USERNAME`, `TWINE_PASSWORD`

7. После загрузки на PyPI, пакет можно установить с помощью `pip install`

#### Еще доп файлы

* **MANIFEST.in** - для добавления непитон файлов, таких как jinja2-шаблонов, в пакет, пример: `include {package}/templates/*`
* **CHANGELOG.md** - описание изменений в новых версиях пакета \([формат](https://keepachangelog.com/en/1.0.0/)\). Версионирование пакетов хорошо делать соглано [правилам версионирования пакетов](https://semver.org/).

### Обновление версии

Для автоматизации версионирования пакетов можно использовать [bump2version](https://github.com/c4urself/bump2version):

1. Устанавливаем:

```text
pip install bump2version
```

1. Создаем конфиг - .bumpversion.cfg:

```text
[bumpversion]
current_version = 0.2.0
commit = True
tag = True

[bumpversion:file:setup.py]
```

1. Обновляем версию:

```text
bump2version patch
```

#### setup.py

* Если нужно добавить зависимости от других пакетов, необходимо прописать настройку `install_requires` в функции `setuptools.setup` в формате **«{package}{operator}{version}»**\(например, `"jinja2>=2.10"`\)

### Приватный PyPI

#### Установка сервера

Для того, чтобы залить пакет на приватный PyPI \(например, если пакет содержит бизнес инфу, которую не надо шарить за пределы работки\), необходимо поднять приватный PyPI:

1. Создаем директорию, где будут находится пароль от pypi и сами пакеты:

```text
mkdir mypypi && cd mypypi
```

1. Устанавливаем [PyPI сервер](https://github.com/pypiserver/pypiserver) с [passlib-зависимостью](https://passlib.readthedocs.io/en/stable/), необходимой для доступа к PyPI с паролем:

```text
pipenv install pypiserver[passlib]
```

1. Устанавливаем [htpasswd](https://httpd.apache.org/docs/2.4/en/programs/htpasswd.html) для создания пароля:

```text
sudo apt-get install apache2-utils
```

1. Создаем файл с паролем и заполняем его для юзера, по которому мы будем стучаться на PyPI \(например, pypi\) :

```text
touch pypipass.txt && htpasswd pypi
```

1. Создаем директорию ддя пакетов:

```text
mkdir packages
```

1. Можем запустить pypi-сервер:

```text
pipenv run pypi-server --port 8090 -i 127.0.0.1 -P pypipass.txt -a update,download,list -r packages
```

1. Если команда ничего не выводит, значит все ок, и теперь можно создать [supervisor-программу](https://py-poc-toolbox.readthedocs.io/ru/latest/pages/supervisor.html):

```text
[program:mypypi]
command=pipenv run pypi-server --port 8090 -i 127.0.0.1 -P pypipass.txt -a update,download,list -r packages
directory=/root/mypypi
user=root
autorestart=true
```

1. Далее создаем nginx-директиву:

```text
server {
    listen 8091;
    location / {
            proxy_pass http://localhost:8090/;
    }
}
```

1. Теперь, перейдя по {айпи, где крутится nginx}:8091, увидим сообщение о развернутом pypi-сервере:

![https://raw.githubusercontent.com/potykion/py\_poc\_toolbox/master/\_static/pypi.PNG](https://raw.githubusercontent.com/potykion/py_poc_toolbox/master/_static/pypi.PNG)

#### Загрузка пакетов

Для загрузки пакетов на pypi-сервер, необходимо указать его адрес с логином и паролем:

```text
twine upload --repository-url http://{user}:{password}@{айпи, где крутится nginx}:8091 dist/*
```

Или адрес без логина и пароля с установленными переменными среды: `TWINE_USERNAME`, `TWINE_PASSWORD`

#### Стягивание пакетов

Для скачивания пакетов необходимо добавить ссылку на pypi-сервер. В случае с pipenv, необходимо в Pipfile добавить директиру source:

```text
[[source]]
url = "http://{user}:{password}@{айпи, где крутится nginx}:8091"
verify_ssl = false
name = "mypypi"
```

Чтобы не палить логин и пароль в Pipfile, можно задать адрес сервера в виде переменной среды и заменить урл в Pipfile:

```text
[[source]]
url = "${MYPYPI_URL}"
verify_ssl = false
name = "mypypi"
```

Теперь можно ставить пакеты как обычно:

```text
pipenv install {package-name}
```

### Poetry

Вместо создания setup.py, сборку через sdist, деплой через twine, ункремент через bump2version можно использовать Poetry

[Poetry](https://poetry.eustace.io/) - альтернатива Pipenv и тулза для создания питон-пакетов

#### Создание пакетов

1.a Создаем pyproject.toml - микс Pipfile и setup.py:

```text
poetry init
```

1.b Для установки зависимостей из существующего pyproject.toml:

```text
poetry install
```

1. Устанавливаем новые зависимости:

```text
poetry add {package_name}
```

1. Собираем и загружаем в PyPI:

```text
poetry publish --build
```

1. Инкремент версии

```text
poetry version (patch|minor|major)
```

#### Загрузка в приватный PyPI

Добавление приватного репозитория {host:port}, с именем {pypi\_alias}:

```text
poetry config repositories.{pypi_alias} {host:port}
```

Добавление логина/пароля для доступа в приватный репо:

```text
poetry config http-basic.{pypi_alias} {username} {password}
```

Загрузка пакета:

```text
poetry publish -r {pypi_alias} --build
```

