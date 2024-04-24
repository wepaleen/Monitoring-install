Установка Prometheus + Alertmanager + node_exporter на Linux Debian, Red Hut

Подготовка сервера
Настроим некоторые параметры сервера, необходимые для правильно работы системы.

Дополнительные пакеты
Установим пакеты, которые нам понадобятся для работы:

```wget``` — для загрузки файлов.

```tar``` — для распаковки архивов.

```sudo -l```

```apt-get install sudo -y```

В зависимости от системы, команды будут немного отличаться.

а) Для Linux Deb (Debian / Ubuntu):
```
apt update

apt install wget tar
```
б) Для Linux RPM (Rocky / CentOS):

```
yum install wget tar
```

Время
Для отображения событий в правильное время, необходимо настроить его синхронизацию. Для этого установим chrony:

а) если на системе CentOS / Red Hat:
```

yum install chrony

systemctl enable chronyd

systemctl start chronyd

```

б) если на системе Ubuntu / Debian:
```
apt install chrony

systemctl enable chrony

systemctl start chrony
```
Брандмауэр
На фаерволе, при его использовании, необходимо открыть порты: 

```TCP 9090``` — http для сервера прометеус.
```TCP 9093``` — http для алерт менеджера.
```TCP и UDP 9094``` — для алерт менеджера.
```TCP 9100``` — для node_exporter.

а) с помощью firewalld:
```
firewall-cmd --permanent --add-port=9090/tcp --add-port=9093/tcp --add-port=9094/{tcp,udp} --add-port=9100/tcp

firewall-cmd --reload
```
б) с помощью iptables:
```
iptables -I INPUT -p tcp --match multiport --dports 9090,9093,9094,9100 -j ACCEPT

iptables -I INPUT -p udp --dport 9094 -j ACCEPT
```
Сохраняем правила с помощью iptables-persistent:
```
apt install iptables-persistent

netfilter-persistent save
```
в) с помощью ufw:
```
ufw allow 9090,9093,9094,9100/tcp

ufw allow 9094/udp

ufw reload
```
SELinux
По умолчанию, SELinux работает в операционный системах на базе Red Hat. Проверяем, работает ли она в нашей системе:
```
getenforce
```
Если мы получаем в ответ:
```
Enforcing
```
... необходимо отключить его командами:

setenforce 0
```
sed -i 's/^SELINUX=.*/SELINUX=disabled/g' /etc/selinux/config
```
* если же мы получим ответ The program 'getenforce' is currently not installed, то SELinux не установлен в системе.

Prometheus
Prometheus не устанавливается из репозитория и имеет, относительно, сложный процесс установки. Необходимо скачать исходник, создать пользователя, вручную скопировать нужные файлы, назначить права и создать юнит для автозапуска.

Загрузка
Переходим на официальную страницу загрузки и копируем ссылку на пакет для Linux (желательно, использовать версию LTS):
