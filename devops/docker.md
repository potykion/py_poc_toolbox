# Docker



[Docker](https://www.docker.com/) - штука для контейнеризации ПО

Контейнеризация - засовывание ПО в контейнеры \(спасибо кэп\) - мини-виртуалки

Таким образом, можно ставить себе программулины в пару команд или можно упаковывать свои приложухи и запускать их на любой машине

### Установка

Если есть Win10 Pro, Enterprise or Education, то Docker ставится через [инсталлер](https://docs.docker.com/docker-for-windows/install/)

Иначе, если обычная Win10, - через [DockerToolbox](https://docs.docker.com/toolbox/toolbox_install_windows/):

* Ставим DockerToolbox
* Запускаем Docker Quickstart Terminal
* [Делаем DockerMachine с 4 гб оперативы](https://github.com/crops/docker-win-mac-docs/wiki/Windows-Instructions-%28Docker-Toolbox%29) - чтобы запускать большинство программулин

На Linux ставится по [интрукции](https://docs.docker.com/install/linux/docker-ce/ubuntu/), или если речь идет о серваке, то хостинги \(например, reg.ru и DigitalOcean\) предоставляют машинки с предустановленным докером:

![reg.ru](../.gitbook/assets/image%20%289%29.png)

### Запуск контейнера

Создатели ПО часто сами предоставляют гайды по запуску своего ПО через Docker, например [MSSQL](https://docs.microsoft.com/ru-ru/sql/linux/quickstart-install-connect-docker?view=sql-server-2017)

Но обычно все сводится к нескольким шагам:

* Выкачивание имеджа \(образа\) с помощью команды: `docker pull`
* Запуск контейнера со скачанным имеджом: `docker run`

Все - контейнер запущен, можно юзать ПО на `localhost` или на `192.168.99.100` под виндой

#### docker run

[Данная команда](https://docs.docker.com/engine/reference/run/) запускает контейнер, имеет множество опций:

* `-d` - запуск контейнера в фоновом режиме / режиме демона
* `--name={name}` - установка названия контейнера, чтобы легче было с ним взаимодействовать
* `--restart = always/no/…` - установка перезапуска контейнера при, например, краше или перезапуске системы
* `-p 8089:80` - открывает порт контейнера \(в данном случае 80\) для порта машинки \(8089\). Полезно, когда запускается nginx-контейнер, который по умолчанию смотрит на 80, а на машине этот порт занят и нужно открыть другой порт контейнеру.

### Создание собственных контейнеров

Для создания собственного контейнера необходимо создать Dockerfile. 

Ниже приведен пример создания nginx-контейнера, который сервит sphinx-документацию:

```text
# делаем контейнер с python
FROM python as build

# ставим poetry
RUN curl -sSL https://raw.githubusercontent.com/sdispater/poetry/master/get-poetry.py | python
ENV PATH="/root/.poetry/bin:${PATH}"

# создаем директорию для файлов контейнера
WORKDIR /app

# копируем poetry-файлы, для кеширования зависимостей
COPY pyproject.toml poetry.lock /app/

# ставим зависимости
RUN poetry install

# копируем оставшиеся файлы
COPY . /app

# собираем доки
RUN poetry run make html

# делаем контейнер с nginx для серва статики
FROM nginx

# копируем собранные доки из python-контейнера в nginx-контейнер
COPY --from=build /app/_build/html /usr/share/nginx/html
```

Когда Dockerfile написан, необходимо запустить сборку контейнера, с помощью команды `docker build`

### Еще команды

Список всех команд отображается при запуске команды `docker`

Наиболее частые:

* `docker ps` - вывод запущенных контейнеров
* `docker stop` / `docker rm` - остановка и удаления контейнера
* `docker stats` - вывод потребляемых контейнерами ресурсов

### Деплой 

Деплой осуществляется в несколько этапов:

* Сборка нового образа: `docker build`
* Остановка старого контейнера: `docker stop`
* Удаление старого контейнера: `docker rm`
* Запуск нового контейнера: `docker run`

Пример полного скрипта:

```text
# не забываем стягивать мастер
git pull
docker build -t {название_сборки} .
docker stop {название_запущеного_контейнера}
docker rm {название_запущеного_контейнера}
docker run --name {название_запущеного_контейнера} -p 8079:80 \
  -d --restart unless-stopped {название_сборки}
```



