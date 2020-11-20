# NetFillter

??? abstract
    1. [Общее описание](#общее-описание)
    2. [Параметры](#параметры)
    3. [Теги](#теги)
    4. [Примеры](#примеры)
    5. [Дополнительные материалы](#дополнительные-материалы)

## Общее описание
Роль для настройки [NetFillter](https://ru.wikipedia.org/wiki/Netfilter#:~:text=netfilter%20%E2%80%94%20%D0%BC%D0%B5%D0%B6%D1%81%D0%B5%D1%82%D0%B5%D0%B2%D0%BE%D0%B9%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%20(%D0%B1%D1%80%D0%B0%D0%BD%D0%B4%D0%BC%D0%B0%D1%83%D1%8D%D1%80),%D1%8F%D0%B4%D1%80%D0%BE%20Linux%20%D1%81%20%D0%B2%D0%B5%D1%80%D1%81%D0%B8%D0%B8%202.4.) с расширенными правилами под высокую нагрузку. Базовые настройки доработаны на основе статьи [DDoS Protection With IPtables](https://javapipe.com/blog/iptables-ddos-protection/). 

## Параметры
|Название переменной    | Тип переменной | Значения по умолчанию | Описание                                                                                 |
|:----------------------|:--------------:|:---------------------:|:-----------------------------------------------------------------------------------------|
|activate_firewall      | boolean        | undef (false)         | Активирует брандмэуэр.                                                                   |
|firewalld_not_disabled | boolean        | undef (false)         | Оставляет брандмэуэр по умолчанию (firewalld), дальнейшая настройка не производится.     |
|nf_ports               | array          | undef                 | Для открытия отдельного входящего порта.                                                 |
|nf_custom_ruleset      | array          | undef                 | Для добавления своих правил.                                                             |

!!! failure
    На хостах у установренным Docker'ом необходимо настравить правила в ручную с использованием `firewalld`.

!!! todo
    Дорабатывается поддержка расширинных правил для LXC.

## Теги
|Тег                  | Описание                                          |
|:--------------------|:--------------------------------------------------|
|iptables             | Выполняет сценарии для настройки `iptables`.      |
|nftables             | Выполняет сценарии для настройки `nftables`.      |

## Примеры

!!! example "inventory/hosts"
    ```
    [example-servers]
    <host_name> ansible_ssh_host=<host_ip> ansible_ssh_user=<user_name_for_connect>

    [example-servers:vars]
    ansible_connection=ssh
    min_audit=true
    # Регистрация только тех событий, при которых файл открывается только на запись и изменение атрибутов в директории '/etc':
    auditd_custom_rules=['-a exit,always -S open -F path=/etc/ -F perm=aw']
    ```

## Дополнительные материалы
