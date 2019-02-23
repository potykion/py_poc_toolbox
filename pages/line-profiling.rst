line_profiler
############################

`line_profiler`_ - либа для построчного профилирования.

Профилирование - подсчет метрик программы во время ее выполнения;
посторочное профилирование - подсчет метрик для каждой строки программы.

Примеры метрик: время выполнения, потребляемая память.


Установка
***********

.. code-block:: shell

    pip install line_profiler


Профилирование
***************

Профилирование можно осуществлять посредством запуска профилируемого модуля с помощью ``kernprof`` или
посредством создания объекта ``LineProfiler`` и передачи ему профилируемой функции.

kernprof
========

В качестве примера использования ``kernprof``, проведем профилирование теста.

Декорируем профилируемую функцию с помощью ``@profile``, дополнительных импортов не требуется.

Создаем модуль, запускающий тесты, в функцию ``pytest.main`` передаем путь к тесту:

.. code-block:: python

    import pytest

    pytest.main("{path_to_test}")

Запускаем тест, передавая на вход созданный модуль:

.. code-block:: shell

    kernprof -v -l {module}.py

Флаг ``-v`` отображает результаты профилирования сразу после завершения модуля.

Пример вывода:

.. code-block:: text

    Line #      Hits         Time  Per Hit   % Time  Line Contents
    ==============================================================
         5                                           @profile
         6                                           async def test_sync_actresses_without_videos(loop, sync_db, actress_without_videos, vk_token):
         7         1      51289.0  51289.0     63.7      assert sync_db.actresses.find({'has_videos': False}).count() == len(actress_without_videos)
         8
         9         1         21.0     21.0      0.0      with patch('rptp.getters.get_videos',
        10         1       3071.0   3071.0      3.8                 CoroutineMock(return_value=[{'url': 'https://vk.com/video150323989_162611387'}])):
        11         1      18104.0  18104.0     22.5          await sync_actresses_without_videos(sync_db, vk_token)
        12
        13         1       7988.0   7988.0      9.9      assert sync_db.actresses.find({'has_videos': False}).count() == 0

После запуска модуля также появится файл ``{module}.py.lprof``, содержащий результаты профилирования,
для просмотра используется ``line_profiler``:

.. code-block:: shell

    python -m line_profiler {module}.py.lprof


Источник: https://stackoverflow.com/a/23765051/5500609

LineProfiler
===============

Для профилирования с помощью ``LineProfiler`` определим декоратор,
создающий экземпляр класса и сохраняющий результаты профилирования:

.. code-block:: python

    from line_profiler import LineProfiler

    def profile(func):
        def wrapper(*args, **kwargs):
            profiler = LineProfiler(func)
            profiler.enable_by_count()
            result = func(*args, **kwargs)
            profiler.dump_stats(f'{func.__name__}.lprof')
            return result

После декорирования и запуска профилируемых функций, сгенерируются файлы с результами.
Для просмотра - снова используем ``line_profiler``.

Источник: https://zapier.com/engineering/profiling-python-boss/

.. _line_profiler: https://github.com/rkern/line_profiler
