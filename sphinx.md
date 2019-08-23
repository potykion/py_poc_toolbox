# Sphinx

[Sphinx ](http://www.sphinx-doc.org/en/master/)- генератор документации, например [такой ](https://flask.palletsprojects.com/en/1.1.x/)и [сякой](https://docs.python.org/2.7/)

### Установка и сборка

Для установки и создания проекта с доками:

```text
pip install sphinx
sphinx-quickstart
```

После выполнения команд, в директории проекта появятся следующие файлы:

1. `conf.py` - конфиг доков
2. `index.rst` - главная страница доков, содержащая список всех доков проекта
3. `Makefile` - файл для рендеринга доков в html и другие форматы

Для сборки \(рендеринга\) доков в html:

```text
make html
```

Отрендеренные доки находятся в `/_build/html`

### Страницы

После установки, можно создавать страницы в формате `.rst`:

```text
<!-- guidelines.rst -->
Гайдлайны
================

Именование
***********

SQLAlchemy таблицы
-------------------

Таблицы именуются как модель во множественном числе, ссылка на таблицу - модель во множественном числе + постфикс ``_table``.

Например, для модели ``Venue`` описание таблицы выглядит таким образом:

.. code-block:: python

    venues_table = sa.Table(
        "venues",
        metadata,
        sa.Column("id", sa.BigInteger, primary_key=True, autoincrement=True),
        sa.Column("active", sa.Boolean, default=True),
        sa.Column("title", sa.Unicode),
    )
```

 и добавлять их в содержимое - `index.rst`:

```text
<!-- index.rst -->
Welcome to sample documentation!
=========================================

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   guidelines
```

### Синтаксис

Дефолтовый язык разметки в Sphinx - [reStructuredText](http://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html) \(rst\). 

Основное преимущество перед [Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) - расширяемость. Например, с помощью rst можно рисовать графики \([так](https://sphinxcontrib-mermaid-demo.readthedocs.io/en/latest/) и [сяк](https://www.sphinx-doc.org/en/master/usage/extensions/graphviz.html)\), и [документировать API](https://sphinxcontrib-httpdomain.readthedocs.io/en/stable/).

Markdown так же можно использовать в качестве языка разметки документов - для этого нужно поставить [расширение для базовой поддержки](http://www.sphinx-doc.org/en/master/usage/markdown.html) + если нужны таблицы, то [расширение для рендеринга таблиц](https://pypi.org/project/sphinx-markdown-tables/).

### Поддержка в IDE

#### **PyCharm**

Подсветка rst/md из коробки, но рендеринг русского текса хромает:

![](.gitbook/assets/image.png)

#### **VSCode**

Ставим [расширение](https://marketplace.visualstudio.com/items?itemName=lextudio.restructuredtext), и получаем не такую богатую подсветку, как в PyCharm, зать получаем отрендеренное превью с учетом ссылок, темы и прочего:

![](.gitbook/assets/image%20%281%29.png)

В rst определение таблиц сложноватое:

```text
+------------+------------+-----------+
| Header 1   | Header 2   | Header 3  |
+============+============+===========+
| body row 1 | column 2   | column 3  |
+------------+------------+-----------+
| body row 2 | Cells may span columns.|
+------------+------------+-----------+
| body row 3 | Cells may  | - Cells   |
+------------+ span rows. | - contain |
| body row 4 |            | - blocks. |
+------------+------------+-----------+
```

+ при расширении колонки, нужно перерисовывать всю таблицу заново 

Для генерации и выравнивания разметки таблицы есть специальное расширение - [Table Formatter](https://marketplace.visualstudio.com/items?itemName=shuworks.vscode-table-formatter)

### Темы

* Темы из [коробки](https://www.sphinx-doc.org/en/master/usage/theming.html#builtin-themes)
* Как в [ReadTheDocs](https://sphinx-rtd-theme.readthedocs.io/en/stable/)
* Тысячи [их](https://sphinx-themes.org)

### Видосы

* [Carol Willing - Practical Sphinx - PyCon 2018](https://www.youtube.com/watch?v=0ROZRNZkPS8) - хороший вводный видос: установка, рендеринг разных форматов, обзор расширений, директив, воркфлоу по написанию доков
* \*\*\*\*[Документирование проектов на Python с помощью Sphinx / Николай Карелин \[Python Meetup 24.06.2016\]](https://www.youtube.com/watch?v=xNSf1g4O4RU) - описание работы sphinx, rst-синтаксиса, директив

### Еще

{% embed url="https://github.com/yoloseem/awesome-sphinxdoc" %}

