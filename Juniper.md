### Справка по cli
help topic  - используется для отображения справки по использованию подсистем
help reference - используется для отображения краткой справки по использолванию пунктов контекстов конфигурации

редактирование кандидатской конфигурации
edit protocols ospf area 51 stub
или
set services web-management http port 8080

удалить конфигурацию
delete system services

деактивировать конфигурацию без удаления
deactivate system services ssh root-login


### Команды для работы с конфигурацией

Для эффективной работы с конфигурацией используются команды:

  * **rename** – переименовывает запись конфигурации

    ```
    [edit interfaces fe-0/0/7 unit 0 family inet]
    root@NetEdu# rename address 3.13.11.37/24 to address 3.1.1.3/24
    ```

  * **replace pattern** – заменяет запись конфигурации

    ```
    [edit interfaces]
    root@NetEdu# replace pattern fe-0/0/6 with fe-0/0/7
    ```

  * **copy** – копирует запись конфигурации в другую запись

    ```
    [edit interfaces]
    root@NetEdu# copy fe-0/0/7 to fe-0/0/6
    ```

  * **insert** – вставка записи конфигурации в другую локацию

    ```
    [edit firewall filter NetEdu]
    root@NetEdu# insert term 3 before term 1
    ```

  * **annotate** – добавление комментария к записи конфигурации


### Применение конфигурации

* Команда **commit** используется для применения конфигурации

  ```
  root@NetEdu# commit
  commit complete

  [edit]
  root@NetEdu#
  ```

* На устройствах с отказоустойчивыми RE команда **commit synchronize** активирует и синхронизирует конфигурацию на обоих RE

* Команда **commit check** используется для проверки конфигурации на синтаксические ошибки

  ```
  root@NetEdu# commit check
  [edit interfaces fe-0/0/5 unit 0]
      'family'
          When ethernet-switching family is configured on an interface, no other family type can be configured on the same interface.
  error: configuration check-out failed
  ```

### Подтверждение конфигурации

* Удалённая настройка связана с риском потери доступа к устройству
* Удобно использовать команду **commit confirmed <timeout>**

  * Команда запускает таймер, в течение которого ожидается второй commit
  * Если второй commit не происходит за время, заданное таймером, то выполняется последовательность команд **rollback 1, commit**

```
root@NetEdu# commit confirmed 5
commit confirmed will be automatically rolled back in 5 minutes unless confirmed
commit complete

# commit confirmed will be rolled back in 5 minutes
[edit]
root@NetEdu#
# commit confirmed will be rolled back in 4 minutes
[edit]
root@NetEdu#
```

### Запланированное применение

* Commit можно запланировать, используя команду **commit at time**

  * Для просмотра и очистки запланированных commit используются команды **show system commit** и **clear system commit**
  * Комментарии добавляются с помощью **commit comment 'comment-string'**

* **Commit and-quit** применяет изменения и выходит из режима конфигурации

```
root@NetEdu# commit at 21:30:55
configuration check succeeds
commit at will be executed at 2017-09-12 21:30:55 GMT-7
Exiting configuration mode

root@NetEdu>
```

```
root@NetEdu# commit and-quit
commit complete
Exiting configuration mode

root@NetEdu>
```

### Просмотр изменений

* Команда режима конфигурации **show | compare** показывает разницу между активной и кандидатской конфигурацией

  * можно проводить сравнения между активной и сохранённой ранее конфигурацией: **show | compare rollback <number>**
  * и с содержимым произвольного файла: **show | compare <filename>**

```
[edit]
root@NetEdu# show | compare
[edit system services]
+       ftp;
-       ssh {
-           root-login deny;
-           protocol-version v2;
-       }
[edit interfaces vlan]
+   unit 11 {
+       family inet {
+           address 10.170.134.1/30;
+       }
+   }
```

---


### Сохранение файла конфигурации

* Команда **save** сохраняет кандидатскую конфигурацию в файл

  * Сохраняется часть конфигурации на текущем уровне иерархии и ниже
  * Если не определить путь, файл сохранится в домашнюю папку пользователя

```
[edit]
root@NetEdu# save filename
```

```
[edit]
root@NetEdu# save path/filename
```

```
[edit]
root@NetEdu# save ftp://user:password@host/path/filename
```

```
[edit]
root@NetEdu# save scp://user@host/path/filename
```

﻿
### Начальная конфигурация
- Аккаунт root по умолчанию не имеет пароля
- Задание пароля для учетной записи root является обязательным условием перед применением любых изменений в конфигурации

```
[edit]
root# edit system

[edit system]
root# set root-authentication plain-text-password
New password:
Retype new password:
            ! Пароль должен соответствовать минимальным требованиям,
            ! иначе отобразится ошибка
```

• Рекомендуется настроить следующие параметры:
 – Hostname
 – Системное время
 – Сервисы для удаленного доступа (telnet или ssh)
 – Интерфейс управления и статический маршрут для трафика управления

 *Настройка Hostname:*

```
[edit system]
root# set host-name NetEdu
```
*Настройка системного времени:*
```
[edit system]
root# set time-zone Asia/Novosibirsk
```
```
[edit system]
root# run set date 201710031342.00
```
*Настройка telnet и ssh:*
```
[edit system]
root# set services ssh
```

```
[edit system]
root# set services telnet
```
*Настройка интерфейса управления и статического маршрута:*

```
[edit]
root# set interfaces fxp0 unit 0 family inet address 10.170.1.2/24
```
```
[edit]
root# set routing-options static route 10.170.0.0/16 next-hop 10.170.1.1
```

*Настройка сообщения при входе в систему:*

```
[edit]
root# set system login message "Authorized access only!"
```

```
[edit]
root# commit and-quit
```

Authorized access only!
NetEdu (ttyp0)
login: root
Password: *******

```
--- JUNOS 14.1R1.10 built 2014-06-07 09:37:07 UTC
root@NetEdu%
```
### Спасительная конфигурация

