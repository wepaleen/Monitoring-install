# Инструкция по развертыванию Zabbix Proxy 6.4.18 на RedOS 7.3 с PostgreSQL 15

### 1. Обновление системы
```bash
sudo yum update
```
или
```bash
sudo dnf update
```
 Обновите все пакеты: 
```bash
sudo yum upgrade -y
```
### 2. Установка PostgreSQL 15
 Установите PostgreSQL 15 (https://redos.red-soft.ru/base/server-configuring/dbms/install-postgresql/) :
```bash
sudo yum install postgresql15-server
```
 Инициализируйте базу данных:
```bash
sudo postgresql-setup initdb
```
 Запустите PostgreSQL:
```bash
sudo systemctl start postgresql-15
```
 Включите автоматический запуск PostgreSQL:
```bash
sudo systemctl enable postgresql-15
```
### 3. Создание пользователя PostgreSQL для Zabbix

 Войдите в консоль PostgreSQL:
```bash
sudo su - postgres
psql
```
 Создайте пользователя Zabbix:
```sql
CREATE USER zabbix WITH PASSWORD 'your_password';
```
 Создайте базу данных для Zabbix:
```sql
CREATE DATABASE zabbix OWNER zabbix;
```
 Предоставьте права пользователю Zabbix на базу данных:
```sql
GRANT ALL PRIVILEGES ON DATABASE zabbix TO zabbix;
```
 Выйдите из консоли PostgreSQL:
```sql
\q
```
### 4. Установка Zabbix Proxy

 Скачать пакеты:
     zabbix-agent2-6.4.18-1.el7.x86_64.rpm 
     zabbix-agent-6.4.18-1.el7.x86_64.rpm 
     zabbix-proxy-pgsql-6.4.18-1.el7.x86_64.rpm
 Установите пакеты:
```bash
sudo dnf install ./zabbix-proxy-pgsql-6.4.18.x86_64.rpm
sudo dnf install ./zabbix-agent2-6.4.18.x86_64.rpm
sudo dnf install ./zabbix-agent-6.4.18.x86_64.rpm
```
### 5. Настройка Zabbix Proxy

 Отредактируйте файл конфигурации Zabbix Proxy:
```bash
sudo nano /etc/zabbix/zabbix_proxy.conf
```
 Измените следующие параметры:
     `DBHost` на IP-адрес PostgreSQL сервера (если Proxy и PostgreSQL находятся на одном сервере, укажите `localhost`)
    
     `DBName` на имя базы данных Zabbix
     
     `DBUser` на имя пользователя PostgreSQL для Zabbix
     
     `DBPassword` на пароль пользователя PostgreSQL
    
     `Server` на IP-адрес Zabbix сервера (если Proxy и Server находятся на одном сервере, укажите `localhost`)

 Сохраните файл конфигурации.

### 6. Запуск Zabbix Proxy

 Запустите Zabbix Proxy:
```bash
sudo systemctl start zabbix-proxy
```
 Включите автоматический запуск Zabbix Proxy:
```bash
sudo systemctl enable zabbix-proxy
```

### 7. Проверка работоспособности

 Проверьте, что Zabbix Proxy запущен:
```bash
sudo systemctl status zabbix-proxy
```
 Проверьте, что Zabbix Proxy подключен к Zabbix серверу:
 Проверьте, что Zabbix Proxy получает данные от агентов:

### 8. Дополнительные настройки

 Настройка агентов:
     Установите и настройте Zabbix Agent 2 на серверах, с которых вы хотите получать данные.
     Настройте Zabbix Agent 2 для подключения к Zabbix Proxy.
 Настройка Zabbix сервера:
     Добавьте Zabbix Proxy в Zabbix сервер.
     Настройте Zabbix сервер для получения данных от Zabbix Proxy.
```bash
ls -la /run/zabbix/
sudo touch /run/zabbix/zabbix_proxy.pid
sudo chown zabbix:zabbix /run/zabbix/zabbix_proxy.pid

```

