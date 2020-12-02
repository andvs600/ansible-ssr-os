# Установка агента мониторинга ITM в ОС CentOS/RHEL с помощью Ansible

??? abstract
    1. [Общее описание](#общее-описание)
    2. [Параметры](#параметры)
    3. [Теги](#теги)
    4. [Примеры](#примеры)
    5. [Дополнительные материалы](#дополнительные-материалы)

## Общее описание
Роль для установки и настройки агента мониторинга ОС Linux (IBM Tivoli Monitoring) на сервера с **``Red Hat EL 7/8``** и **``CentOS 7/8``**.

Список серверов TEMS для подключения агента:

|Регион                           | Сервера                                                    |
|:--------------------------------|:-----------------------------------------------------------|
|ГВЦ Собственно	для Linux серверо | gvc-rtems-01.gvc.oao.rzd                                   |
|Москва                           | rtems-x86-msk.msk.oao.rzd                                  |
|Санкт-Петербург – ДЦУП           | orw-itmcup-01.orw.oao.rzd                                  |
|Санкт-Петербург – ПТК            | orw-itmapp-01.orw.oao.rzd                                  |
|Санкт-Петербург – СТО            | orw-itmsto-01.orw.oao.rzd                                  |
|Санкт-Петербург - ERP            | orw-itmsap-01.orw.oao.rzd                                  |
|Калининград                      | orw-itmklg-01.orw.oao.rzd                                  |
|Нижний Новгород                  | orw-itmnnw-01.orw.oao.rzd                                  |
|Самара                           | orw-itmsam-01.orw.oao.rzd                                  |
|Ярославль                        | orw-itmyar-01.orw.oao.rzd                                  |
|Ростов                           | msk-rtems-ini-01.msk.oao.rzd, msk-rtems-ini-02.msk.oao.rzd |
|Саратов                          | msk-rtems-ini-01.msk.oao.rzd, msk-rtems-ini-02.msk.oao.rzd |
|Воронеж                          | msk-rtems-ini-01.msk.oao.rzd, msk-rtems-ini-02.msk.oao.rzd |
|Екатеринбург                     | svrw-tivoli-01.svrw.oao.rzd, svrw-tivoli-02.svrw.oao.rzd   |
|Иркутск                          | svrw-tivoli-06.svrw.oao.rzd, svrw-tivoli-07.svrw.oao.rzd   |
|Красноярск                       | svrw-tivoli-06.svrw.oao.rzd, svrw-tivoli-07.svrw.oao.rzd   |
|Хабаровск                        | svrw-tivoli-06.svrw.oao.rzd, svrw-tivoli-07.svrw.oao.rzd   |
|Новосибирск                      | svrw-tivoli-06.svrw.oao.rzd, svrw-tivoli-07.svrw.oao.rzd   |
|Челябинск                        | svrw-tivoli-06.svrw.oao.rzd, svrw-tivoli-07.svrw.oao.rzd   |
|Чита                             | svrw-tivoli-06.svrw.oao.rzd, svrw-tivoli-07.svrw.oao.rzd   |
|Серверы СДО всех дорог           | svrw-tivoli-06.svrw.oao.rzd, svrw-tivoli-07.svrw.oao.rzd   |

!!! question
    Для получения консультации и дополнительных инструкций, а также списка серверов можно обратится в ЦК СП.

## Параметры
|Название переменной  | Тип переменной | Значения по умолчанию | Описание                                     |
|:--------------------|:--------------:|:---------------------:|:---------------------------------------------|
|srv_mon              | array          | undef                 | Указываются сервера TEMS для подключения.    |

## Теги
|Тег                  | Описание                                          |
|:--------------------|:--------------------------------------------------|
|itm_install          | Устанавливает агент.                              |
|itm_service          | Создает юнит для запуска и остановки агента.      |
|itm_cleanup          | Удаляет временные файлы.                          |

## Примеры

!!! example "inventory/hosts"
    ``` ini
    [example-servers]
    <host_name> ansible_ssh_host=<host_ip> ansible_ssh_user=<user_name_for_connect>

    [example-servers:vars]
    ansible_connection=ssh
    srv_mon=['gvc-rtems-01.gvc.oao.rzd']
    ```

## Дополнительные материалы

- [IBM Tivoli Monitoring V6.3 documentation](https://www.ibm.com/support/knowledgecenter/en/SSTFXA_6.3.0/com.ibm.itm.doc_6.3/welcome_63.htm)
- [AIX Portal](https://aixportal.ru/)
