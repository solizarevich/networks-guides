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


