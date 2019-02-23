# Sphinx 

[Sphinx](http://www.sphinx-doc.org/en/master/) - штука для генерации доков, например [этих](https://py-poc-toolbox.readthedocs.io/ru/latest/index.html) и [таких](https://docs.python.org/2.7/)

## Установка и сборка

Для установки и создания проекта с доками:

```shell
pip install sphinx
sphinx-quickstart
```

После выполнения команд, в директории проекта появятся следующие файлы:

1. `conf.py` - конфиг доков
2. `index.rst` - главная страница доков, содержащая список всех доков проекта
3. `Makefile` - файл для рендеринга доков в html и другие форматы

Для сборки (рендеринга) доков в html:

```
make html
```

Отрендеренные доки находятся в `/_build`

## Страницы

После установки, можно создавать доки в формате .rst и добавлять их в содержимое:

```rst
<!-- index.rst -->
Welcome to sample documentation!
=========================================

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   page
```

- [Синтаксис rst](http://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html)

## Поддержка markdown

- [Основа](http://www.sphinx-doc.org/en/master/usage/markdown.html)
- [Рендеринг таблиц](https://pypi.org/project/sphinx-markdown-tables/)

## Темы 

- [ReadTheDocs](https://sphinx-rtd-theme.readthedocs.io/en/stable/)