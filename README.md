Изменение имени хоста

Vim /etc/hostname


Настройка статических IP

![image](https://github.com/D9rk0/Configure-iperf3-static-dhcp-ssh-frr-ospf-firewalld/assets/170403880/f3c64eb5-e37e-4582-93a5-5cab20869be3)

![image](https://github.com/D9rk0/Configure-iperf3-static-dhcp-ssh-frr-ospf-firewalld/assets/170403880/eee26f0a-59cf-46fc-9474-6b986f9df6fc)

Vim /etc/net/interfaces/ens

/ipv4address

/options

/ipv4route

FRR 
(это часть задания касается только машин hq-r, isp, br-r)

Apt-get update

Apt-get install frr

Vim /etc/frr/daemons

Ospfd=yes

systemctl reload frr

vtysh

conf t

![image](https://github.com/D9rk0/Configure-iperf3-static-dhcp-ssh-frr-ospf-firewalld/assets/170403880/a29c625a-a4af-4f08-b286-52cd2bf0892f)

![image](https://github.com/D9rk0/Configure-iperf3-static-dhcp-ssh-frr-ospf-firewalld/assets/170403880/fb214a40-6c70-4eb2-848b-f9ac1f8b0482)

![image](https://github.com/D9rk0/Configure-iperf3-static-dhcp-ssh-frr-ospf-firewalld/assets/170403880/95cd22da-6c63-41d4-8ed7-f17c8d06bb14)

ip forwarding

exit

wr mem

systemctl reload frr

vim /etc/sysctl.conf

net.ipv4.ip_forward=1, не забываем  сохранить изменения. Эту тему нужно провернуть на машинах HQ-R и BR-R

![image](https://github.com/D9rk0/Configure-iperf3-static-dhcp-ssh-frr-ospf-firewalld/assets/170403880/8f017275-5d7b-4fa3-9713-5eca7f90b4cf)

Настройте автоматическое распределение IP-адресов на роутере HQ-R.

Apt-get install isc-dhcp-server

теперь нужно определить порт прослушивания интерфейсов, для этого определяем какой порт смотрит на HQ-SRV именно он и является портом прослушивания, как это сделать вы знаете. Далее открываем файл конфигурации: nano /etc/default/isc-dhcp-server. Тут значение INTERFACESv4="eth0" нужно прировнять непосредственно к названию того самого интерфейса, который вы искали ранее.

![image](https://github.com/D9rk0/Configure-iperf3-static-dhcp-ssh-frr-ospf-firewalld/assets/170403880/cfdf3f7b-1ca9-43fe-a28d-23d589697748)

Vim /etc/dhcp/dhcpd.conf

![image](https://github.com/D9rk0/Configure-iperf3-static-dhcp-ssh-frr-ospf-firewalld/assets/170403880/4a88f87a-a0e7-40d4-aba9-f85f4b6772d3)

Hq-srv

![image](https://github.com/D9rk0/Configure-iperf3-static-dhcp-ssh-frr-ospf-firewalld/assets/170403880/5721cc26-138c-4356-b763-6d46a733f610)

systemctl start isc-dhcp-server

systemctl enable isc-dhcp-server

Настройте локальные учётные записи на всех устройствах

Учетная запись	Пароль	Примечание
Admin	        P@ssw0rd	CLI HQ-SRV HQ-R
Branch admin	P@ssw0rd	BR-SRV BR-R
Network admin	P@ssw0rd	HQ-R BR-R BR-SRV

Useradd admin 

Passwd admin –

Измерьте пропускную способность сети между двумя узлами HQ-R-ISP по средствам утилиты iperf3. Предоставьте описание пропускной способности канала со скриншотами.

Apt-get install iperf3

Systemctl start iperf3

Systemctl enable iperf3

![image](https://github.com/D9rk0/Configure-iperf3-static-dhcp-ssh-frr-ospf-firewalld/assets/170403880/180a43c3-3401-4675-a213-9791d0d72076)

![image](https://github.com/D9rk0/Configure-iperf3-static-dhcp-ssh-frr-ospf-firewalld/assets/170403880/0496e369-7b04-4cce-a99d-b83866d61871)

Составьте backup скрипты для сохранения конфигурации сетевых устройств, а именно HQ-R BR-R. Продемонстрируйте их работу

![image](https://github.com/D9rk0/Configure-iperf3-static-dhcp-ssh-frr-ospf-firewalld/assets/170403880/e3b28b02-9fc9-42dc-8e19-01f3effa30d6)

![image](https://github.com/D9rk0/Configure-iperf3-static-dhcp-ssh-frr-ospf-firewalld/assets/170403880/ce4da68c-cf2d-4f2d-87c4-5a6785aa3f16)

Запуск скрипта

Для запуска скрипта:

1. Перейти в директорию, где находится файл скрипта:
2. 
	cd /opt/backup

4. Запустить скрипт:
5. 
	./backup-script.sh

Настройте подключение по SSH для удалённого конфигурирования устройства HQ-SRV по порту 2222. Учтите, что вам необходимо перенаправить трафик на этот порт по средствам контролирования трафика.
Apt-get install (openssh-server)d

systemctl enable ssh

vim /etc/ssh/sshd_config

![image](https://github.com/D9rk0/Configure-iperf3-static-dhcp-ssh-frr-ospf-firewalld/assets/170403880/361de165-5204-41df-bb25-1e0de9c9c3b8)

![image](https://github.com/D9rk0/Configure-iperf3-static-dhcp-ssh-frr-ospf-firewalld/assets/170403880/931b03dc-2b39-4f27-bdaa-166c98f86bf8)

systemctl restart ssh

Устанавливается firewalld

Apt-get install firewalld

firewall-cmd --list-all-zones | less

firewall-cmd --set-default-zone=trusted

firewall-cmd --change-interface=ens(у вас свой) --zone=trusted

firewall-cmd --change-interface=ens(у вас свой) --zone=trusted

firewall-cmd --runtime-to-permanent

systemctl restart firewalld.service

![image](https://github.com/D9rk0/Configure-iperf3-static-dhcp-ssh-frr-ospf-firewalld/assets/170403880/d1f8a9b9-9492-4740-b7f4-b030c9c43b58)

Настройте контроль доступа до HQ-SRV по SSH со всех устройств, кроме CLI.

Vim /etc/hosts.deny

В файл дописывается: sshd:ip-адрес CLI

systemctl restart ssh
