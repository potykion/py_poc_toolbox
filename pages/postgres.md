# Postgres

**Postgres** - популярная реляционная БД

### Установка

Устанавливаем БД: [https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04)

Ставим пароль: [https://serverfault.com/questions/110154/whats-the-default-superuser-username-password-for-postgres-after-a-new-install](https://serverfault.com/questions/110154/whats-the-default-superuser-username-password-for-postgres-after-a-new-install)

Разрешаем БД админу - **postgres** аутентифицироваться:[https://stackoverflow.com/questions/18664074/getting-error-peer-authentication-failed-for-user-postgres-when-trying-to-ge](https://stackoverflow.com/questions/18664074/getting-error-peer-authentication-failed-for-user-postgres-when-trying-to-ge)

Для аутентификации передаем имя юзера и название БД: `psql -U postgres {db_name}`

Чтобы не вводить каждый раз пароль, можно сделать переменную среры - `PGPASSWORD`

Настраиваем БД для внешних соединений: [https://blog.bigbinary.com/2016/01/23/configure-postgresql-to-allow-remote-connection.html](https://blog.bigbinary.com/2016/01/23/configure-postgresql-to-allow-remote-connection.html)

Открывать БД для внешних соединений необязательно, но если открыть, то можно удобно просматривать ее содержимое, например, с помощью _Database view_ в PyCharm.

### Содержимое БД

`\l` - список созданных БД

`\connect {db_name}` - смена БД

`\dt` - список таблиц БД

`\d+ {table_name}` - столбцы таблицы[Next ](https://py-poc-toolbox.readthedocs.io/ru/latest/pages/packaging.html)[ Previous](https://py-poc-toolbox.readthedocs.io/ru/latest/pages/gino.html)  


