ElasticSearch
##############

`ElasticSearch <https://www.elastic.co/>`_ - поисковая NoSQL-бд


Установка
***********

Через `Docker <https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html>`_:

.. code-block:: shell

    docker run -p 9200:9200 -p 9300:9300 -d --name es -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:6.4.3

Иначе `так <https://www.elastic.co/guide/en/elasticsearch/reference/current/install-elasticsearch.html>`_

Помимо ElasticSearch, полезно поставить `Kibana <https://www.elastic.co/products/kibana>`_ - веб-апп для просмотра содержимого бд

Ставится `так <https://www.elastic.co/downloads/kibana>`_

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