• Спасительная конфигурация (rescue config) предназначена для экстренного решения проблем с конфигурацией

• Содержимое спасительной конфигурации задается пользователем
 – По умолчанию спасительной конфигурации нет
 – Создание спасительной конфигурации:

```
root@NetEdu> request system configuration rescue save
```
 – Удаление спасительной конфигурации:
```
root@NetEdu> request system configuration rescue delete
```
 – Откат на спасительную конфигурацию:
```
[edit]
root@NetEdu# rollback rescue
load complete
```
```
[edit]
root@NetEdu# commit
commit complete
```

### Порядок аутентификации
• Можно одновременно настроить RADIUS и TACACS+ на устройстве
• Можно задать порядок использования методов аутентификации

```
[edit]
root@NetEdu# show system authentication-order
authentication-order [ radius tacplus password ];
```
### Обозначение физических интерфейсов

• Большинство интерфейсов именуется в соответствии с конвенцией
 – Тип среды интерфейса (ge, so, at и прочие)
 – Слот линейной карты (FPC, Flexible PIC concentrator)
 – Слот интерфейсной карты на линейной карте (PIC, Physical interface card)
 – Номер порта на интерфейсной карте

• Пример обозначения интерфейса:
 – ge-0/2/3 порт 3 Gigabit Ethernet PIC в слоте 2 на FPC 0

• Нумерация портов и слотов начинается с 0, а не с 1

*Обозначение других интерфейсов*

• Часто используемые интерфейсы
 - ge: Gigabit Ethernet interface
 - fe: FastEthernet 100 Mbit/s
 - xe: 10-Gigabit Ethernet
 - et: 40G или 100G Ethernet
 – ae: Aggregated Ethernet interface
 – as: Aggregated SONET interface
 – vlan: VLAN interface
 **lt** Logical Tunnel interface. Логические туннели внутри JUNOS, например для MPLS-тестов.
**mtun** Multicast Tunnel. Внутренние интерфейсы для мультикастовых механизмов.
**ipip** IP-over-IP туннелирование. Внутренние интерфейсы для IP-туннелей.
**tap** Virtual TAP. Виртуальный интерфейс для тестов/мониторинга.
**gr / gre** GRE-туннель.
**lo0** Loopback. Виртуальный интерфейс, аналог Loopback на Cisco.
**vlan** VLAN-interface. Логический интерфейс для VLAN'ов.
**irb** Integrated Routing and Bridging. Интерфейс-SVI, как SVI на Cisco, сочетает L2 и L3.
**pp0 / ppp** PPP интерфейсы (точка-точка).
**st0** IPSec secure tunnel. Интерфейс для VPN IPsec на SRX.
**reth** Redundant Ethernet (HA-кластер SRX). Это логический интерфейс, который "плавает" между двумя устройствами.
**fab** Fabric interface. Интерфейсы для кластерных связей.

### Логические интерфейсы
• Каждый дескриптор физического интерфейса может обслуживать один или несколько дескрипторов логических интерфейсов
 – Такие дескрипторы (unit) позволяют сопоставить один или несколько логических интерфейсов с одним физическим            
• Создание множества логических интерфейсов полезно в случаях, где несколько соединений канального уровня связаны с одним физическим интерфейсом
Если переводить «unit» из Junos на язык Cisco, то самое близкое понятие почти всегда subinterface.
То есть логика такая:
**В Junos:**
ge-0/0/14.51
где .51 это unit 51
**В Cisco:**
interface GigabitEthernet0/0/14.51
где .51 это subinterface 51
Оба они делают одно и то же: дают создать несколько логических интерфейсов поверх одного физического порта.

### Проверка состояния интерфейсов

• Команда **show interfaces terse** отображает состояние интерфейсов
```
root@NetEdu> show interfaces ge-0/0/0 terse
Interface        Admin Link Proto    Local                 Remote
ge-0/0/0         up    up
ge-0/0/0.0       up    up   inet     10.172.254.1/24
                               inet6     3333::1/64
                                         fe80::2a8a:1cff:fed8:2018/64
```

```
root@NetEdu> show interfaces fe-0/0/7 terse
Interface        Admin Link Proto    Local                 Remote
fe-0/0/7         up    down
fe-0/0/7.0       up    down inet     10.174.25.1/24
                                     192.168.44.190/24
```
***Примеры конфигурации интерфейсов***

```
root@NetEdu# show interfaces
fe-0/0/2 {
    unit 0 {
        description "###To-Customers###";
        family ethernet-switching {
            port-mode trunk;
            vlan {
                members [ LAN Management ];
            }
        }
    }
}
vlan {
    unit 9 {
        description "###To-MNG_network###";
        family inet {
            address 10.170.6.17/28;
        }
    }
    unit 10 {
        description "###To-LAN###";
        family inet {
            address 10.171.101.1/24;
        }
    }
}
```

```
root@NetEdu# show vlans
LAN {
    vlan-id 10;
    l3-interface vlan.10;
}
Management {
    vlan-id 9;
    l3-interface vlan.9;
}
```
***Пример как это выглядит на Cisco.***
Cisco версия:
```
interface FastEthernet0/0/2
 description ###To-Customers###
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 9,10
```
SVI для VLAN:
```
interface Vlan9
 description ###To-MNG_network###
 ip address 10.170.6.17 255.255.255.240
```

```
interface Vlan10
 description ###To-LAN###
 ip address 10.171.101.1 255.255.255.0
```

Создание VLAN:

```
vlan 9
 name Management

vlan 10
 name LAN
```
***Примеры конфигурации интерфейсов***

