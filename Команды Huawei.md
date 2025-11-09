**СОЗДАНИЕ VLAN НА КОММУТАТОРЕ HUAWEI**

|<Huawei> system-view|*//переход в режим конфигурирования*|
| :- | :- |
|[Huawei] vlan 20|*//создание VLAN 20*|
|[Huawei-vlan20] quit|*//выход из режима конфигурирования VLAN*|
|[Huawei] display vlan summary|*//просмотр VLAN, созданных на коммутаторе*|

![](Aspose.Words.3420260d-e89c-43f6-9153-6b6894917d12.001.png)

**НАЗНАЧЕНИЕ ИНТЕРФЕЙСОВ КОММУТАТОРА В VLAN**

|[Huawei] interface gigabitethernet 0/0/1|*//режим конфигурирования интерфейса*|
| :- | :- |
|[Huawei-interface gigabitethernet 0/0/1] port link-type access|*//определение типа порта*|
|[Huawei-interface gigabitethernet 0/0/1] port default vlan 20|*//привязка VLAN к порту*|
|[Huawei-interface gigabitethernet 0/0/1] quit|*//выход из режима конфигурирования интерфейса*|
|[Huawei] display port vlan gigabitethernet 0/0/1|*//просмотр VLAN на интерфейсе*|



|[Huawei] interface gigabitethernet 0/0/1|*//режим конфигурирования интерфейса*|
| :- | :- |
|[Huawei-interface gigabitethernet 0/0/1] port link-type trunk|*//определение типа порта*|
|[Huawei-interface gigabitethernet 0/0/1] port trunk allow-pass vlan 20|*//привязка VLAN к порту*|
|[Huawei-interface gigabitethernet 0/0/1] quit|*//выход из режима конфигурирования интерфейса*|
|[Huawei] display port vlan gigabitethernet 0/0/1|*//просмотр VLAN на интерфейсе*|


**НАСТРОЙКА STP НА КОММУТАТОРАХ**

