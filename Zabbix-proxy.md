# Установка Zabbix-proxy на Centos7
***
![image](https://github.com/wepaleen/Monitoring-install/assets/110018366/e4415612-e973-48e0-9941-8cf9c2a3f857)
***
### Установите репозиторий Zabbix #
```
# rpm -Uvh https://repo.zabbix.com/zabbix/6.4/rhel/7/x86_64/zabbix-release-6.4-1.el7.noarch.rpm
```
```
# yum clean all
```
### Выполнить команду setenforce 0 или даже лучше в /etc/selinux/config перевести SELINUX в статус disable, т.к. будет мешать работе прокси, агента. #
```
setenforce 0
```
### * Если команда не работает, то лучше через редактор перевести SELINUX в статус disable, т.к. будет мешать работе прокси, агента.#
```
nano /etc/selinux/config
```
### Установите Zabbix-proxy #
```
# yum install zabbix-proxy-sqlite3 zabbix-selinux-policy
```
### Создайте директорию ```/var/lib/zabbix/``` и дайте права пользователю Zabbix #

```
mkdir /var/lib/zabbix/
```
```
chown zabbix:zabbix /var/lib/zabbix
```

### Отредактируйте файл ```/etc/zabbix/zabbix_proxy.conf``` и добавьте в параметр DBName. #

```
vim /etc/zabbix/zabbix_proxy.conf
```
```
Server = localhost
Hostname = Zabbix_proxy
DBName = /var/lib/zabbix/zabbix_proxy
```
### Запустите процесс Zabbix-proxy и настройте его запуск при загрузке. #
```
# systemctl restart zabbix-proxy
```
```
# systemctl enable zabbix-proxy
```
***
https://redos.red-soft.ru/base/arm/arm-network/synchro-time/chrony/