```
[edit]
admin@NetEdu# show interfaces fe-0/0/2
description "###To-Customers###";
vlan-tagging;
unit 9 {
    description "###To-MNG_network###";
    vlan-id 9;
    family inet {
        address 10.170.6.17/28;
    }
}
unit 10 {
    description "###To-LAN###";
    vlan-id 10;
    family inet {
        address 10.171.101.1/24;
    }
}
```
в Junos интерфейс fe-0/0/2 работает в режиме **vlan-tagging**, то есть это полноценный trunk с routed subinterfaces.
В Cisco это будет выглядеть как набор **subinterfaces с dot1q**, потому что IP висит на каждом VLAN прямо на физическом интерфейсе, а не через SVI.
Cisco эквивалент:
```
interface FastEthernet0/0/2
 no ip address
```
Subinterfaces:
```
interface FastEthernet0/0/2.9
 description ###To-MNG_network###
 encapsulation dot1q 9
 ip address 10.170.6.17 255.255.255.240
```
```
interface FastEthernet0/0/2.10
 description ###To-LAN###
 encapsulation dot1q 10
 ip address 10.171.101.1 255.255.255.0
```

***Примеры конфигурации интерфейсов***

```
[edit]
admin@NetEdu# show chassis
aggregated-devices {
    ethernet {
        device-count 1;
    }
}
```

```
[edit]
admin@NetEdu# show interfaces ae0
aggregated-ether-options {
    lacp {
        active;
    }
}
unit 0 {
    family ethernet-switching {
        port-mode trunk;
        vlan {
            members [ Service-01 Service-02 Service-03 ];
        }
    }
}
```

```
[edit]
admin@NetEdu# show interfaces
...
fe-0/0/2 {
    fastether-options {
        802.3ad ae0;
    }
}
fe-0/0/3 {
    fastether-options {
        802.3ad ae0;
    }
}
fe-0/0/4 {
    fastether-options {
        802.3ad ae0;
    }
}
...
```

Вот примерно эквивалент того, что на Juniper сделано через ae0 с LACP и trunk с несколькими VLAN.

Конфигурация для Cisco будет выглядеть так:

```

interface range FastEthernet0/2 - 4
  switchport mode trunk
  switchport trunk allowed vlan  Service-01,Service-02,Service-03
  channel-group 1 mode active


interface Port-channel1
  switchport mode trunk
  switchport trunk allowed vlan  Service-01,Service-02,Service-03
```
---

﻿###Network Time Protocol

• Протокол NTP используется для синхронизации часов сетевых устройств с точным источником времени
• NTP использует иерархическую сеть
– Каждый уровень имеет свой номер, называемый слой (Stratum)
– Слой №1 это первичные серверы
• Junos OS поддерживает клиент, сервер и симметричный режим NTP
– Также поддерживается аутентификация и broadcast
```
[edit]
root@NetEdu# show system ntp
boot-server 192.168.206.10;
server 192.168.206.10;
server 192.168.206.170;
source-address 192.168.161.98;
```
### Мониторинг NTP
• Команда show ntp associations отображает статус синхронизации
```
root@NetEdu> show ntp associations
===============================================================
    remote           refid          st  t  when  poll  reach  delay   offset  jitter
===============================================================
*net-monitor.net   212.164.119.161   2  -   38    64     3    2.912  -3.175   5.292
+192.168.206.170   212.164.119.161   2  -   34    64     3    4.097  -2.388   0.193
```
Разберём  каждую колонку, чтобы стало по-настоящему понятно, что ты смотришь в `show ntp associations`. Это одна из тех команд, которые выглядят страшновато, пока не поймёшь логику. Теперь пойдем по колонкам.

**remote**
Адрес или имя NTP сервера, с которым устройство общается.

**refid**
Это источник времени, который использует тот сервер.
Если оба сервера синхронизируются от одного источника, ты увидишь одинаковый refid.

**st (stratum)**
Это уровень источника времени.
• 1 это атомные часы, GPS и прочие первичные.
• 2 это устройства, синхронизированные с первичными.
• Твой случай: оба сервера stratum 2.

**t**
Тип соединения.
Обычно там "-" для обычных NTP peer/серверов. Это не особо важная колонка.

**when**
Сколько секунд прошло с последнего запроса NTP.
Чем свежее — тем лучше.
Тут 38 секунд и 34 секунды очень ок.

**poll**
Как часто устройство опрашивает сервер, в секундах.
64 означает раз в 64 секунды. NTP сам корректирует этот интервал в зависимости от стабильности связи.

**reach**
Это маленькая восьмибитная маска доступности сервера.
Цифра показывается в восьмеричном виде.
`3` — это двоичное `00000011`.
Это означает, что несколько последних опросов были успешными, но не все восемь.
Если reach падает до 0 — сервер считается недоступным.

**delay**
Задержка до сервера.
Просто RTT: туда-обратно.
Маленькая задержка — это хорошо.

**offset**
Разница между временем сервера и локальными часами в миллисекундах. Устройство старается держать offset близким к нулю.
Отрицательное значение значит, что твои часы чуть спешат.
Значения в районе нескольких миллисекунд отлично. Если offset уходит в сотни миллисекунд — уже плохо.

**jitter**
Разброс измерений offset.
Это "дрожание", нестабильность канала или времени.
Маленький jitter значит стабильность.
Тут у одного источника jitter 5.292 — так себе, но терпимо, у другого 0.193 — вообще класс.

***Теперь самое важное: специальные символы перед именем сервера.***

*Текущий выбранный сервер NTP. Именно с ним синхронизированы часы.
+Допустимый альтернативный сервер. Он подходит, но не лучший.
–Тоже сервер, но NTP посчитал его хуже и не использует.
x Этот сервер признан "врут". NTP не доверяет ему.

 Сервер слишком низкого качества, игнорируется.
В выводе символы слева от имени сервера обозначают статус, который NTP присвоил каждому источнику времени.

***(звёздочка)**
  Этот сервер выбран как основной источник времени.
  То есть именно от него система сейчас синхронизирует свои часы.
  Если видишь *, значит NTP работает и синхронизация есть.

