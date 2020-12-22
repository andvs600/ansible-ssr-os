# Kaspersky_role
 
Copyright (C) 2020 Nikita Ilin ilnik96@gmail.com

## Содержание

1. [Общее описание](#общее-описание)
2. [Параметры](#параметры)
3. [Примеры](#примеры)
4. [Дополнительные материалы](#дополнительные-материалы)

## Общее описание

Kaspersky Endpoint Security для Linux осуществляет защиту компьютеров под управлением операционной системы Linux от вредоносных программ. Угрозы могут проникать в систему через сетевые каналы передачи данных или со съемных дисков.

Данное решение представляет сценарий `Ansible` для автоматической установки и настройки ПО `Kaspersky network agent` и `Kaspersky Endpoint Security для Linux` на хостах под управлением ОС CentOS 7 или 8 с использованием `RPM` пакетов. Помимо этого данное решение может использоваться для массового управления (включение или отключение) агентами на уже сконфигурированных хостах, а так же для их удаления с серверов.

Устанавливаемые компоненты:

1. Kaspersky network agent for Linux
2. Kaspersky Endpoint Security for Linux

Для установки необходимо сделать клон проекта, настроить параметры в файле `inventory`, запустить `playbook`

```
    # Склонировать проект
    > git clone https://github.com/NikSonn/Kaspersky_role.git
    # Перейти в директорию с проектом
    > cd Kaspersky_role
    # Создать файл с параметрами (В качестве примера представлен файл hosts.example)
    > vim inventory/hosts
    # Запустить playbook
    > ansible-playbook -i inventory/hosts playbook.yml --ask-pass --become --ask-become-pass
```

## Параметры

|Название переменной   | Тип переменной | Значения по умолчанию  | Описание                                                    |
|:--------------------:|:--------------:|:----------------------:|:------------------------------------------------------------|
|ksp_server            | string         | gvc_kav_03.gvc.oao.rzd | FQDN управляющего сервера Kaspersky                         |
|uninstall_only        | boolean        | undef (false)*         | Только удаление ПО Kaspersy с серверов                      |
|control               | boolean        | undef (false)*         | Флаг управления ПО (задание состояния после установки ПО)   |
|control_only          | boolean        | undef (false)*         | Флаг управления ПО (без инсталляции ПО, только управление)  |
|all_up_enabled        | boolean        | undef (true)*          | Агент и KESL включены и добавлены в автозагрузку            |
|agent_up_enabled      | boolean        | undef (false)*         | Только Агент включен и добавлен в автозагрузку (не KESL)    |
|kesl_stop             | boolean        | undef (false)*         | KESL остановлен, но добавлен в автозагрузку (Агент включен) |
|all_stop              | boolean        | undef (false)*         | Агент и KESL остановлены, но добавлены в автозагрузку       |

*Если boolean переменная не определена (значение по умолчанию указано как `undef`), то в скобках `()` указано её поведение по-умолчанию. Для изменения стандартного поведения необходимо инициализировать переменную в инвентори точным значением `true` или `false`.  

## Примеры

### inventory/hosts

```
    [example-servers]
    <host_name> ansible_ssh_host=<host_ip> ansible_ssh_user=<user_name_for_connect>

    [example-servers:vars]
    ansible_connection=ssh
    ksp_server='<your_kaspersky_master_server>'
    control=true
    all_stop=true
    ##установка ПО Kaspersky и его выключение до перезагрузки сервера (или до принудительного запуска)
```

## Дополнительные материалы

- [Ansible installation guide](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
- [Ansible Documentations](https://docs.ansible.com/)
- [Ansible tags](https://docs.ansible.com/ansible/latest/user_guide/playbooks_tags.html)
- [Connection methods](https://docs.ansible.com/ansible/latest/user_guide/connection_details.html)
- [Ansible passing sudo](https://8gwifi.org/docs/ansible-sudo-ssh-password.jsp)
- [Kaspersky Endpoint Security for Linux](https://support.kaspersky.com/KES4Linux/11.1.0/ru-RU/196581.htm)
- [MkDocs](https://www.mkdocs.org/)
- [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/getting-started/)
