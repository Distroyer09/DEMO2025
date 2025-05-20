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
