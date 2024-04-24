# Установка Zabbix-server на Postgesql Ubuntu
***
### Установите репозиторий Zabbix 
```
# wget https://repo.zabbix.com/zabbix/6.4/ubuntu-arm64/pool/main/z/zabbix-release/zabbix-release_6.4-1+ubuntu22.04_all.deb
```
```
# dpkg -i zabbix-release_6.4-1+ubuntu22.04_all.deb
```
```
# apt update
```
### Установите Zabbix сервер, веб-интерфейс и агент
```
# apt install zabbix-server-pgsql zabbix-frontend-php php8.1-pgsql zabbix-nginx-conf zabbix-sql-scripts zabbix-agent
```
## Выполните следующие комманды на хосте, где будет распологаться база данных. ##
### Создайте базу данных
```
sudo apt install postgresql postgresql-contrib
```
```
sudo service postgresql start
```
### Проверьте состояние Posrgresql.
```
sudo service postgresql status
```
Нажмите ```CTRL+C``` для выхода.


### Установите и запустите сервер базы данных.


```
# sudo -u postgres createuser --pwprompt zabbix
```
```
# sudo -u postgres createdb -O zabbix zabbix
```


### На хосте Zabbix сервера импортируйте начальную схему и данные. Вам будет предложено ввести недавно созданный пароль.
```
# zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix
```

### Настройте базу данных для Zabbix сервера
Отредактируйте файл ```/etc/zabbix/zabbix_server.conf```

DBPassword=password

### Настройте PHP для веб-интерфейса
Отредактируйте файл ```/etc/zabbix/nginx.conf``` раскомментируйте и настройте директивы 'listen' и 'server_name'.
```
# listen 8080;
# server_name example.com;
```
Запустите процессы Zabbix сервера и агента (В системе может работать только один агент)
Запустите процессы Zabbix сервера и агента и настройте их запуск при загрузке ОС.
```
# systemctl restart zabbix-server zabbix-agent nginx php8.1-fpm
```
```
# systemctl enable zabbix-server zabbix-agent nginx php8.1-fpm
```
Октройте Zabbix UI веб-страницу\
По адресу ```http://YourServerIP:8080```
***
### Установка [Zabbix UI](https://www.zabbix.com/documentation/6.0/ru/manual/installation/frontend/)

### Установка языка [Zabbix UI](https://www.zabbix.com/documentation/6.0/ru/manual/appendix/install/locales/)

***

## Установка Zabbix-agent2

### Установите репозиторий Zabbix #
```
# wget https://repo.zabbix.com/zabbix/6.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.4-1+ubuntu22.04_all.deb
```
```
# dpkg -i zabbix-release_6.4-1+ubuntu22.04_all.deb
```
```
# apt update
```
### Установите Zabbix-agent2 #
```
# apt install zabbix-agent2 zabbix-agent2-plugin-*
```
### Запустите процесс Zabbix-agent2 и настройте его запуск при загрузке ОС. #
```
# systemctl restart zabbix-agent2
```
```
# systemctl enable zabbix-agent2
```
### Посмотрите статус #
```
# systemctl status zabbix-agent2
```
### Чтобы посмотреть последние логи, введите команду #
```
tail -f /var/log/syslog
```
***
# Мониторинг Postgresql Zabbix агентом2

## Настройка
Разверните Zabbix agent 2 с помощью плагина PostgreSQL. 
Начиная с версий Zabbix 6.0.10 / 6.2.4 / 6.4 Показатели PostgreSQL переносятся в загружаемый плагин и требуют установки отдельного пакета или compilation of the plugin from sources.

### Создайте пользователя PostgreSQL для мониторинга (<password> по вашему усмотрению) и унаследуйте разрешения от роли по умолчанию pg_monitor: #
```
CREATE USER zbx_monitor WITH PASSWORD '<PASSWORD>' INHERIT;
```
```
GRANT pg_monitor TO zbx_monitor;
```
Ищем и открываем файл pg_hba.conf:
```
find / -name pg_hba.conf
```

Редактируем ```pg_hba.conf``` файл конфигурации, чтобы разрешить подключения для пользователя zbx_monitor. Например, вы могли бы добавить одну из следующих строк, чтобы разрешить локальные TCP-соединения с того же хоста:
```
# TYPE  DATABASE        USER            ADDRESS                 METHOD
  host       all        zbx_monitor     localhost               trust
  host       all        zbx_monitor     127.0.0.1/32            md5
  host       all        zbx_monitor     ::1/128                 scram-sha-256
```
Для получения дополнительной информации, пожалуйста, ознакомьтесь с [документацией PostgreSQL](https://www.postgresql.org/docs/current/auth-pg-hba-conf.html/).

Задайте строку подключения для экземпляра PostgreSQL в {$PG.CONNSTRING} макросе в качестве URI, например ```<protocol(host:port)>```, или укажите именованный сеанс - ```<sessionname>```.\
*Примечание: если вы хотите использовать шифрование SSL / TLS для защиты связи с удаленным экземпляром PostgreSQL, необходимо использовать именованный сеанс. В этом случае URI экземпляра должен быть указан в параметре *Plugins.PostgreSQL.Sessions.*.Uri в файлах конфигурации плагина PostgreSQL наряду со всеми параметрами шифрования (тип, пути к файлам cerfiticate/key, если необходимо, и т.д.).

Вы можете проверить [документацию плагина Postgresql](https://git.zabbix.com/projects/AP/repos/postgresql/browse?at=refs%2Fheads%2Frelease%2F6.4/) для получения подробной информации о параметрах плагина агента и именованных сеансах.

Кроме того, предполагается, что вы настроили экземпляр PostgreSQL для работы в желаемом режиме шифрования. Проверьте [документацию Postrgesql](https://www.postgresql.org/docs/current/ssl-tcp.html/) для получения подробной информации.

Примечание: проверка TLS-сертификата плагина основана на проверке альтернативных имен субъекта (SAN) вместо общего имени (CN), подробности см. в пакете [криптографии](https://pkg.go.dev/crypto/x509/).

Например, чтобы включить требуемое шифрование в транспортном режиме без проверки подлинности, вы могли бы создать файл ```/etc/zabbix/zabbix_agent2.d/postgresql_myconn.conf``` со следующей конфигурацией для именованного сеанса myconn (замените <instanceip> адресом экземпляра PostgreSQL):

```Plugins.PostgreSQL.Sessions.myconn.Uri=tcp://<instanceip>:5432```


```Plugins.PostgreSQL.Sessions.myconn.TLSConnect=required```

Затем установите для {$PG.CONNSTRING} макроса значение myconn, чтобы использовать этот именованный сеанс.\

Установите пароль, который вы указали на шаге 2 в макросе {$PG.PASSWORD}.
