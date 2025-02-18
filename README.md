# Part 1. ipcalc tool

Для определения адреса сети для подсети 192.167.38.54/13 использую утилиту ipcalc
Для установки использовал команду sudo apt install ipcalc

Определяю адрес сети для подсети 192.167.38.54/13 командой ipcalc 192.167.38.54/13

![Alt text](src/screenshots/pipcalc.png)

На скриншоте в выводе команды мы видим:
слева результаты вывода в виде десятичной записи, справа - двоичной

Address - это ip адрес, который мы ввели

Netmask - это маска подсети

Wildcard - это обратная маска

Network - это адрес сети, в которой находится наш IP

Hostmin - самый первый (минимальный) доступный IP адрес для хостов

(Мы видим, что он 192.160.0.1, тк хосты не могут использовать адрес 192.
160.0.0 т.к. это адрес сети)

HostMax - последний доступный IP адрес для хостов

Broadcast - это адрес широковещательной рассылки 

Hosts/Net - показывает сколько всего доступно ip адресов для хостов

преобразование маски 255.255.255.0 в префиксную и двоичную

![Alt text](src/screenshots/p2552552552550.png)

![Alt text](src/screenshots/p1.1.2.png)

![Alt text](src/screenshots/p000015.png)



Минимальный и максимальный хост в сети 12.167.38.4 с масками: /8 , 11111111.11111111.00000000.00000000 , 255.255.254.0 и /4

![Alt text](src/screenshots/p1.1.3m8.png)

![Alt text](src/screenshots/p1.1.3m255.png)

![Alt text](src/screenshots/p1.1.3m16.png)

![Alt text](src/screenshots/p1.1.3m4.png)


## Можно ли получить доступ к приложению, работающему на локальном хосте, со следующих IP-адресов: 194.34.23.100 , 127.0.0.2 , 127.1.0.1 , 128.0.0.1.

![Alt text](src/screenshots/p1.2.png)

К частным "серым" адресам относятся IP-адреса из следующих подсетей: От 10.0.0.0 до 10.255.255.255 с маской 255.0.0.0 или /8. От 172.16.0.0 до 172.31.255.255 с маской 255.240.0.0 или /12. От 192.168.0.0 до 192.168.255.255 с маской 255.255.0.0 или /16.

Каждое число в наборе может находиться в диапазоне от 0 до 255. Таким образом, полный диапазон IP-адресов варьируется от 0.0.0.0 до 255.255.255.255. IP-адреса не случайны.

- Частным 10.0.0.45, 10.10.10.10,  172.16.255.255, 172.20.250.4

- Публичное 134.43.0.2, 192.168.4.2,  192.172.0.1, 172.68.0.2,  192.169.168.1, 172.0.2.1,


## Какие из перечисленных IP-адресов шлюза возможны у сети 10.10.0.0/18?

Маска /18 означает, что первые 18 бит IP-адреса используются для определения сети, а оставшиеся 14 бит — для хостов.

11111111.11111111.11000000.00000000 маска

00000000.00000000.00111111.11111111 для хоста

Переведем в десятичный вид и получим, что диапазон доступных хостов будет от 10.10.0.1 до 10.10.63.255

- Входит:  10.10.0.2, 10.10.1.255, 10.10.10.10

- Не входит:  10.0.0.1, 10.10.100.1


# Part 2. Static routing between two machines

С помощью команды ip a посмотрел существующие сетевые интерфейсы.

![Alt text](src/screenshots/p2.1.png)

Описал сетевой интерфейс, соответствующий внутренней сети, на обеих машинах и задал следующие адреса и маски: ws1 — 192.168.100.10, маска /16, ws2 — 172.24.116.8, маска /12.

![Alt text](src/screenshots/p2.0.1.png)

Выполнил команду netplan apply для перезапуска сервиса сети.

![Alt text](src/screenshots/p2.0.2.png)


Добавил статический маршрут от одной машины до другой и обратно при помощи команды вида ```ip r add (ip) dev enp0s8.``` но перед этим нужно сделать ```ip link set enp0s8 up``` чтобы изсенить статус с DOWN на UP

![Alt text](src/screenshots/p2.png)

Перезагрузил машины.

После преезагрузки настройки не сохранились

![Alt text](src/screenshots/p2.2.1.png)

Добавлю статический маршрут с одной машины на другую с помощью файла /etc/netplan/00-installer-config.yaml

![Alt text](src/screenshots/p2.2.2.png)

Сохраню изменения командой ```sudo netplan apply``` и пропингую ```ping```

![Alt text](src/screenshots/p2.2.3.png)

# Part 3. iperf3 utility

Перевел и записал в отчет скорость соединения

