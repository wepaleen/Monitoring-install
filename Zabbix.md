# Установка Zabbix-server на Postgesql Ubuntu
![thb](https://github.com/vprimin/pub/blob/main/Monitoring-install/zproxy.png)
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
### Запустите процесс Zabbix-agent2 #
### Запустите процесс Zabbix-agent2 и настройте его запуск при загрузке ОС. #
```
# systemctl restart zabbix-agent2
```
```
# systemctl enable zabbix-agent2
```
