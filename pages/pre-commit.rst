pre-commit
##########

`pre-commit`_ - либа для написания pre-commit хуков.

Pre-commit хуки - команды, которые запускаются до совершения коммита; пока выполнение команд не завершится успехом, коммит производиться не будет.

Примеры команд: прогон тестов, линтинг, проверка типов.

Установка
**********

1. Установить либу: ``pip install pre-commit``

2. Создать конфиг: ``.pre-commit-config.yaml``

3. Включить хуки: ``pre-commit install``

Пример конфига
**************

.. code-block:: yaml

    fail_fast: true # при неудачном запуске хука, последующие хуки не выполняются
    repos:
    # прогон тестов с помощью pytest
    -   repo: local # хук находится на локальной машине
        hooks:
        - id: pytest
          name: pytest
          entry: pipenv run pytest
          args: [tests, -x]
          pass_filenames: false # передача файлов репозитория как параметра - отключено
          language: system # использование интерпретатора, установленного на машине, в данном случае pipenv-среда

    # проверка типов с помощью mypy
    -   repo: local
        hooks:
        - id: mypy
          name: mypy
          entry: pipenv run mypy
          args: [--ignore-missing-imports, --disallow-untyped-defs, {package_name}] # todo указать название пакета, для которого запускать mypy
          pass_filenames: false
          language: system

    # линтинг с помощью flake8
    -   repo: https://github.com/pre-commit/pre-commit-hooks
        rev: v1.3.0
        hooks:
        - id: flake8

    # форматирование с помощью black
    -   repo: https://github.com/psf/black
        rev: stable
        hooks:
        - id: black
          language_version: python3.6

Запуск
******

Хуки запускаются автоматом при коммите, для запуска хуков вручную - ``pre-commit run -a``

.. _pre-commit: https://pre-commit.com
.. _black: https://github.com/psf/black
.. _pytest: https://pytest.org
.. _pipenv: https://github.com/pypa/pipenv
