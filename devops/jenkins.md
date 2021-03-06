# Jenkins

[Jenkins](https://jenkins.io/) - популярный CI-сервер.

CI - континиус интегрейшен, процесс сливания изменений в одну ветку и осуществление сборки, например, в случае питона - прогон тестов, сборка докер-контейнера, и т.д.

CI - это удобно, потому что разрабу достаточно сделать пуш в репу, CI-сервер при обнаружении коммита сам проведет сборку и деплой, а в случае неудачи пришлет письмо с ошибками.

Основы Jenkins можно посмотреть [тут](https://www.youtube.com/playlist?list=PLzvRQMJ9HDiSaisKr7OnM4Fl7JXCDDcmt).

### Установка

На офф сайте довольно [адекватная инструкция](https://jenkins.io/doc/book/installing), все без проблем встает.

После установки, Jenkins будет запушен на 8080 порту, и выглядит примерно так:

![https://jenkins.io/images/post-images/2018-01-15-JENKINS-43786/JENKINS-43786\_2.png](https://jenkins.io/images/post-images/2018-01-15-JENKINS-43786/JENKINS-43786_2.png)

#### Плагины

Для Jenkins’а создано куча плагинов, например:

* [Bitbucket Plugin](https://wiki.jenkins.io/display/JENKINS/BitBucket+Plugin) - интеграция с Bitbucket, включая триггер сборки при пуше
* [Email-ext plugin](https://wiki.jenkins.io/display/JENKINS/Email-ext+plugin) - отправка писем с помощью сторонних сервисов, типа Google SMTP
* [EnvInject Plugin](https://wiki.jenkins.io/display/JENKINS/EnvInject+Plugin) - для работы с переменными среды

Приведенные выше плагины **должны быть установлены** для прохождения туториала, установить их и другие можно в `{jenkins-host-url}/pluginManager/`

Примечание

`{jenkins-host-url}` - адрес сервера, где запущен Jenkins, с указанием порта.

### Создание item’a

Item - описание процесса сборки.

Создание Item’а осушестляется в `{jenkins-host-url}/view/all/newJob/`, тип можно оставить дефолтовым.

#### Конфигурация репозитория

Сборка начинается с клонирования репозитория, первым делом, нужно его настроить.

В случае с публичным репозиторием достаточно указать адрес репозитория, для приватного репозитория необходимо также создать на сервере ssh-ключи для юзера jenkins:

```text
sudo su jenkins
ssh-keygen
```

В результате выполнения команд, получаем публичный ключ, который необходимо скопировать:

```text
cat /var/lib/jenkins/.ssh/id_rsa.pub
```

В настройках Bitbucket-репозитория в разделе _Access Keys_ создаем новый ключ со скопированным содержимым.

Для того, чтобы уведомлять Jenkins о новых коммитах, в разделе Webhooks создаем хук на `{jenkins-host-url}/bitbucket-hook/`.

Аналогичное делаем в Jenkins, для этого переходим в `{jenkins-host-url}/credentials/store/system/domain/_/`, создаем _Credentials_ с типом _SSH Username with private key_, и приватным ключом в _~/.ssh_.

Возвращаемся в создание Item, созданную учетную запись добавляем в соответствующее поле в настройке репозитория.

#### Конфигурация сборки

Помимо указания репозитория, для сборки необходимо определить следующее:

* **Триггеры сборки** - ставим флаг _Build when a change is pushed to BitBucket_ для запуска сборки при коммите
* **Создание среды** - здесь определяем настройки среды, такие как переменные окружения
* **Сборка** - набор команд, успешное выполнение которых говорит об успешной сборке, примеры комманд - установка зависимостей, запуск тестов
* **Послесборочные операции** - уведомление по почте, присвоение статуса сборки коммиту

После сохранения Item’а можем запустить сборку вручную или стригерить ее коммитом.  


