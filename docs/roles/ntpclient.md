# NTP Client

??? abstract
    1. [Общее описание](#общее-описание)
    2. [Параметры](#параметры)
    3. [Теги](#теги)
    4. [Примеры](#примеры)
    5. [Дополнительные материалы](#дополнительные-материалы)

## Общее описание
Network Time Protocol (NTP) — протокол сетевого времени. Роль настраивает клиент NTP прописывая сервера точного времени и настраивает временную зону на целевом узле. 

!!! note
    По умолчанию используется пул `ru.pool.ntp.org` и сервера `0.ru.pool.ntp.org, 1.ru.pool.ntp.org, 2.ru.pool.ntp.org, 3.ru.pool.ntp.org` российского Интернет сегмента.

## Параметры
|Название переменной               | Тип переменной | Значения по умолчанию | Описание                                                                                 |
|:---------------------------------|:--------------:|:---------------------:|:-----------------------------------------------------------------------------------------|
|ntp_pool                          | string         | undef                 | Задает пул серверов точного времени (нужен только для синхронизации времени из Интернет).| 
|ntp_servers                       | array          | undef                 | Задает список серверов точного времени, перечисление через запятую.                      |
|chrony_config_extra_options       | array          | undef                 | Дополнительные опции указываются как ключ значение.                                      |
|t_zone                            | string         | 'Europe/Moscow'       | Задает временную зону.                                                                   |

!!! attention
    Переменная `ntp_pool` применяется только совместно с переменной `ntp_servers`.

## Теги
|Тег                  | Описание                                          |
|:--------------------|:--------------------------------------------------|
|set_tz               | Устанавливает часовой пояс.                       |
|disable_ntpd         | Отключает устаревший демон синхронизации.         |
|enable_chronyd       | Вклучает и настраивает синхронизацию времени.     |

## Примеры

!!! example "inventory/hosts"
    ```
    [example-servers]
    <host_name> ansible_ssh_host=<host_ip> ansible_ssh_user=<user_name_for_connect>

    [example-servers:vars]
    ansible_connection=ssh
    ntp_servers=['1.2.3.4','4.3.2.1']
    ```

## Дополнительные материалы

- [NTP](https://ru.wikipedia.org/wiki/NTP)
- [For RHEL 7](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd)
- [For RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp)