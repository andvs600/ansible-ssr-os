# Установка агента СРК в ОС CentOS/RHEL с помощью Ansible

??? abstract
    1. [Общее описание](#общее-описание)
    2. [Параметры](#параметры)
    3. [Теги](#теги)
    4. [Примеры](#примеры)
    5. [Дополнительные материалы](#дополнительные-материалы)

## Общее описание
Роль для установки и настройки агента NetBackup на сервера с **``Red Hat EL 7/8``** и **``CentOS 7/8``**.

!!! question
    Для получения списка серверов необходимо сделать обращение в ЕСПП группы *ЦКИТ-<Зона_ответственности>*.

## Параметры
|Название переменной  | Тип переменной | Значения по умолчанию | Описание                                     |
|:--------------------|:--------------:|:---------------------:|:---------------------------------------------|
|netbackup_master     | string         | undef                 | Указываются имена для мастер серверов.       |
|netbackup_media      | array          | undef                 | Указываются имена для медиа серверов.        |

## Теги
|Тег                  | Описание                                          |
|:--------------------|:--------------------------------------------------|
|nb_install           | Устанавливает агент.                              |
|nb_cleanup           | Удаляет временные файлы.                          |

## Примеры

!!! example "inventory/hosts"
    ```
    [example-servers]
    <host_name> ansible_ssh_host=<host_ip> ansible_ssh_user=<user_name_for_connect>

    [example-servers:vars]
    ansible_connection=ssh
    netbackup_master='gvc-nbcmst.gvc.oao.rzd'
    netbackup_media=['gvc-nbmedia-01.gvc.oao.rzd','gvc-nbmedia-02.gvc.oao.rzd']
    ```

## Дополнительные материалы

- [Veritas NetBackup](https://www.backupsolution.ru/downloads/admin-guide-netbackup.pdf)