**+(плюс)**
  Этот сервер признан хорошим и качественным источником, но чуть хуже, чем тот, который отмечен звездочка.
  Он стоит "в очереди" как резерв. Если основной источник пропадёт, NTP автоматически переключится на плюс.
Обычно одна звёздочка, а плюсов может быть несколько.

### Настройка SNMP

• SNMP настраивается на уровне иерархии [edit snmp]
– Junos OS поддерживает SNMP версий 1, 2c и 3

```
[edit]
admin@NetEdu# show snmp
description Junos_for_Labs;
location Cloud;
contact "Admin - 9999";
community NEdu2017 {
    authorization read-only;
    clients {
        10.190.245.0/24;
    }
}
trap-group Test_trap {
    version v2;
    categories {
        link;
        routing;
    }
    targets {
        192.168.206.10;
    }
}
```
Пройдёмся по конфигу построчно, SNMP в JunOS выглядит громоздко, но логика у него аккуратная и понятная. Разбираем:

*description Junos_for_Labs;*
Просто описание SNMP-конфига. Это строка, которая может отображаться в SNMP-инструментах как sysDescr. Чисто косметика, но полезная.

*location Cloud;*
Это sysLocation — обычно указывают физическое местоположение устройства. Здесь указано "Cloud", значит где-то в облачной части лаборатории.

*contact "Admin - 9999";*
Это sysContact. Любой SNMP-мониторинг покажет, к кому обращаться по устройству. В идеале: email, телефон, имя администратора.

