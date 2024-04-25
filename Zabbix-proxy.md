# Установка Zabbix-proxy на Centos7
### Установите репозиторий Zabbix #
```
# rpm -Uvh https://repo.zabbix.com/zabbix/6.4/rhel/7/x86_64/zabbix-release-6.4-1.el7.noarch.rpm
```
```
# yum clean all
```
### Cразу советую выполнить команду setenforce 0 или даже лучше в /etc/selinux/config перевести SELINUX в статус disable, т.к. будет мешать работе прокси, агента. #
```
setenforce 0
```
### Если команда не работает, то лучше через редактор перевести SELINUX в статус disable, т.к. будет мешать работе прокси, агента.#
```
nano /etc/selinux/config
```
### Установите Zabbix-proxy #
```
# yum install zabbix-proxy-sqlite3 zabbix-selinux-policy
```
### Отредактируйте файл /etc/zabbix/zabbix_proxy.conf and set DBName parameter.(Для надежности указать ```/var/lib/zabbix/``` #
```
mkdir /var/lib/zabbix/
```
```
chown zabbix:zabbix /var/lib/zabbix
```
```
vim /etc/zabbix/zabbix_proxy.conf
```
```
DBName = /var/lib/zabbix/zabbix_proxy
```
### Запустите процесс Zabbix-proxy и настройте его запуск при загрузке. #
```
# systemctl restart zabbix-proxy
```
```
# systemctl enable zabbix-proxy
```
