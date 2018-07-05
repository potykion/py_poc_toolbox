GINO
#######

`GINO`_ - асинхронная ORM для Postrgres с SQLAlchemy-синтаксисом.

Модели
*******

Для создания модели необходимо создать ссылку на БД:

.. code-block:: python

    from gino import Gino
    db = Gino()

Далее определяем саму модель:

.. code-block:: python3

    class News(db.Model):
        __tablename__ = "news"

        id: int = db.Column(db.Integer(), primary_key=True)
        title: str = db.Column(db.Unicode())
        text: str = db.Column(db.Unicode(), required=True)
        image_url: str = db.Column(db.Unicode())

        created: datetime = db.Column(db.DateTime(), default=datetime.now)
        start: datetime = db.Column(db.DateTime())

        status: int = db.Column(db.Integer(), default=0)
        repeat_count: int = db.Column(db.Integer(), default=1)
        repeat_every: int = db.Column(db.Integer(), default=1)

        venues: List[int] = db.Column(db.ARRAY(db.Integer()))


Для опредления столбца используется класс ``Column`` -
в него передается `SQLAlchemy-тип`_, а также `дополнительные параметры`_.

Для создания таблицы в БД, необходимо подключиться к БД и создать таблицы с помощью ``create_all``:

.. code-block:: python3

        await db.set_bind(f"postgres://{user}:{password}@{host}/{db}")
        await db.gino.create_all()


.. _GINO: https://github.com/fantix/gino
.. _дополнительные параметры: http://docs.sqlalchemy.org/en/latest/core/metadata.html#sqlalchemy.schema.Column
.. _SQLAlchemy-тип: http://docs.sqlalchemy.org/en/latest/core/type_basics.html