*community NEdu2017 {*
Это SNMP community — строка, которая служит "паролем" в SNMP v1/v2c. В данном случае community называется NEdu2017.

*authorization read-only;*
SNMP community имеет доступ только на чтение (RO).
То есть мониторинг может собирать данные, но не менять ничего на устройстве. Это правильная, безопасная настройка.

*clients {
10.190.245.0/24;
}*
Этот блок ограничивает, кто может обращаться по SNMP.
Только устройства из сети 10.190.245.0/24 получат доступ с community NEdu2017. Это важная мера безопасности.

*trap-group Test_trap {*
Задаём группу SNMP-трэпов, названную Test_trap.
Трэпы — это когда устройство само сообщает о событиях (линк-даун, изменение маршрутов и т. д.), а не когда мониторинг сам ходит опрашивать.

*version v2;*
Тип трэпов: SNMPv2c.
Самый распространённый вариант.

*categories {
link;
routing;
}*
Указываем, какие события отправлять:
link — события, связанные со статусом интерфейсов (up/down);
routing — события маршрутизации (изменения протоколов, перестроения, соседства и т. д.).

*targets {
192.168.206.10;
}*
Адрес SNMP-менеджера (сервер мониторинга), куда устройство будет отправлять трэпы. Сюда придут уведомления о событиях из категорий link и routing.

***Мониторинг SNMP***

• Мониторинг SNMP-агента с помощью систем управления сетью (Zabbix, Cacti и пр.)

• Мониторинг SNMP-агента осуществляется с использованием трассировки, syslog и команд show snmp <...>

• Проход по MIB и получение значений объектов через CLI

```
admin@NetEdu> show snmp mib walk jnxOperatingDescr
jnxOperatingDescr.1.1.0.0 = midplane
jnxOperatingDescr.2.1.0.0 = PEM 0
jnxOperatingDescr.4.1.0.0 = SRX210 Chassis fan
jnxOperatingDescr.7.1.0.0 = FPC: FPC @ 0/*
jnxOperatingDescr.8.1.1.0 = PIC: 2x GE, 6x FE, 1x 3G @ 0/0/*
jnxOperatingDescr.9.1.0.0 = Routing Engine
jnxOperatingDescr.9.1.1.0 = USB Hub
```

### Автоматическое резервное копирование

• Настраивается на уровне иерархии [edit system archival]

• Можно настроить регулярное резервное копирование в запланированные интервалы, либо всякий раз, когда применяется конфигурация.

```
[edit]
admin@NetEdu# show system archival
configuration {
    transfer-on-commit;
    archive-sites {
        "scp://storage@10.254.0.5:/archive" password "$9$..."; ## SECRET-DATA
        "ftp://storage@10.254.0.5:/archive" password "$9$..."; ## SECRET-DATA
    }
}
```

### Мониторинг системы

• Команды show system <...> показывают информацию о системе

```
root@NetEdu> show system ?
Possible completions:
  alarms            Show system alarm status
  audit             Show file system MD5 hash and permissions
  auto-snapshot     Show auto-snapshot status when system booted from alternate slice
  autoinstallation  Show autoinstallation information
  autorecovery      Show autorecovery information
  boot-messages     Show boot time messages
  buffers           Show buffer statistics
! вывод сокращен
```
Наиболее часто используемые:
– alarms: показывает текущие аварии системы
– boot-messages: показывает журнал последней загрузки системы
– connections: показывает статус локальных TCP и UDP соединений
– statistics: позволяет просматривать статистику для различных протоколов
– storage: показывает состояние хранилища файловой системы

*Мониторинг шасси*

• Команды show chassis <...> производят мониторинг шасси

```
root@NetEdu> show chassis ?
Possible completions:
  alarms           Show alarm status
  cluster          Show chassis cluster information
  craft-interface  Show craft interface status
  environment      Show component status and temperature, cooling system speeds
  fan              Show fan and fan tray information
! вывод сокращен
```

• Наиболее часто используемые:
– alarms: показывает текущие аварии шасси
– environment: показывает компоненты и состояние окружающей среды, а также текущие скорости системы охлаждения
– hardware: показывает инвентарь установленных аппаратных компонентов, вместе с серийным номером каждого компонента
– routing-engine: показывает информацию о рабочем состоянии и загрузке движка маршрутизации

*Проверка состояния интерфейсов*

• Команда show interfaces позволяет проверить различные детали и информацию о статусе интерфейсов

• Использование дополнительных опций позволяет увеличить или уменьшить количество отображаемых деталей.

```
root@NetEdu> show interfaces ge-0/0/1 ?
Possible completions:
  <[Enter]>         Execute this command
  brief             Display brief output
  descriptions      Display interface description strings
  detail            Display detailed output
  extensive         Display extensive output
  media             Display media information
  routing-instance  Name of routing instance
  snmp-index        SNMP index of interface
  statistics        Display statistics and detailed output
  switch-port       Front end port number (0..15)
  terse             Display terse output
  |                 Pipe through a command
```

Вот что означают основные режимы:

**terse**
Это ультракороткий вывод.
Показывает только самое необходимое: состояние интерфейса, протокол up/down, немного статистики.
Используется для «быстро понять, живой порт или нет».

Типичное применение:
• проверить статус линка
• проверить VLAN/LLDP по минимуму
• пробежаться по всем интерфейсам разом

**brief**
Чуть подробнее, чем terse.
Появляется информация об IP-адресах, описаниях, иногда протокольном статусе.

Если тебе нужно видеть IP + статус — brief отлично подходит.

**detail**
Уже полноценный, «рабочий» вывод.
Здесь ты увидишь:
• состояние физики
• L1/L2 параметры
• MAC-адреса интерфейса
• ошибки (CRC, drops, input/output errors)
• скорость/дуплекс
• информация по протоколам (если интерфейс участвует в L3)
Для реального troubleshooting интерфейсов — лучший выбор.

**extensive**
Это максимально подробный вариант.
Содержит абсолютно всю возможную информацию:
• длинные статистики трафика
• состояние очередей
• различную расширенную телеметрию
• подробные счётчики ошибок
• история flaps (время последнего падения/подъёма)
• более глубокие протокольные данные

Используется, когда:
• нужно понять, что происходит «под капотом»
• ищешь редкие или нестандартные ошибки
• нужно посмотреть историю интерфейса
• требуется максимально полный дамп для анализа

*Мониторинг в реальном времени*

• Команда monitor interface <interface> отображает в реальном времени счетчики пакетов, байтов и ошибок

```
NetEdu                                               Seconds: 8               Time: 16:28:59
                                                                            Delay: 0/0/4

Interface: ge-0/0/0, Enabled, Link is Up
Encapsulation: Ethernet, Speed: 1000Mbps
Traffic statistics:
  Input bytes:       17038   (3928 bps)           [1808]
  Output bytes:      1436    (616 bps)            [156]
  Input packets:     172     (5 pps)              [24]
  Output packets:    18      (0 pps)              [2]

Error statistics:
  Input errors:      0                            [0]
  Input drops:       0                            [0]
  Input framing errors: 0                         [0]
  Policed discards:  0                            [0]
  L3 incompletes:    0                            [0]
  L2 channel errors: 0                            [0]
  L2 mismatch timeouts: 0                         [0]

                                             0  Carrier transiti  [0]

Next='n', Quit='q' or ESC, Freeze='f', Thaw='t', Clear='c', Interface='i'
```

Разберём monitor interface. Эта команда в JunOS очень удобная, но многие не до конца понимают, что именно она показывает.

1. Заголовок

```
Interface: ge-0/0/0, Enabled, Link is Up
Encapsulation: Ethernet, Speed: 1000Mbps
```

• Интерфейс включён логически
• Линк поднят физически
• Скорость 1 Gbps
• Тип — Ethernet

Это просто быстрый sanity check, что порт живой.

2. Traffic statistics — основная движуха
   Здесь два вида цифр:

Например строка:

```
Input bytes: 17038 (3928 bps) [1808]
```

Вот что это значит:

17038
Общее количество байтов, прошедшее через интерфейс с момента загрузки системы.

3928 bps
Текущая скорость трафика за последнюю секунду (или интервал обновления).

[1808]
Current Delta — сколько байтов пришло с момента предыдущего обновления экрана.
Это то, что меняется в реальном времени.


Input bytes
Сколько байтов приходит в интерфейс.

Output bytes
Сколько уходит.

Input packets
Сколько входящих пакетов.

Output packets
Сколько выходящих.

pps — packets per second
bps — bits per second

Запомнить главное: в квадратных скобках **дельта за интервал**, в скобках (…) **текущая скорость**, а слева **общий счётчик**.

3. Error statistics
   Это критически важная часть.

Каждый параметр:

**Input errors**
Все ошибки приема: CRC, alignment, giant/ runt, многое сюда попадает.

**Input drops**
Порт получил пакет, но вынужден был его выбросить (буфер переполнен, перегрузка).

**Input framing errors**
Проблемы с кадрами Ethernet (обычно физика или плохой кабель).

**Policed discards**
Полиция трафика (Policer) сработала и сбросила пакет.

**L3 incompletes**
Пакет пришел, но маршрутизатор не может обработать L3 (например ARP не найден).

**L2 channel errors**
Ошибки внутренней шины между интерфейсом и RE/DP.

**L2 mismatch timeouts**
Разные ошибки, связанные с задержками или нарушениями L2 кодировок.

В квадратных скобках всё так же Current Delta, то есть сколько ошибок добавилось за этот интервал.

4. Управление мониторингом

```
Next='n' 
Quit='q' или ESC
Freeze='f'
Thaw='t'
Clear='c'
Interface='i'
```

**n**
Переключение между интерфейсами (если ты мониторишь несколько).

**q или ESC**
Выход.

**f (Freeze)**
Заморозить вывод (пауза).

**t (Thaw)**
Разморозить — продолжить обновление.

**c (Clear)**
Обнулить текущие дельта-значения (в квадратных скобках).

**i**
Смена интерфейса.

5. Чем monitor interface отличается от show interfaces?

Это важно:
show interfaces
показывает статический снимок состояния, не в реальном времени.

monitor interface
работает как «живой график», обновляясь каждую секунду, даёт динамику трафика и ошибок.
Похож по смыслу на Linux `watch`, но с красивым форматированием.

Идеально подходит для:

• ловли всплесков трафика
• диагностики ошибок «в моменте»
• проверки QoS/policer
• поиска просадок по линку
• просмотра нагрузки на интерфейс прямо сейчас

• Команда monitor interface traffic отображает статистику в реальном времени по всем интерфейсам в табличном виде

```
NetEdu                                               Seconds: 48        Time: 16:43:07

Interface   Link   Input packets   (pps)     Output packets   (pps)
ge-0/0/0    Up     352             (5)       32               (0)
gr-0/0/0    Up     0               (0)       0                (0)
ip-0/0/0    Up     0               (0)       0                (0)
lt-0/0/0    Up     0               (0)       0                (0)
mt-0/0/0    Up     0               (0)       0                (0)
sp-0/0/0    Up     0               (0)       0                (0)
ge-0/0/1    Down   0               (0)       0                (0)
fe-0/0/2    Down   0               (0)       0                (0)
fe-0/0/3    Down   0               (0)       0                (0)
fe-0/0/4    Down   0               (0)       0                (0)
fe-0/0/5    Down   0               (0)       0                (0)
fe-0/0/6    Down   0               (0)       0                (0)
fe-0/0/7    Down   0               (0)       0                (0)
fxp2        Up     0               (0)       1318             (0)
gre         Up     0               0
ipip        Up     0               0
irb         Up     0               0

Bytes=b, Clear=c, Delta=d, Packets=p, Quit=q or ESC, Rate=r, Up= ^U, Down= ^D
```
Она отличается от `monitor interface <iface>` тем, что показывает **все интерфейсы одновременно** и в компактной таблице.

1. Interface
   Имя интерфейса.
   В списке могут быть:
   • физические порты (ge-, fe-)
   • логические интерфейсы (irb, lo0)
   • туннели (gre, ipip)
   • сервисные порты (sp-)
   • fxp2 — управляющий интерфейс JunOS

2. Link
   Статус физической линии:
   Up — линк поднят
   Down — линка нет
   Это мгновенная диагностика физики.

Например:

```
ge-0/0/1 Down   → кабель не подключён или порт выключен
```

3. Input packets
   Количество входящих пакетов с момента загрузки системы.
   Это глобальный счётчик, не обнуляется сам по себе.

4. (pps) — packets per second
   Это самое важное.
   Показывает **текущую скорость поступления пакетов в интерфейс**, обновляется несколько раз в секунду.

Например:

```
ge-0/0/0: Input 352 (5) 
```

Это значит:
• 352 пакетов пришло за всё время
• 5 пакетов в секунду приходят сейчас

Если ппс резко растёт — устройство под нагрузкой или идёт DDoS/сканирование.

5. Output packets
   То же самое, но для исходящих пакетов.

Если видишь, например:

```
fxp2     Up   0   (0)     1318 (0)
```

Это означает:
• fxp2 передал 1318 пакетов в мир
• и сейчас трафика от него нет
• это нормально — fxp2 это сервисный интерфейс, он почти не принимает трафик

6. Что можно понять из таблицы за секунду:

Смотрим:

ge-0/0/1 Down
Порт физически не подключён. Трафика быть и не должно.

fe-0/0/3 Down
Аналогично.

ge-0/0/0 Up, 5 pps in, 0 pps out
Есть входящий трафик. Скорее всего, кто-то разговаривает с устройством (ARP, управление, LLDP, пинг).

fxp2 Up, много выходящих пакетов
Управляющий интерфейс активно отправляет трафик — может быть SNMP, логирование, SSH.

7. Управляющие клавиши внизу:

Bytes=b
Переключить отображение в байтах, а не пакетах.

Clear=c
Очистить текущие дельты (сбросить pps на ноль).

Delta=d
Показать/скрыть изменения за интервал.

Packets=p
Вернуться к пакетам.

Rate=r
Переключить отображение скоростей (pps/ bps).

Quit=q
Выйти.

Up= ^U / Down= ^D
Скроллинг списка интерфейсов, если их много.

8. Когда эта команда особенно полезна:

Она идеальна в таких случаях:
• найти интерфейс, который генерирует много трафика
• понять, какой порт «раздувает» процессор
• быстро увидеть DDoS или ARP-шторм
• посмотреть активность туннелей (GRE/IPIP)
• понять, какие порты реально используются
• мониторить сеть вживую без графиков и сложных тулов


***Мониторинг трафика***

• Команда monitor traffic предоставляет доступ к утилите tcpdump
– RE отслеживает трафик, который возникает или терминируется на нем
– RE не может отслеживать транзитный трафик, проходящий через PFE

• Опции для фильтрации захватываемого трафика:

```
root@NetEdu> monitor traffic ?
Possible completions:
  <[Enter]>        Execute this command
  detail           Display detailed output
  extensive        Display extensive output
  interface        Name of interface
  layer2-headers   Display link-level header on each dump line
  matching         Expression for headers of receive packets to match
! вывод сокращен
```

• Если не указать интерфейс, то будет отслеживаться трафик интерфейса управления


### Realtime performance monitoring (RPM)

• RPM измеряет качество сервиса между двумя узлами
– Аналог инструмента IP SLA у других вендоров

• Зонды отправляют и принимают тестовый трафик на указанную цель
– Когда цель принимает тестовые запросы, она генерирует ответы, которые принимаются и анализируются зондом

• RPM определяет на основе ответов:
– Качество сервиса по времени отклика, джиттеру, доле потерянных пакетов
– Доступность сервиса


Типы зондов

• Junos OS поддерживает следующие типы зондов:
– ICMP эхо-запрос (по умолчанию)
– ICMP запрос метки времени
– HTTP GET запрос
– HTTP GET запрос для метаданных
– UDP ping
– UDP запрос метки времени
– TCP ping

• Для TCP и UDP зондов требуется сконфигурированный отвечик, который будет генерировать ответы на тестовый трафик


***Настройка RPM***

• RPM настраивается на уровне иерархии [edit services rpm]

```
admin@NetEdu# show services rpm
probe Net_test {
    test t1 {
        target address 8.8.8.8;
        probe-count 10;
        probe-interval 5;
        test-interval 20;
        thresholds {
            successive-loss 10;
            total-loss 4;
        }
        destination-interface fe-0/0/6.0;
        next-hop 192.168.1.1;
    }
    test t2 {
        probe-type http-get;
        target url http://www.juniper.net;
        probe-count 10;
        probe-interval 5;
        test-interval 20;
        source-address 192.168.1.10;
        thresholds {
            successive-loss 10;
            total-loss 4;
        }
    }
}
```


Разберём конфиг RPM по строчкам, чтобы каждая часть стала понятной. Это очень полезно, потому что RPM в JunOS реально мощный инструмент, но выглядит страшновато до тех пор, пока не поймёшь смысл каждого параметра.

**probe Net_test**
Это имя RPM-профиля.
Внутри него могут быть один или несколько тестов.

**test t1 { … }**
Это тест номер один. У него свои параметры, цель, интервалы, пороги.

1. target address 8.8.8.8
   Цель теста.
   У теста t1 это обычный ICMP ping до 8.8.8.8.
   Если не задан probe-type, используется ICMP Echo по умолчанию.

2. probe-count 10
   Количество пакетов в одном цикле теста.
   Иначе: сколько ping’ов отправится подряд.

3. probe-interval 5
   Интервал между отдельными пакетами внутри одного цикла.
   То есть: раз в 5 секунд отправляется один ICMP пакет.

4. test-interval 20
   Интервал между циклами тестов.
   То есть: каждые 20 секунд начинается новый «набор» из 10 пакетов.

Пример:
20 секунд — пауза
10 проб по 5 секунд — идёт цикл
20 секунд — пауза
и так далее

5. thresholds { … }
   Пороги для генерации тревог.

**successive-loss 10**
Если подряд потеряно 10 пакетов — поднимается тревога (цель полностью недоступна).

**total-loss 4**
Если потеряно больше 4 пакетов из одного цикла — качество сервиса считается плохим.
Это позволяет фиксировать деградации, а не только полный даун.

6. destination-interface fe-0/0/6.0
   Интерфейс, от имени которого отправляется тестовый трафик.
   Это важно, если у устройства несколько выходов.

7. next-hop 192.168.1.1
   Принудительный next-hop для ICMP тестов.
   Используется, когда нужно тестировать конкретный маршрут, а не таблицу маршрутизации в целом.

---

***Теперь тест t2***

Этот тест другого типа — HTTP GET.

probe-type http-get
Junos начнёт отправлять HTTP GET запросы.
Это уже не просто ping, а проверка доступности веб-сервиса.

target url [http://www.juniper.net](http://www.juniper.net)
Тестируется HTTP доступность сайта.

probe-count 10
10 HTTP запросов в одном цикле.

probe-interval 5
Промежуток между запросами — 5 секунд.

test-interval 20
Каждые 20 секунд начинается новый набор из 10 HTTP запросов.

source-address 192.168.1.10
Адрес, который будет использоваться как source при отправке HTTP запросов.
Это аналог привязки исходного IP.

thresholds { … }
Те же пороги, что и в первом тесте.

*Если коротко, что делает каждый тест:*

t1 — ICMP ping до Google DNS
Используется для проверки доступности интернета.

t2 — HTTP GET до сайта juniper.net
Проверяет доступность L7 сервиса, время ответа сервера и корректность работы маршрутизации.

***Результаты работы RPM***

• Команда show services rpm probe-results отображает результат работы RPM

```
admin@NetEdu> show services rpm probe-results
Owner: Net_test, Test: t1
Target address: 8.8.8.8, Probe type: icmp-ping
Destination interface name: fe-0/0/6.0
Test size: 10 probes
Probe results:
    Response received, Sun Nov 26 16:47:12 2017, No hardware timestamps
    Rtt: 48748 usec

Results over current test:
    Probes sent: 9, Probes received: 9, Loss percentage: 0
    Measurement: Round trip time
        Samples: 9, Minimum: 47730 usec, Maximum: 51860 usec,
        Average: 49504 usec, Peak to peak: 4130 usec,
        Stddev: 1283 usec, Sum: 445535 usec

Results over last test:
    Probes sent: 10, Probes received: 10, Loss percentage: 0
    Test completed on Sun Nov 26 16:46:12 2017
    Measurement: Round trip time
        Samples: 10, Minimum: 47347 usec, Maximum: 51518 usec,
        Average: 48966 usec, Peak to peak: 4171 usec,
        Stddev: 1242 usec, Sum: 489658 usec

Results over all tests:
    Probes sent: 12269, Probes received: 10191, Loss percentage: 16
    Measurement: Round trip time
        Samples: 10191, Minimum: 46702 usec, Maximum: 193652 usec,
        Average: 49261 usec, Peak to peak: 146950 usec,
        Stddev: 2354 usec, Sum: 502014293 usec
```
Разберём вывод **show services rpm probe-results** максимально подробно и по-человечески. Это один из тех выводов, которые выглядят страшновато, пока не понимаешь логику — а потом всё становится очень простым и полезным.

Вот ключевая структура вывода:
```
Owner: Net_test, Test: t1
Target address: 8.8.8.8
Probe type: icmp-ping
Destination interface: fe-0/0/6.0
Test size: 10 probes
```

Это просто краткое описание теста: куда пингуем, чем, и сколько пакетов в цикле.

---

**Блок 1: Probe results**

Это результат **последнего полученного ответа**, буквально последнего пинга.

```
Response received
Rtt: 48748 usec
```

Расшифровка:
• RTT — время от отправки до получения ответа (round-trip time).
• 48748 usec — это ~48.7 миллисекунд.

Это единичный результат, по сути "последний пинг".

---

**Блок 2: Results over current test**

Это статистика для **текущего набора probe-count**, то есть текущего цикла из 10 пингов.

```
Probes sent: 9, received: 9, Loss: 0%
```

Значит:
• отправили пока 9 пакетиков (цикл ещё не закончился)
• получили все 9
• потерь нет

Дальше идут метрики качества:

Samples: 9
Минимальный RTT: 47730 usec
Максимальный RTT: 51860 usec
Средний RTT: 49504 usec
Peak to peak: 4130 usec
Stddev: 1283 usec

Расшифровываю:

Minimum
Самый быстрый пакет.

Maximum
Самый медленный пакет.

Average
Средняя задержка, полезно для оценки стабильности.

Peak to peak
Разница между max и min.
Чем больше — тем нестабильнее канал.

Stddev (standard deviation)
Стандартное отклонение.
Это и есть тот самый джиттер.
1283 usec = 1.2 ms — хороший показатель.

Sum
Сумма RTT всех пакетов. Используется внутренними механизмами.

---

***Блок 3: Results over last test***

Это статистика **предыдущего завершённого** цикла "probe-count 10".
```
Probes sent: 10, received: 10, Loss: 0%
```
Вывод:
в предыдущем тесте тоже всё было ок.

Метрики аналогичны — min, max, average, stddev.

---

***Блок 4: Results over all tests***

Это самое интересное.
Здесь показываются **все тесты от момента запуска RPM**, а не только последний.

```
Probes sent: 12269
Probes received: 10191
Loss percentage: 16
```
Вот где многие путаются.
Хотя **последние тесты показывают 0% потерь**, суммарно потери 16%.
Что это значит?
Раньше была проблема с сетью
• когда-то в истории RPM терялось много пакетов
• но сейчас ситуация стабильная

Эта статистика полезна, чтобы понимать, как долго сохранялась нестабильность, особенно при работе с event-policy.

Дальше метрики:

Minimum: 46702 usec
Maximum: 193652 usec (!)
Average: 49261 usec
Peak-to-peak: 146950 usec (!!)
Stddev: 2354 usec

Тут видно:

• когда-то были жёсткие всплески задержки (до 193 ms)
• jitter был большим
• сервис деградировал

Но сейчас всё ок — текущие тесты показывают стабильные ~48–50 ms.


### Версия Junos OS

• Команда show version отображает текущую версию Junos OS

```
admin@NetEdu> show version
Hostname: NetEdu
Model: srx210he
JUNOS Software Release [12.1X46-D40.2]
```

• Опция detail отображает список пакетов ПО

• Некоторые типы пакетов ПО:
– jkernel*: ядро и пакет сетевых инструментов (основные файлы ОС)
– jroute*: пакет движка маршрутизации (ПО RE)
– jpfe*: пакет движка пересылки пакетов (ПО PFE)
– jdocs*: пакет документации (набор документации для ПО)
– jcrypto*: пакет шифрования (ПО безопасности)
– jweb*: пакет J-Web (ПО графического интерфейса пользователя)

**Формат имени пакета установки**

• Пакеты установки именуются по формату package-release-edition:

– Package: описание содержимого ПО
• jinstall — для M, MX, T, TX серий
• jinstall-ex — для EX-серий
• junos-srxsme — для SRX300, SRX320, SRX340, SRX345 и SRX550M и т. д.

– Release: описывает релиз Junos OS и содержит несколько компонентов:
• основной и второстепенный номер релиза
• тип релиза (R — released, B — beta, I — internal)
• номер сборки и спиновый номер

– Edition: domestic или export
• Domestic содержит ПО jcrypto, которое позволяет использовать функции шифрования
• Export не содержит ПО jcrypto, возможности шифрования не могут быть использованы

Внизу пример:
jinstall-ex-2200-15.1R6.7-domestic-signed
Package | Release | Edition


### Установка Junos OS

• Образцы Junos OS скачиваются с официального сайта Juniper
• Команда request system software add <path/image-name> позволяет обновить версию Junos OS
– Можно указать локальный путь и имя файла или FTP/SCP URI, который содержит образ для загрузки и установки
• Для активации нового ПО необходимо перезагрузить устройство

Пример:

admin@NetEdu> request system software add /var/tmp/junos-srxsme-12.1X46-D40.2-domestic.tgz
NOTICE: Validating configuration against junos-srxsme-12.1X46-D40.2-domestic.tgz.
NOTICE: Use the 'no-validate' option to skip this if desired.
Formatting alternate root (/dev/da0s1a)...
/dev/da0s1a: 296.9MB (607996 sectors) block size 16384, fragment size 2048
using 4 cylinder groups of 74.22MB, 4750 blks, 9600 inodes.
super-block backups (for fsck -b #) at:
32, 152032, 304032, 456032
saving package file in /var/sw/pkg ...
! вывод сокращен

### Перезагрузка и выключение устройства

• Команда request system power-off отключит устройство
• Команда request system reboot перезагружает устройство

• Для этих команд доступны опции:
– at: перезагрузка устройства в указанное время
– in: перезагрузка устройства через указанное количество минут

• Некоторые устройства оборудованы кнопкой питания

### Восстановление пароля root

• Для восстановления пароля root требуется консольное подключение
• Шаги для восстановления пароля:
– Перезагрузите систему
• Во время загрузки системы нажмите пробел, когда будет предложено
• Введите boot -s для загрузки в однопользовательский режим

– В процессе загрузки в однопользовательском режиме Junos предложит запустить сценарий восстановления, ввести путь к оболочке или нажать Enter для оболочки по умолчанию
• Введите recovery на этом этапе
– CLI загрузится и в конфигурационном режиме можно поменять пароль root
– Сохраните изменения и перезагрузите устройство.
• Функция отключается настройкой set system ports console insecure

---