|<SW2> system-view|*//переход в режим конфигурирования*|
| :- | :- |
|[SW2] stp mode stp|*//конфигурирование протокола STP*|
|[\*SW2] stp root primary|*//определение коммутатора корневым*|
|[\*SW2] stp pathcost-standart legacy|*//определяем стоимость пути стандартным методом huawei*|
|[SW3] stp mode stp||
|[\*SW3] stp root secondary|*//определение резервного корневого коммутатора*|
|[\*SW3] stp pathcost-standart legacy||
|[SW3 interface gigabitethernet 0/0/1|*//режим конфигурирования интерфейса оконечного хоста*|
|[SW3-interface gigabitethernet 0/0/1] stp disable|*//выключение STP на порту*|
|[SW4] stp mode stp||
|[\*SW4] stp pathcost-standart legacy||
|[SW4] interface gigabitethernet 0/0/2|*//режим конфигурирования избыточного интерфейса*|
|[SW4-interface gigabitethernet 0/0/2] stp cost 10000|*//определение стоимости 10000*|
|[SW4-interface gigabitethernet 0/0/2] quit||
|[SW4] interface gigabitethernet 0/0/1||
|[SW4-interface gigabitethernet 0/0/1] stp disable||
|[SW1] stp mode stp||
|[\*SW1] stp pathcost-standart legacy||
|[SW1-SW4] stp enable|*//запуск процесса STP* *на всех коммутаторах*|
|[SW1-SW4] display stp brief|*//просмотр STP*|

**НАСТРОЙКА PORT SECURITY НА КОММУТАТОРАХ**


|<SW1> system-view||
| :- | :- |
|[SW1] interface gigabitethernet 0/0/1||
|[SW1-interface gigabitethernet 0/0/1] port-security enable|*//включение режима port-security*|
|[SW1-interface gigabitethernet 0/0/1] port-security mac-address sticky|*//включение запоминания MAC-адресов*|
|[SW1-interface gigabitethernet 0/0/1] port-security max-mac-num 2|*//включение максимального кол-ва (2 адреса)*|


**НАСТРОЙКА СТАТИЧЕСКИХ МАРШРУТОВ НА МАРШРУТИЗАТОРАХ HUAWEI**

|<HUAWEI> system-view|*//переход в режим конфигурирования*|
| :- | :- |
|[HUAWEI] ip route-static 0.0.0.0 0.0.0.0 172.16.0.1|*//добавление маршрута по-умолчанию*|
|[HUAWEI] ip route-static 192.168.1.0 mask  24 172.16.1.1 preference 20|*//определение приоритета маршрута\**|
|[HUAWEI] display ip routing-table|*//просмотр таблицы маршрутизации*|
|[HUAWEI] undo ip route-static 0.0.0.0 0.0.0.0 172.16.0.1|*//удаление маршрута по-умолчанию*|

\*чем меньше значение preference, тем выше приоритет маршрута, по-умолчанию значение preference **60**

**НАСТРОЙКА СТАТИЧЕСКОГО NAT НА МАРШРУТИЗАТОРАХ HUAWEI**


|<HUAWEI> system-view|*//переход в режим конфигурирования*|
| :- | :- |
|[HUAWEI] interface gigabitethernet 0/0/1|*//режим конфигурирования интерфейса WAN*|
|[HUAWEI-interface gigabitethernet 0/0/1] nat static global 80.80.80.1 inside 192.168.1.1|*//включение трансляции внутреннего адреса 192.168.1.1 во внешний 80.80.80.1*|
|[HUAWEI] display nat static|*//просмотр таблицы NAT*|

**НАСТРОЙКА ДИНАМИЧЕСКОГО NAT НА МАРШРУТИЗАТОРАХ HUAWEI**

|<HUAWEI> system-view|*//переход в режим конфигурирования*|
| :- | :- |
|[HUAWEI] acl number 999|*//создаем access-list*|
|[HUAWEI-acl-basic-999] rule 10 permit source 192.168.1.0 0.0.0.255|*//добавляем диапазон внутренних IP-адресов для трансляции с указанием обратной маски подсети*|
|[HUAWEI-acl-basic-999] quit||
|[HUAWEI] nat address-group 1 80.80.80.1 80.80.80.3|*//создаем диапазон внешних IP-адресов*|
|[HUAWEI-interface gigabitethernet 0/0/1] nat outbound 999 address-group 1 no-pat|*//включение динамической трансляции на WAN интерфейсе без преобразования портов*|
|[HUAWEI] display nat address-group 1|*//просмотр диапазона внешних адресов*|
|[HUAWEI] display nat outbound|*//информация об исходящем NAT*|


**OSPF** – протокол динамической маршрутизации, используемый для передачи информации в пределах одной автономной системы (IGP протокол), основан на алгоритме Link State (состояние связей).

 
- **OSPF** инкапсулируется в IP. Номер протокола 89
- Для передачи пакетов использует мультикастовые адреса:
- - 224.0.0.5 все маршрутизаторы OSPF
- - 224.0.0.6 все DR (выделенный маршрутизатор)
 
- Базовые термины **OSPF**:
- Идентификатор маршрутизатора (router ID, RID) — уникальное 32-битовое число, которое уникально идентифицирует маршрутизатор в пределах одной автономной системы
- Зона (area) — совокупность сетей и маршрутизаторов, имеющих один и тот же идентификатор зоны
- Объявление о состоянии канала (link-state advertisement, LSA) — единица данных, которая описывает локальное состояние маршрутизатора или сети. Например, для маршрутизатора LSA включает описание состояния каналов и отношений соседства. Множество всех LSA, описывающих маршрутизаторы и сети, образуют базу данных состояния каналов (LSDB).
- База данных состояния каналов (link state database, LSDB) — список всех записей о состоянии каналов (LSA). Встречается также термин топологическая база данных (topological database), употребляется как синоним базы данных состояния каналов
 

**СОСТОЯНИЕ СОСЕДСТВА OSPF**

- **Down** — начальное состояние процесса обнаружения соседей. Это состояние указывает на то, что от соседей не была получена свежая информация. В NBMA сетях Hello-пакеты могут отправляться и соседям в состоянии Down, однако с меньшей частотой (PollInterval)Attempt — это состояние имеет смысл только для соседей, которые присоединены к NBMA сетям. Оно указывает на то, что от соседа не была получена свежая информация и что нужно сделать попытку связаться с соседом. Это делается путём отправки соседу сообщений Hello с промежутком времени Hello Interval (в Cisco в этом состоянии сообщения Hello отправляются каждый PollInterval. Имеет смысл для соседей в NBMA сетях, которые заданы командой neighbor)
- **Init** — состояние, в котором находится маршрутизатор, отправивший своему соседу hello и ожидающий от него ответного hello
- **Two-way** — при получении ответных hello маршрутизатор должен увидеть в них свой RID в списке соседей. Если это так, то он устанавливает отношения и переходит в состояние two-wayКогда в широковещательном сегменте более 3х маршрутизаторов, то в этом состоянии остаются между собой маршрутизаторы DROTHER
- **Exstart** — маршрутизаторы определяют Master/Slave отношения на основании Router ID. Маршрутизатор с высшим RID становится Master-маршрутизатором, который определяет DD Sequence number, а также первым начинает обмен DD-пакетами
- **Exchange** — маршрутизаторы посылают друг другу database description пакеты (DD) с информацией о сетях, содержащихся в их собственной LSDB
- **Loading** — Если маршрутизатор видит, что части маршрутов нет в его LSDB, он посылает сообщение LSR с перечислением тех сетей, по которым он хочет получить дополнительную информацию.  Пока маршрутизатор находится в ожидании ответа в виде LSU сообщений, он пребывает в состоянии Loading
- **Full** — Когда маршрутизатор получил всю информацию и LSDB на обоих маршрутизаторах синхронизирована, оба маршрутизатора переходят в состояние fully adjacent (FULL).

**НАСТРОЙКА ПРОТОКОЛА OSPF НА МАРШРУТИЗАТОРАХ HUAWEI**

|<HUAWEI> system-view|*//переход в режим конфигурирования*|
| :- | :- |
|[HUAWEI] ospf 1 router-id 1.1.1.1|*//включение процесса OSPF и назначение router-id*|
|[HUAWEI-ospf-1] area 0|*//конфигурирование магистральной зоны (area 0)*|
|[HUAWEI-ospf-1-area 0] network 192.168.1.0|*//анонсирование сетей в OSPF*|
|[HUAWEI] display ospf peer|*//просмотр OSPF соседей*|
|[HUAWEI] display ip routing-table|*//просмотр таблицы маршрутизации*|
|[HUAWEI] display ospf interface|*//просмотр информации об интерфейсах, задействованных в OSPF*|

**СОСТОЯНИЯ СОСЕДСТВА BGP**

- **Idle** — начальное состояние, ничего не происходит. BGP-сессия может висеть в состоянии IDLE, если нет маршрута к соседу.
- **Connect** — маршрутизатор слушает TCP пакеты на 179 порт. В таком состоянии находится очень мало.
- **Active** — маршрутизатор отправил TCP с флагом SYN на 179 порт соседу и ждет ответа. BGP-сессия может зависнуть, если:
- не совпали IP-адреса с соседом;
- нет связности между соседями;
- не запущен BGP у соседа;
- не проходит 179 порт, например из-за access-list.
- **Open** — сразу после установления TCP-сессии отправляется сообщение OPEN для установления отношений соседства и обмена базовыми параметрами:
- версия протокола; 
- номер AS (номера автономных систем должны совпадать;
- Hold Timer (максимальное время в секундах между сообщениями keepalive и update);
- Router ID (должны различаться). 
- Open sent – маршрутизатор отправил сообщение Open и ждет ответа;
- Open confirm – маршрутизатор получил в ответ сообщение Open и затем отправляет сообщение Keepalive. 
- **Established** — соседство установлено. Далее маршрутизаторы начинают обмен маршрутной информацией, отправляют сообщения Update. 




**НАСТРОЙКА ПРОТОКОЛА BGP НА МАРШРУТИЗАТОРАХ HUAWEI**

|<HUAWEI> system-view|*//переход в режим конфигурирования*|
| :- | :- |
|[HUAWEI] interface loopback 0|*//создание loopback интерфейса*|
|[HUAWEI-interface loopback 0] ip address 1.1.1.1 24|*//конфигурирование интерфейсов*|
|[HUAWEI] bgp 62001|*//создание процесса BGP с ASN 62001*|
|[HUAWEI-bgp] router-id 1.1.1.1|*//назначение router-id*|
|[HUAWEI-bgp] peer 80.80.80.2 as-number 62002|*//добавление BGP-соседа*|
|[HUAWEI-bgp] graceful-restart|*//плавный перезапуск BGP маршрутов*|
|[HUAWEI-bgp] peer 80.80.80.2 description Rostelecom|*//подпись соседа*|
|[HUAWEI-bgp] ipv4-family unicast|*//режим конфигурирования маршрутов*|
|[HUAWEI-af-ipv4] peer 80.80.80.2 enable|*//включение соседа*|
|[HUAWEI-af-ipv4] network 192.168.1.0 24|*//анонсирование сетей в BGP*|
|[HUAWEI-af-ipv4] peer 80.80.80.2 ip-prefix № import|*//применение фильтрации входящих маршрутов*|
|[HUAWEI-af-ipv4] peer 80.80.80.2 ip-prefix № export|*//применение фильтрации исходящих маршрутов*|
|[HUAWEI] ip ip-prefix № permit 10.10.10.0 24|*//создание префикс-листа, разрешающего сеть 10.10.10.0/24 и запрещающего все остальное*|
|[HUAWEI] ip ip-prefix № deny 0.0.0.0 0||
|[HUAWEI-bgp] peer 80.80.80.2 preferred-value 300|*//установка соседу значения localpref 300*|
|||


**НАСТРОЙКА DHCP-СЕРВЕРА НА МАРШРУТИЗАТОРАХ И КОММУТАТОРАХ HUAWEI**

|<HUAWEI> system-view|//переход в режим конфигурирования|
| - | - |
|[HUAWEI] dhcp enable|//включение сервиса DHCP|
|[HUAWEI] interface gigabitethernet 0/0/0|//режим конфигурирования интерфейса|
|[HUAWEI-gigabitethernet-0/0/0] ip address 10.10.10.0 24|//назначение шлюза по-умолчанию для клиента и диапазона IP-адресов|
|[HUAWEI-gigabitethernet-0/0/0] dhcp select interface|//создание dhcp-сервера на интерфейсе|
|[HUAWEI] display ip pool|//просмотр пула IP-адресов|
|[HUAWEI] ip pool pool\_name|//создание глобального пула IP-адресов|
|[HUAWEI-ip-pool-pool-name] network 11.11.11.0 mask 24|//добавление диапазона адресов|
|[HUAWEI-ip-pool-pool-name] excluded-ip-address 11.11.11.2 11.11.11.4|//исключение из пула IP-адресов с 11.11.11.2, 11.11.11.3, 11.11.11.4|
|[HUAWEI-ip-pool-pool-name] gateway-list 11.11.11.1|//назначение шлюза в сервисе DHCP|
|[HUAWEI-gigabitethernet-0/0/1] dhcp select global|//включение режима DHCP с глобальным пулом|
|[HUAWEI-gigabitethernet-0/0/1] ip address 11.11.11.1 24|//назначение адреса на интерфейсе в сторону клиентов|
|[HUAWEI] interface Vlanif 20|//создание vlan интерфейса, с которого будем раздавать DHCP|
|||











|**Основные типы ACL в HUAWEI**|||
| :-: | :- | :- |
|Тип ACL|Правила определения|Диапазон номеров|
|Стандартный ACL|Определяет правила<br>` `на основе исходных IP-адресов|2000-2999|
|Расширенный ACL|Определяет правила на основе IP-адресов назначения и источника, типов протоколов, номеров портов|3000-3999|
|ACL уровня L2|Определяет правила на основе заголовков Ethernet-кадров (MAC-адрес источника, назначения, типов протоколов L2)|4000-4999|



**НАСТРОЙКА ACL НА МАРШРУТИЗАТОРАХ HUAWEI**

|<HUAWEI> system-view|//переход в режим конфигурирования|
| - | - |
|[HUAWEI] acl name test 2000|//создание стандартного ACL|
|[HUAWEI-acl-test-2000] rule 5 permit 10.10.10.1|//создание правил отбора трафика в ACL|
|[HUAWEI-gigabitethernet-0/0/0] traffic-filter inbound acl name test|//применение ACL на входящий трафик интерфейса|
|[HUAWEI-gigabitethernet-0/0/0] traffic-filter outbound acl name test|//применение ACL на исходящий трафик интерфейса|
|||


**Link aggregation** **(агрегирование каналов)** – технология, позволяющая объединить несколько физических каналов в один логический для увеличения пропускной способности канала и повышения надежности.

- **LACP** **(link aggregation control protocol)** – протокол агрегирования каналов, описанный в стандарте 802.3ad. Использует LACPDU сообщения для обмена служебной информацией с соседней группой интерфейсов.
- технология Link aggregation на маршрутизаторах Huawei называется Ethernet-Trunk
- можно создавать L2 и L3 агрегированные каналы
- агрегировать каналы можно в статическом (ручном) режиме или в режиме LACP (динамическая агрегация)
- в Ethernet Trunk можно добавить 8 физических интерфейсов
- один физический интерфейс может быть добавлен только в один Ethernet Trunk 

**НАСТРОЙКА ETH-TRUNK** **НА МАРШРУТИЗАТОРАХ И КОММУТАТОРАХ HUAWEI**




|<huawei> system-view|*//переход в режим конфигурирования*|
| :- | :- |
|[huawei] interface eth-trunk 20|*//создание eth-trunk 20*|
|[huawei-interface-eth-trunk-20] undo portswitch |*//перевод eth-trunk в режим L3*|
|[huawei-interface-eth-trunk-20] ip address 10.10.10.1 24 |*//назначение IP-адреса L3 eth-trunk* |
|[huawei-interface-eth-trunk-20] mode lacp-static |*//включение режима LACP*|
|[huawei-interface-eth-trunk-20] trunkport gigabitethernet 0/0/0 to interface gigabitethernet 0/0/1 |*//добавление в eth-trunk интерфейсов (максимум 8)*|
|**ПРОСМОТР ИНФОРМАЦИИ О СОЗДАННЫХ ETH-TRUNK**||
|[huawei] display eth-trunk | |
|[huawei] display lacp statistics eth-trunk| |
|[huawei] display interface eth-trunk | |
| | |







**НАСТРОЙКА IPSEC** **НА МАРШРУТИЗАТОРАХ HUAWEI**

|<huawei> system-view|*//переход в режим конфигурирования*||
| :- | :- | :- |
|[huawei] ipsec proposal 1|*//создание предложения параметров шифрования IPSec*||
|[huawei-ipsec-proposal-1] esp authentication-algorithm …|*//выбор алгоритма аутентификации*||
|[huawei-ipsec-proposal-1] esp encryption-algorithm …|*//выбор алгоритма шифрования*||
|[huawei] ike proposal 1|*//создание предложения параметров* *обмена ключами*||
|[huawei-ike-proposal-1] encryption-algorithm …|*//выбор алгоритма проверки подлинности*||
|[huawei-ike-proposal-1] dh group|//выбор группы алгоритма Диффи-Хеллмана||
|[huawei-ike-proposal-1] sa duration 30000|//время жизни ключей в секундах||
|[huawei] ike peer NAME v1|//создание соседа IPSec||
|[huawei-ike-peer-NAME-v1] pre-shared-key cipher 123|//аутентификация с предварительным ключем||
|[huawei-ike-peer-NAME-v1] ike proposal 1|//применение шаблона с ike||
|[huawei-ike-peer-NAME-v1] local-address 50.50.50.2 |//IP-адрес маршрутизатора, с которого будет строиться IPSec||
|[huawei] ipsec profile name|//создание профиля IPSec||
|[huawei-ipsec-profile-name] ike-peer NAME|//привязка к профилю соседа IPSec||
|[huawei-ipsec-profile-name] proposal 1|//привязка к профилю параметров шифрования ||
|[huawei-ipsec-profile-name] sa duration time-based …|//время жизни IPSec SA в секундах||
|[huawei] interface Tunnel 0/0/1|//создание туннельного интерфейса||
|[huawei-interface-Tunnel0/0/1] ip address 1.1.1.1 30|//применение туннельного IP-адреса||
|[huawei-interface-Tunnel0/0/1] tunnel-protocol ipsec|//задание типа туннельного протокола интерфейсу||
|[huawei-interface-Tunnel0/0/1] source 50.50.50.2|//локальный IP-адрес туннеля||
|[huawei-interface-Tunnel0/0/1] destination 80.80.80.2|//удаленный IP-адрес туннеля||
|[huawei-interface-Tunnel0/0/1] ipsec profile name|//привязка профиля IPSec к туннельному интерфейсу||
|[huawei] display ipsec sa brief|//просмотр сессий IPSec||
| | ||

