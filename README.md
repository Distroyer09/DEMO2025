# DEMON2025
Сетивикон который поможет тем кому помочь крайне сложно, но все ещё возможно.
![image](https://github.com/user-attachments/assets/048e025f-5d33-4bd0-aada-ed759ed7cc81)

## IP адресация

### ISP

Настройка на ISP

<code>apt-get update && apt-get dist-upgrade</code>

![image](https://github.com/user-attachments/assets/9b8cab21-505a-463d-8369-b40956c247de)
Подтверждаем (Y)
Далее будем настраивать интерфейс ISP_HQ-RTR, настраиваем соответствующий интерфейс. 
![image](https://github.com/user-attachments/assets/50a1666f-b72e-4937-b446-52c1db7f48fb)

Далее введите команду 
<code>ip a</code>
Для того чтобы увидеть то какой интерфейс у вас создан **и измените номер тут на ваш номер, иначе ничего не заработает**

Создаем папку с нужным номером интерфейса
<code>mkdir /etc/net/ifaces/ens37</code>(37 замените на ваш номер интерфейса) 

Открываем ваш файл конфигурации
<code>vim /etc/net/ifaces/ens37/options</code>
Сюда копируем настройку
<code>
BOOTPROTO=static
TYPE=eth
NM_CONTROLLED=no
DISABLED=no
CONFIG_WIRELESS=no
SYSTEMD_BOOTPROTO=static
CONFIG_IPV4=yes
SYSTEMD_CONTROLLED=yes
ONBOOT=yes
CONFIG_IPV6=no
</code>

Открываем ваш файл настройки ip адреса
<code>vim /etc/net/ifaces/ens37/ipv4address</code>
Сюда копируем настройку(Имейте в виду что ваше задание может отличаться)
<code>
172.16.4.1/28
</code>

Далее будем настраивать интерфейс ISP_BR-RTR, настраиваем соответствующий интерфейс. 
![image](https://github.com/user-attachments/assets/7079fa1a-c6a9-4173-806a-fc203ddad082)

Далее введите команду 
<code>ip a</code>
Для того чтобы увидеть то какой интерфейс у вас создан **и измените номер тут на ваш номер, иначе ничего не заработает**

Создаем папку с нужным номером интерфейса
<code>mkdir /etc/net/ifaces/ens38</code>(38 замените на ваш номер интерфейса) 

Открываем ваш файл конфигурации
<code>vim /etc/net/ifaces/ens38/options</code>
Сюда копируем настройку
<code>
BOOTPROTO=static
TYPE=eth
NM_CONTROLLED=no
DISABLED=no
CONFIG_WIRELESS=no
SYSTEMD_BOOTPROTO=static
CONFIG_IPV4=yes
SYSTEMD_CONTROLLED=yes
ONBOOT=yes
CONFIG_IPV6=no
</code>

Открываем ваш файл настройки ip адреса
<code>vim /etc/net/ifaces/ens38/ipv4address</code>
Сюда копируем настройку(Имейте в виду что ваше задание может отличаться)
<code>
172.16.5.1/28
</code>

На ваших машинах IP адресация уже настроена, так что вам не придется, её настраивать. 

Перейдем к IPTables

## IPTables

### ISP
<code>
iptables -t nat -A POSTROUTING -s 172.16.4.0/28 -o ens33 -j MASQUERADE
iptables -t nat -A POSTROUTING -s 172.16.5.0/28 -o ens33 -j MASQUERADE
</code>
Затем сохраняем.
<code>
iptables-save >> /etc/sysconfig/iptables
</code>
Перезапускаем
<code>
systemctl enable --now iptables
systemctl restart iptables
systemctl status iptables
</code>
Проверьте файл resolv.conf, там должен быть укзан IP яндекса:
<code>
77.88.8.8
77.88.8.1
</code>
Если работает, то пропингуйте восьмерки с любого RTR если пингуются, все хорошо, если нет, чините как нибудь, проверьте правильность написания, перезагрузите iptables, network или вообще всю машину.

### BR-RTR
<code>
iptables -t nat -A POSTROUTING -s 192.168.4.0/27 -o ens36 -j MASQUERADE
iptables-save >> /etc/sysconfig/iptables
</code>
Имейте в виду что ваша сеть будет отличаться по IP адресу,  маске подсети и порту, проверьте это перед вводом.
<code>
systemctl enable --now iptables
systemctl restart iptables
systemctl status iptables
</code>
Проверяем пингуя восьмерки с BR-SRV

### HQ-RTR
<code>
iptables -t nat -A POSTROUTING -s 192.168.1.0/26 -o ens36 -j MASQUERADE
iptables -t nat -A POSTROUTING -s 192.168.2.0/28 -o ens36 -j MASQUERADE
iptables -t nat -A POSTROUTING -s 192.168.3.0/29 -o ens36 -j MASQUERADE
iptables-save >> /etc/sysconfig/iptables
</code>

<code>
systemctl enable --now iptables
systemctl restart iptables
systemctl status iptables
</code>

Проверяем пингуя восьмерки с HQ-SRV

## Настройка тоннеля

### HQ-RTR

Переходим в папку интерфейсов и создаем интерфейс с необходимыми файлами.
<code>
cd /etc/net/ifaces/
mkdir gre1
cd gre1
vim options
</code>
options
<code>
TUNLOCAL=172.16.4.2
TUNREMOTE=172.16.5.2
TUNTYPE=gre
TYPE=iptun
TUNTTL=64 
TUNMTU=1476
TUNOPTIONS='ttl 64'
DISABLE=no
</code>
Теперь создаем файл ipv4address
<code>
vim ipv4address
</code>
И укзываем ip 
<code>
10.10.10.1/30
</code>
Перезапускаем сеть
<code>
systemctl restart network
</code>
Теперь проверяем ip a и смотрим появились ли порты gre. Если да, то идем дальше если нет, перезагрузите все что можете и проверьте то как вы это написали, возможно где то дпущена опечатка.

Теперь, разрешим прием-передачу пакетов по 47 порту через iptables
<code>
iptables -I INPUT -i ens36 -p 47 -j ACCEPT
iptables -I OUTPUT -o ens36 -p 47 -j ACCEPT
iptables-save >> /etc/sysconfig/iptables
systemctl restart iptables
iptables-save
</code>
Проверьте настройки

### BR-RTR

Переходим в папку интерфейсов и создаем интерфейс с необходимыми файлами.
<code>
cd /etc/net/ifaces/
mkdir gre1
cd gre1
vim options
</code>
options
<code>
TUNLOCAL=172.16.5.2
TUNREMOTE=172.16.4.2
TUNTYPE=gre
TYPE=iptun
TUNTTL=64 
TUNMTU=1476
TUNOPTIONS='ttl 64'
DISABLE=no
</code>
Теперь создаем файл ipv4address
<code>
vim ipv4address
</code>
И укзываем ip 
<code>
10.10.10.2/30
</code>
Перезапускаем сеть
<code>
systemctl restart network
</code>
Теперь проверяем ip a и смотрим появились ли порты gre. Если да, то идем дальше если нет, перезагрузите все что можете и проверьте то как вы это написали, возможно где то дпущена опечатка.

Теперь, разрешим прием-передачу пакетов по 47 порту через iptables
<code>
iptables -I INPUT -i ens36 -p 47 -j ACCEPT
iptables -I OUTPUT -o ens36 -p 47 -j ACCEPT
iptables-save >> /etc/sysconfig/iptables
systemctl restart iptables
iptables-save
</code>
Проверьте настройки и пропингуйте тонель HQ-RTR# <code> ping 10.10.10.2 </code>
То же самое с BR-RTR# <code> ping 10.10.10.1 </code>
Пингуется? Значит работает.

## FRR/OSPF

### HQ-RTR

Установим FRR
<code>
apt-get install frr
</code>
Подтверждаем и после установки переходим в файл конфигурации
<code>
vim /etc/frr/daemons
</code>
Тут меняем строчку с ospfd=no на ospfd=yes. Затем перезапусм и проверяем статус
<code>
systemctl restart frr
systemctl status frr
</code>
Теперь переходим в vtysh и вводим следующие команды(IP адреса и маски будут отличаться)
<code>
conf t
do sh int br
router ospf
network 10.10.10.0/30 area 0
network 192.168.1.0/26 area 0
network 192.168.2.0/28 area 0
network 192.168.3.0/29 area 0
do wr mem
exit
int gre1
ip ospf authentication messasge-digest
ip ospf message-digest-key 1 md5 P@ssw0rd
do wr mem
exit
do show run
</code>

Теперь выходим из этого режима и перезапускаем 
<code>
systemctl enable --now frr
systemctl restart frr
</code>

### HQ-RTR

Установим FRR
<code>
apt-get install frr
</code>
Подтверждаем и после установки переходим в файл конфигурации
<code>
vim /etc/frr/daemons
</code>
Тут меняем строчку с ospfd=no на ospfd=yes. Затем перезапусм и проверяем статус
<code>
systemctl restart frr
systemctl status frr
</code>
Теперь переходим в vtysh и вводим следующие команды(IP адреса и маски будут отличаться)
<code>
conf t
do sh int br
router ospf
network 10.10.10.0/30 area 0
network 192.168.4.0/27 area 0
do wr mem
exit
int gre1
ip ospf authentication messasge-digest
ip ospf message-digest-key 1 md5 P@ssw0rd
do wr mem
exit
do show run
</code>

Теперь выходим из этого режима и перезапускаем 
<code>
systemctl enable --now frr
systemctl restart frr
</code>

### OSPF проверка

Теперь переходим в vtysh и проверяем
<code>
conf t
do show ip ospf neighbor
</code>
В командной строке самих роутеров traceroute 192.168.4.2 Эта команда выполняется какое то время, так что подождите пока кончится проверка.

## DNSMASQ

### HQ-SRV

<code>

</code>






