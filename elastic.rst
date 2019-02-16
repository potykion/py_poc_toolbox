ElasticSearch
##############

`ElasticSearch <https://www.elastic.co/>`_ - поисковая NoSQL-бд


Установка
***********

Через `Docker <https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html>`_:

.. code-block:: shell

    docker run -p 9200:9200 -p 9300:9300 -d --name es -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:6.4.3

Иначе `так <https://www.elastic.co/guide/en/elasticsearch/reference/current/install-elasticsearch.html>`_

Конфигурация для supervisor:

.. code-block:: shell

    [program:elasticsearch]
    user=elasticsearch
    command=/usr/share/elasticsearch/bin/elasticsearch
    autostart=true
    autorestart=true
    environment=ES_HEAP_SIZE=2g

Помимо ElasticSearch, полезно поставить `Kibana <https://www.elastic.co/products/kibana>`_ - веб-апп для просмотра содержимого бд

Ставится `так <https://www.elastic.co/downloads/kibana>`_

Конфигурация для supervisor:

.. code-block:: shell

    [program:kibana]
    user=root
    command=/usr/share/kibana/bin/kibana
    autostart=true
    autorestart=true

Статус
***********

Статус elasticsearch можно посмотреть так:

.. code-block:: shell

    curl -X GET "localhost:9200/_cluster/health"

Описание метрик можно найти `тут <https://habr.com/ru/company/yamoney/blog/358550/>`_

status: yellow && unassigned_shards > 0
================================================

При запуске elasticsearch на одной машине без репликации появляются неназначенные шарды

Чтобы убрать их, нужно обнулить количество реплик для каждого индекса:

.. code-block:: shell

    curl -X PUT "localhost:9200/*/_settings" -H 'Content-Type: application/json' -d'{"index" : {"number_of_replicas" : 0}}'

После выполнения этой команды, статус должен измениться на зеленый, а количество неназначенных шардов должно быть равно 0


API
***

Если нужно просто вставлять записи (например, логи), то достаточно юзать `лоулевел-клиент <https://elasticsearch-py.readthedocs.io/en/master/>`_:

.. code-block:: python

    # pip install elasticsearch

    from elasticsearch import Elasticsearch
    es = Elasticsearch()

    # индекс - местный аналог бд, создается автоматически
    index = "test_index"

    # док-тайп - местные таблицы, устарели с версии 6+, так что можно юзать 1 на индекс
    doc_type = "test_doc_type"

    # бд - документноориентированная, можно сувать любой словарь
    doc = {
        'author': 'kimchy',
        'text': 'Elasticsearch: cool. bonsai cool.',
        'timestamp': datetime.now(),
    }

    # суем в ES
    res = es.index(index=index, doc_type=doc_type, body=doc)

Док появляется в индексе, далее выбираем индекс в Kibana:

.. code-block:: shell

    Management > Index Patterns > Create Index Pattern > {index_name}

и все - можно анализировать записи

Если нужно делать запросы к индексам в коде, то юзаем `хайлевел-клиент <https://elasticsearch-dsl.readthedocs.io/en/latest/>`_