8 Mbps = 1 MB/s
100 MB/s = 800000 Kbps
1 Gbps = 1000 Mbps

Измерил скорость соединения между ws1 и ws2. Для этого нужно скачать и воспользоваться утилитой ```iperf3``` с флагом -с и ip ws2 в ws1 и iperf3 -s в ws2 для открытия в режиме сервера

![Alt text](src/screenshots/p3.1.png)

# Part 4. Network firewall

Создал файл /etc/firewall.sh, имитирующий файрвол, на ws1 и ws2:

Добавить в файл подряд следующие правила:

1) На ws1 применил стратегию, когда в начале пишется запрещающее правило, а в конце пишется разрешающее правило (это касается пунктов 4 и 5).

2) На ws2 применил стратегию, когда в начале пишется разрешающее правило, а в конце пишется запрещающее правило (это касается пунктов 4 и 5).

3) Открыл на машинах доступ для порта 22 (ssh) и порта 80 (http).

4) Запретил echo reply (машина не должна «пинговаться», т. е. должна быть блокировка на OUTPUT).

5) Разрешил echo reply (машина должна «пинговаться»).


![Alt text](src/screenshots/p4.1.png)

Запустил файлы на обеих машинах командами ```chmod +x /etc/firewall.sh и /etc/firewall.sh.```

![Alt text](src/screenshots/p4.2.png)

Командой ping нашел машину, которая не «пингуется», после чего утилитой nmap показал, что хост машины запущен.

![Alt text](src/screenshots/p4.2.1.png)

Ключ -sV команды nmap включает детальное исследование портов для определения версий служб

# Part 5. Static network routing

Поднял пять виртуальных машин (3 рабочие станции (ws11, ws21, ws22) и 2 роутера (r1, r2)).

Настроил конфигурации машин в etc/netplan/00-installer-config.yaml согласно сети на рисунке.

![Alt text](src/screenshots/ppart5_network.png)

Роутеры: 

![Alt text](src/screenshots/p5.1routers.png)

Машины:

![Alt text](src/screenshots/p5.1ws.png)

Перезапустил сервис сети. Командой ```ip -4 a``` проверил, что адрес машины задан верно.

![Alt text](src/screenshots/p5.1ip4a.png)

Также пропинговал ws22 с ws21. Аналогично пропинговал r1 с ws11.

![Alt text](src/screenshots/p5.1pingr1ws11andws22ws21.png)

Для включения переадресации IP выполнил команду на роутерах: ```sysctl -w net.ipv4.ip_forward=1```

![Alt text](src/screenshots/p5.2.png)

Открыл файл /etc/sysctl.conf и добавил в него следующую строку:
```net.ipv4.ip_forward = 1```

При использовании этого подхода, IP-переадресация включена на постоянной основе.

![Alt text](src/screenshots/p5.2.2.png)


Настроbk маршрут по умолчанию (шлюз) для рабочих станций. Для этого добавbk default перед IP-роутера в файле конфигураций.

Вызвал ```ip r``` и показал, что добавился маршрут в таблицу маршрутизации.

![Alt text](src/screenshots/p5.3.png)

Пропинговал с ws11 роутер r2 и показал на r2, что пинг доходит. Для этого используй команду:

```tcpdump -tn -i eth0```

![Alt text](src/screenshots/p5.3.1.png)

Добавил в роутеры r1 и r2 статические маршруты в файле конфигураций.

![Alt text](src/screenshots/p5.4.1.png)

Вызвал ip r и показал таблицы с маршрутами на обоих роутерах.

![Alt text](src/screenshots/p5.4.2.png)

Запустил команды на ws11:
```ip r list 10.10.0.0/[маска сети]``` и ```ip r list 0.0.0.0/0```

![Alt text](src/screenshots/p5.4.3.png)

Установил traceroute командой ```sudo apt install inetutils-traceroute```

Запустил на r1 команду дампа командой ```sudo tcpdump -tnv -i enp0s8```

При помощи утилиты traceroute пострил список маршрутизаторов на пути от ws11 до ws21.

![Alt text](src/screenshots/p5.5.png)

Traceroute использует принцип отправки UDP пакетов по маршруту к целевому узлу.
Каждый пакет содержит увеличивающийся счетчик TTL (Time to Live), изначально установленный в 1.
Когда пакет достигает маршрутизатора, счетчик TTL уменьшается на 1.
Когда счетчик TTL достигает нуля, маршрутизатор отбрасывает пакет и отправляет обратно сообщение (time exceeded) и traceroute записывает адрес этого маршрутизатора.
Затем процесс повторяется с увеличенным TTL, что позволяет traceroute отслеживать каждый шаг пути до целевого узла.

