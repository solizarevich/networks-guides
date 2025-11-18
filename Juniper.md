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



