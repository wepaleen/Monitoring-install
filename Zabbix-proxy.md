# Установка Zabbix-proxy на Centos7
## Установите репозиторий Zabbix
Документация
'''
# rpm -Uvh https://repo.zabbix.com/zabbix/6.4/rhel/7/x86_64/zabbix-release-6.4-1.el7.noarch.rpm

# yum clean all
b. Установите Заббикс aгент2
# yum install zabbix-agent2 zabbix-agent2-plugin-*
c. Запустите процесс Zabbix агента2
Запустите процесс Zabbix агента2 и настройте его запуск при загрузке ОС.

# systemctl restart zabbix-agent2
# systemctl enable zabbix-agent2