UDP (User Datagram Protocol) Этот протокол используется для «связи без установки соединения» (connectionless communication).
Один узел сети отсылает пакеты, адресуя их другому узлу. Отправитель не знает ничего о том, готов ли получатель к приёму пакетов, и вообще, существует ли этот получатель.
Отправитель также не ждёт какого-либо подтверждения о том, что получатель принял предыдущие пакеты.

Ключевой момент в использовании протокола UDP в утилите traceroute заключается в его ненадежности и неблокирующей природе. Поскольку основная цель traceroute - отслеживание пути до конечного узла, стабильная и надежная передача данных не является первостепенной. Вместо этого, traceroute предпочитает скорость и простоту для быстрого обнаружения промежуточных маршрутизаторов и определения времени прохождения данных через каждый из них. Протокол UDP, в отличие от TCP, не устанавливает устойчивое соединение и не требует ответа на каждый отправленный пакет, что позволяет более эффективно определять маршрут и адреса промежуточных узлов на пути следования пакетов.
Термин "ненадежность" в данном контексте означает, что протокол UDP не гарантирует надежной доставки данных.
В случае использования TCP, требовалось бы установить устойчивое соединение между отправителем и получателем, что привело бы к более надежной, но также более медленной передаче.
"Неблокирующая природа" UDP означает, что он не ждет подтверждения доставки каждого пакета, что делает его более быстрым и подходящим для утилиты типа traceroute.

Запустил на r1 перехват сетевого трафика, проходящего через eth0 с помощью команды:
```tcpdump -n -i eth0 icmp```

Пропинговал с ws11 несуществующий IP с помощью команды:
```ping -c 1 10.30.0.111```

![Alt text](src/screenshots/p5.6.png)

# Part 6. Dynamic IP configuration using DHCP

Установил сервис DHCPD командой ```sudo apt install isc-dhcp-server```

Для r2 настрил в файле /etc/dhcp/dhcpd.conf конфигурацию службы DHCP:

Указал адрес маршрутизатора по умолчанию, DNS-сервер и адрес внутренней сети.

![Alt text](src/screenshots/p6.1.png)

В файле /etc/resolv.conf прописал nameserver 8.8.8.8.

![Alt text](src/screenshots/p6.2.png)

Перезагрузил службу DHCP командой systemctl restart isc-dhcp-server. Машину ws21 перезагрузил при помощи reboot и через ip a показал, что она получила адрес. Также пропинговал ws22 с ws21.

![Alt text](src/screenshots/p6.6.png)

Изменим настройки машин ws21 и ws22 в файле конфигурации, чтобы сделать протокол DHCP активным

![Alt text](src/screenshots/p6.5.png)

![Alt text](src/screenshots/p6.4.png)

Указал MAC-адрес у ws11, для этого в etc/netplan/00-installer-config.yaml надо добавить строки: macaddress: 10:10:10:10:10:BA, dhcp4: true.

Зашел в менеджер виртуальных машин VirtualBox и там настрол ws11 MAC-адрес

![Alt text](src/screenshots/p6.8.png)

![Alt text](src/screenshots/p6.7.png)

Для r1 настроил аналогично r2, но сделал выдачу адресов с жесткой привязкой к MAC-адресу (ws11). Провел аналогичные тесты.

![Alt text](src/screenshots/p6.9.png)

![Alt text](src/screenshots/p6.11.png)

![Alt text](src/screenshots/p6.10.png)

![Alt text](src/screenshots/p6.12.png)

Обновил ip на машине ws21
Ввёл ip a чтобы вывести текущий ip
Использовал sudo dhclient -r enp0s8 для освобождения ip адреса
Запросил обновление ip адреса командой sudo dhclient -r enp0s8
Загрузил скрин c ip до и после обновления

![Alt text](src/screenshots/p6.13.png)

![Alt text](src/screenshots/p6.15.png)

# Part 7. NAT

Установил Apache2 на ws22 командой ```sudo apt install apache2```

В файле /etc/apache2/ports.conf на ws22 и r1 изменил строку Listen 80 на Listen 0.0.0.0:80, то есть сделал сервер Apache2 общедоступным.

![Alt text](src/screenshots/p7.0.png)

Запустил веб-сервер Apache командой service apache2 start на ws22 и r1.

![Alt text](src/screenshots/p7.2.png)

Добавил в фаервол, созданный по аналогии с фаерволом из Части 4, на r2 следующие правила:

1) Удаление правил в таблице filter — iptables -F;

2) Удаление правил в таблице «NAT» — iptables -F -t nat;

3) Отбрасывать все маршрутизируемые пакеты — iptables --policy FORWARD DROP.

![Alt text](src/screenshots/p7.4.png)

