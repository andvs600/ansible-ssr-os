# SELinux

Copyright (C) 2020 Dmitriy Prigoda deamon.none@gmail.com This script is free software: Everyone is permitted to copy and distribute verbatim copies of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License.

## Содержание

1. [Общее описание](#общее-описание)
2. [Параметры](#параметры)
3. [Теги](#теги)
4. [Примеры](#примеры)
5. [Дополнительные материалы](#дополнительные-материалы)

## Общее описание
[Security-Enhanced Linux](https://ru.wikipedia.org/wiki/SELinux) - реализация системы принудительного контроля доступа, которая может работать параллельно с классической избирательной системой контроля доступа. Роль устанавливает все необходимые пакеты для управления и настраивает режимы работы SELINUX.

## Параметры
|Название переменной  | Тип переменной | Значения по умолчанию | Описание                                     |
|:--------------------|:--------------:|:---------------------:|:---------------------------------------------|
|selinux_policy       | string         | targeted              | Устанавливает политику.                      |
|selinux_state        | string         | permissive            | Устанавливает режим.                         |

!!! info     
    Режимы:
    **Enforcing:** Режим по-умолчанию. При выборе этого режима все действия, которые каким-то образом нарушают текущую политику безопасности, будут блокироваться, а попытка нарушения будет зафиксирована в журнале.
    **Permissive:** В случае использования этого режима, информация о всех действиях, которые нарушают текущую политику безопасности, будут зафиксированы в журнале, но сами действия не будут заблокированы.
    **Disabled:** Полное отключение системы принудительного контроля доступа.

!!! important
    В процессе адаптации и внедрения, в опытной эксплуатации рекомендуется использовать режим: *Permissive*. В промышленной эксплуатации необходимо настроить режим: *Enforcing*. 

## Теги
|Тег                  | Описание                                          |
|:--------------------|:--------------------------------------------------|
|selinux              | Запускает все задачи по настройке SELINUX.        |

## Примеры

### inventory/hosts

```
    [example-servers]
    <host_name> ansible_ssh_host=<host_ip> ansible_ssh_user=<user_name_for_connect>

    [example-servers:vars]
    ansible_connection=ssh
    selinux_state='enforcing'
    selinux_policy='targeted'
```

## Дополнительные материалы

- [SELinux](http://selinuxproject.org/page/Main_Page)
- [SELinux – описание и особенности работы](https://habr.com/ru/company/kingservers/blog/209644/)