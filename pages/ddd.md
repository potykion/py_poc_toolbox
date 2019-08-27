# DDD

**DDD** - domain-driven-development - стиль разработки, при котором бизнес логика отделяется от кода приложения, таким образом разделяя систему на слои. Бизнес логика пишется максимально понятной для эксперта предметной области, код приложения лишь дергает эту бизнес логику, не зная ничего о реализации.

![](../.gitbook/assets/image%20%283%29.png)

### Составляющие DDD

![](../.gitbook/assets/image%20%285%29.png)

1. **Entities** - бизнес сущности, то вокруг чего пишутся бизнес сценарии
2. **Aggregates** - аггрегаты - сущности, из которых строятся Entities, например, для бизнес сущности Заказ: позиции заказа будут являтся аггрегатами
3. **Value Objects** - легковестные сущности, например, дата, является каркасом для аггрегатов
4. **Factories** - фабрики - аналогично паттернам Фабричный метод и Фабрика - классы, инкапсулирующие создание сущностей
5. **Services** - сервисы - здесь описываются бизнес сценарии
6. **Repositories** - репозитории - абстракция над любым персистенцем \(бд/celery-worker\); например, в контексте бд репозиторий преобразует бизнес-сущности в записи бд и сует их в бд

### Материалы

* [Борис Цема, Wargaming.net «Как мы уменьшили сложность наших проектов»](https://www.youtube.com/watch?v=45rk0iyTn7I) - видас про ddd в питоне
* \*\*\*\*[Алексей Мерсон — Domain-driven design: рецепт для прагматика](https://www.youtube.com/watch?v=CR9mLGN9jh0) - видас про ddd на c\#
* [Modular Monolith with DDD](https://github.com/kgrzybek/modular-monolith-with-ddd) - пример проекта с подробным описанием как все устроено