Проверил соединение между ws22 и r1 командой ping

![Alt text](src/screenshots/p7.3.png)

Добавил в файл ещё одно правило:

4) Разрешить маршрутизацию всех пакетов протокола ICMP.

Запустил файл командой ```sudo chmod +x ... && sh ...```

Добавил в файл ещё два правила:

5) Включил SNAT, а именно маскирование всех локальных IPиз локальной сети, находящейся за r2 (по обозначениям из Части 5 — сеть 10.20.0.0).

6) Включил DNAT на 8080 порт машины r2 и добавил к веб-серверу Apache, запущенному на ws22, доступ извне сети.

![Alt text](src/screenshots/p7.5.png)

Значения использованных опций:
t - указывает на используемую таблицу;
p - указывает протокол, такие как tcp, udp, udplite и другие, поддерживаемые системой, ознакомиться со списком можно в файле /etc/protocols;
m - подключает указанный модуль;
s - указывает адрес источника пакета, в качестве значения можно указать как один IP-адрес, так и диапазон;
i - задает входящий сетевой интерфейс;
o - указывает исходящий сетевой интерфейс;
--dport - порт получателя пакета;
DNAT — подменяет адрес получателя в заголовке IP-пакета, основное применение — предоставление доступа к сервисам снаружи, находящимся внутри сети;
SNAT — служит для преобразования сетевых адресов, применимо, когда за сервером находятся машины, которым необходимо предоставить доступ в Интернет, при этом от провайдера имеется статический IP-адрес.

Проверил соединение по TCP для SNAT: для этого с ws22 нужно подключиться к серверу Apache на r1 командой:
```telnet [адрес] [порт]```

Проверил соединение по TCP для DNAT: для этого с r1 нужно подключиться к серверу Apache на ws22 командой telnet (обращаться по адресу r2 и порту 8080).

![Alt text](src/screenshots/p7.6.png)

![Alt text](src/screenshots/p7.8.png)

# Part 8. Introduction to SSH Tunnels

Запустил на r2 фаервол с правилами из Части 7.

![Alt text](src/screenshots/p8.0.png)

Запустил веб-сервер Apache на ws22 только на localhost (то есть в файле /etc/apache2/ports.conf измени строку Listen 80 на Listen localhost:80).

![Alt text](src/screenshots/p8.1.png)

![Alt text](src/screenshots/p8.2.png)

В файле /etc/ssh/sshd_config раскомментировал строку AllowTcpForwarding yes чтобы разрешить маршрутизацию по ssh

![Alt text](src/screenshots/p8.4.png)

Перезапустил ssh на машине ws22 и проверил прослушиваемые порты


Использованные ключи команды netstat:

![Alt text](src/screenshots/p8.6.png)

t: отображает только TCP-соединения, исключая UDP и другие протоколы
u: отображает только UDP-соединения
l: отображает только прослушиваемые порты, т.е. порты, на которых серверы слушают входящие соединения
n: отображает портов номера и IP-адреса в числовом формате, а не пытается выполнять обратное разрешение DNS для представления их символьных имен

Создал локальный порт 2024 на ws21, для переадресации трафика через ssh на localhost:80 на ws22
для этого на ws21 юзаю команду ```ssh -L 8080:localhost:80 10.20.0.20```

![Alt text](src/screenshots/p8.7.png)

Проверил соединение командой ```telnet 127.0.0.1 8080```

![Alt text](src/screenshots/p8.8.png)

На машине ws11 в файле /etc/ssh/sshd_config раскомментировал строку AllowTcpForwarding yes чтобы разрешить маршрутизацию по ssh
Чтобы работало удалённое перенаправление нужно также раскомментировать строку GatewayPorts yes
Перезапустил службу ssh и проверил статус командами sudo systemctl restart sshd и systemctl status sshd

![Alt text](src/screenshots/p8.9.png)

На машине ws11 в файле /etc/ssh/sshd_config раскомментировал строку AllowTcpForwarding yes чтобы разрешить маршрутизацию по ssh
Чтобы работало удалённое перенаправление нужно также раскомментировать строку GatewayPorts yes
Перезапустил службу ssh и проверил статус командами ```sudo systemctl restart sshd``` и ```systemctl status sshd```

Для настройки Remote TCP forwarding с ws22 на ws11 юзаю команду: ```ssh -R 8080:10.20.0.20 

Заходим на машину через созданный ssh-туннель
Чтобы выйти из ssh-туннеля ввожу exit

![Alt text](src/screenshots/p8.10.png)

![Alt text](src/screenshots/p8.11.png)

Проверил соединение командой ```telnet 127.0.0.1 8080```

![Alt text](src/screenshots/p8.12.png)