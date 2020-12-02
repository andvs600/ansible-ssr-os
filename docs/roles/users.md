# Создание локальных учетных записей в ОС CentOS/RHEL с помощью Ansible

??? abstract
    1. [Общее описание](#общее-описание)
    2. [Параметры](#параметры)
    3. [Теги](#теги)
    4. [Примеры](#примеры)
    5. [Дополнительные материалы](#дополнительные-материалы)

## Общее описание
Роль для создания типовых учетных записей согласно типовым требования: root, adminos, aminptk, adminvp0, adminvp1, adminvp2, smenaptk. Актуализация аутентификационных данных и генерация правил sudoers.

!!! help
    Задачи сгруппированы в блоки управления учетными записями (УЗ), для *root, adminos, adminptk* задачи выполняются отдельными блоками, для *adminvp[0,1,2], smenaptk* объединены в один блок. Выполнить блок можно указав тег или задав специальную переменную.

!!! attention
    Необходимо явно активировать выполнение блока.

!!! info
    По умолчанию SSH ключи пароли уже заданы и передается как шифрованный секрет. Переопределяются с помощью переменных.

## Параметры
|Название переменной  | Тип переменной | Значения по умолчанию | Описание                                                                                      |
|:--------------------|:--------------:|:---------------------:|:----------------------------------------------------------------------------------------------|
|root_mgmt            | boolean        | undef (false)         | Включает управление УЗ *root'a*.                                                              |
|root_pass            | string         | def in var vauit      | Устанавливает пароль *root'у*, зависит от переменной. Обновится , если они отличаются.        |
|adminos_mgmt         | boolean        | undef (false)         | Включает управление УЗ *adminos*.                                                             |
|adminos_pass         | string         | def in var vauit      | Устанавливает пароль *adminos*, зависит от переменной. Обновится , если они отличаются.       |
|adminos_key          | string         | def in var vauit      | Создает ключ авторизации *adminos*, зависит от переменной. Обновится , если они отличаются.   |
|adminptk_mgmt        | boolean        | undef (false)         | Включает управление УЗ *adminptk*.                                                            |
|adminptk_pass        | string         | def in var vauit      | Устанавливает пароль *adminptk*, зависит от переменной. Обновится , если они отличаются.      |
|adminptk_key         | string         | undef (false)         | Создает ключ авторизации *adminptk*, зависит от переменной. Обновится , если они отличаются.  |
|adminptk_tmux        | boolean        | undef (false)         | Активирует Tmux для УЗ *adminptk*.                                                            |
|adminvp_smena_mgmt   | boolean        | undef                 | Включает управление УЗ *adminvp0, adminvp1, adminvp2, smenaptk*.                              |
|smenaptk_pass        | string         | def in var vauit      | Устанавливает пароль *smenaptk*, зависит от переменной. Обновится , если они отличаются.      |
|smenaptk_key         | string         | def in var vauit      | Создает ключ авторизации *smenaptk*, зависит от переменной. Обновится , если они отличаются.  |
|smenaptk_tmux        | boolean        | undef                 | Активирует Tmux для УЗ *smenaptk*.                                                            |
|adminvp0_pass        | string         | def in var vauit      | Устанавливает пароль *adminvp0*, зависит от переменной. Обновится , если они отличаются.      |
|lock_pass            | boolean        | undef (false)         | Блокирует или разблокирует УЗ *adminvp0*.                                                     |
|adminvp_pass         | string         | def in var vauit      | Устанавливает пароль *adminvp[1,2]*, не обновляется при изменение переменной.                 |
|sudo_services_drive  | array          | undef                 | Список сервисов (служб) которыми можно управлять.                                             |
|sudo_services_boot   | array          | undef                 | Список сервисов (служб) которые можно автоматически запускать.                                |
|srules_custom        | string         | undef                 | Дополнительные правила.                                                                       |

!!! attention
    Пароли шифруются хеш функцией sha-512, в переменной указывается ее значение. Подробно описано здесь: [how-do-i-generate-encrypted-passwords-for-the-user-module](https://docs.ansible.com/ansible/latest/reference_appendices/faq.html#how-do-i-generate-encrypted-passwords-for-the-user-modul)

## Теги
|Тег                  | Описание                                          |
|:--------------------|:--------------------------------------------------|
|root_mgmt            | Запускае задачи для УЗ *root*.                    |
|adminos_mgmt         | Запускае задачи для УЗ *adminos*.                 |
|vimrc_conf           | Создает конфиг для Vim УЗ *adminos*.              |
|tmux_conf            | Создает конфиг для Tmux УЗ *adminos*.             |
|tmux_adminptk        | Создает конфиг для Tmux УЗ *adminptk*.            |
|tmux_smenaptk        | Создает конфиг для Tmux УЗ *smenaptk*.            |
|adminptk_mgmt        | Запускае задачи для УЗ *adminptk*.                |
|smena_mgmt           | Запускае задачи для УЗ *smenaptk*.                |
|adminvp0_mgmt        | Запускае задачи для УЗ *adminvp0*.                |
|adminvp_mgmt         | Запускае задачи для УЗ *adminvp[1,2]*.            |
|sudoers_mgmt         | Обновляет правила `sudoers`.                      |

## Примеры

!!! example "inventory/hosts"
    ``` ini
    [example-servers]
    <host_name> ansible_ssh_host=<host_ip> ansible_ssh_user=<user_name_for_connect>

    [example-servers:vars]
    ansible_connection=ssh
    adminvp_smena_mgmt=true
    smenaptk_pass='$6$oGnmjGVGD.../6WKDvcGqAIq...cmxSsyzLozOYo0'
    smenaptk_tmux=true
    sudo_services_drive=['httpd']
    ```

!!! example "Пример генирации хеша для пароля по словарю"
    ```bash
    python -c 'import crypt; print(crypt.crypt("My Password"))'
    ```

!!! example "Пример генирации хеша для пароля с интерактивным водом"
    ```bash
    python -c 'import crypt,getpass;pw=getpass.getpass();print(crypt.crypt(pw) if (pw==getpass.getpass("Confirm: ")) else exit())' 
    ```

??? example "Пример шаблона sudoers"
    ```
    #{{ ansible_managed }}
    Host_Alias SERVERS = 10.0.0.0/8
    Cmnd_Alias AGENTS = /usr/openv/netbackup/bin/bp.start_all, /opt/IBM/ITM/bin/itmcmd agent stop *, /opt/IBM/ITM/bin/itmcmd agent start *, /usr/bin/kesl-control --start-task 6, /usr/bin/kesl-control --app-info, /usr/bin/systemctl start klnagent64, /usr/bin/systemctl restart klnagent64, /usr/bin/systemctl stop klnagent64, /usr/bin/systemctl start kesl-supervisor, /usr/bin/systemctl restart kesl-supervisor, /usr/bin/systemctl stop kesl-supervisor
    Cmnd_Alias PROCESSES = /usr/bin/ps -[aefuxw]*, /bin/nice -n [-0-5] *, /bin/kill -s (TERM|KILL) [0-9]*, /usr/bin/kill -s (TERM|KILL) [0-9]*, /usr/bin/killall
    Cmnd_Alias NETWORKING = /sbin/route -n, /sbin/ifconfig [-a-z0-9]*, /bin/ping *, /usr/bin/host -(a|t) *, /usr/bin/nmtui, /sbin/iptables -[vnL]*, /sbin/iptables-save *, /usr/sbin/nft list ruleset, /usr/sbin/nft -s list ruleset *
    Cmnd_Alias SERVICES = /usr/bin/systemctl status *{% if sudo_services_drive is defined %}{%   for units in sudo_services_drive %}, /usr/bin/systemctl start {{ units }}, /usr/bin/systemctl stop {{ units }}, /usr/bin/systemctl restart {{ units }}{%   endfor %}{% endif %}{% if sudo_services_boot is defined %}{%   for units in sudo_services_boot %}, /usr/bin/systemctl enable {{ units }}, /usr/bin/systemctl disable {{ units }}{%   endfor %}{% endif %}, /usr/bin/systemctl reload
    Cmnd_Alias SOFTWARE = /usr/bin/yum, /usr/bin/dnf, /usr/bin/systemctl
    Cmnd_Alias SMENA =  /sbin/reboot, /sbin/shutdown -P +[0-9]+ "*", ! /usr/bin/bash
    Cmnd_Alias NROOT = /usr/bin/su [!-]*, ! /usr/bin/su *root*
    Cmnd_Alias DEBUG = /usr/bin/nmap, /usr/sbin/tcpdump
    Cmnd_Alias FILES = /usr/bin/ls, /usr/bin/cat, /usr/bin/grep, /usr/bin/egrep, /usr/bin/stat, /usr/sbin/lsof, /usr/bin/getfacl, /usr/bin/lsattr, /usr/bin/find, !/usr/bin/find *-exec*, !/usr/bin/find *-fprint*, !/usr/bin/find *-ok*
    Cmnd_Alias BIN = /usr/sbin/sosreport, /usr/bin/yum update, /usr/bin/dnf upgrade, ! /usr/bin/bash
    {% if srules_custom is defined %}{%   for rules in srules_custom %}Cmnd_Alias SRULES = {{ rules }}{%   endfor %}{% endif %}
    smenaptk  SERVERS = NOPASSWD: AGENTS, PROCESSES, NETWORKING, FILES, SERVICES, SMENA, NROOT, BIN{% if srules_custom is defined %}, SRULES{% endif %}
    adminvp0  ALL=(ALL)   NOPASSWD: PROCESSES, NETWORKING, FILES, SOFTWARE, DEBUG, !SERVICES
    adminvp1  ALL=(ALL)   NOPASSWD: PROCESSES, NETWORKING, FILES, SERVICES, DEBUG, NROOT, BIN
    adminvp2  ALL=(ALL)   NOPASSWD: PROCESSES, NETWORKING, FILES, SERVICES, DEBUG, NROOT, BIN
    ```

## Дополнительные материалы

- [SSH Public Key Authentication](https://www.theurbanpenguin.com/ssh-public-key-authentication/)
- [How to enable sudo](https://developers.redhat.com/blog/2018/08/15/how-to-enable-sudo-on-rhel/)
