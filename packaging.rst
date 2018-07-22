Создание PyPI пакетов
######################

`PyPI`_ (Python Package Index) - хранилище питон-либ, установка которых производится с помощью ``pip install``.

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

setup.py
========

- Если нужно добавить зависимости от других пакетов, необходимо прописать настройку ``install_requires`` в функции ``setuptools.setup`` в формате **"{package}{operator}{version}"** (например, ``"jinja2>=2.10"``)


.. _PyPI: https://pypi.org
.. _здесь: https://packaging.python.org/tutorials/packaging-projects/
.. _twine: https://github.com/pypa/twine
.. _тестовый PyPI: https://test.pypi.org
.. _правилам версионирования пакетов: https://semver.org